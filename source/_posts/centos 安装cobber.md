---
title: centos7安装cobbler
date: 2017-07-16 22:56:00
categories: linux
tags: [centos,linux,cobbler]

---

# 一、调整基本配置
## 1.配置selinux
   关闭selinux或参照cobbler官方文档设置selinux，http://cobbler.github.io/manuals/2.6.0/4/2_-_SELinux.html
```
 sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config
```
   配置完成后要重启服务器。
## 2.关闭iptables以及取消自启动。minimal未安装，不需要配置。
## 3.关闭iptables以及取消自启动，或者放行80 67 68 69 443端口。
- 80 443是cobbler web管理界面端口
- 67 68 是DHCP端口
- 69是TFTP端口
```
systemctl stop firewalld
systemctl disable firewalld
```

# 二、安装软件包
## 1.安装epel包
```
yum install epel-release
yum makecache
```
## 2.安装软件包
```
yum install cobbler cobbler-web xinetd pykickstart fence-agents dhcp -y
```
- 其中依赖的tftp-server、bind、httpd、rsync、python-ctypes会依赖安装。
- 如果需要使用deb源，可以安装`yum install debmirror`

# 三、配置cobbler
## 1.配置Cobbler主配置文件，/etc/cobbler/settings
```
cp -p /etc/cobbler/settings /etc/cobbler/settings.bak
sed -i 's/manage_dhcp: 0/manage_dhcp: 1/g' /etc/cobbler/settings  
sed -i 's/manage_rsync: 0/manage_rsync: 1/g' /etc/cobbler/settings
sed -i 's/server: 127.0.0.1/server: {服务器ip}/g' /etc/cobbler/settings
sed -i 's/pxe_just_once: 0/pxe_just_once: 1/g' /etc/cobbler/settings（避免重复安装）
sed -i 's/next_server: 127.0.0.1/next_server: {tftp服务器ip}/g' /etc/cobbler/settings
```

## 2.配置cobbler-web
```
    cp -p /etc/cobbler/modules.conf /etc/cobbler/modules.conf.bak
    sed -i 's/authn_denyall/authn_configfile/g' /etc/cobbler/modules.conf
```
给web页面登录添加cobbler用户，并配置密码
```
htdigest /etc/cobbler/users.digest "Cobbler" cobbler
```
## 3.配置安装系统root默认密码
```
openssl passwd -1 -salt "cobbler" "{password}"
```
修改`/etc/cobbler/setting`中`default_password_crypted`字段为生成密码。

# 四、配置tftp、rsync服务
- 修改/etc/xinetd.d/tftp中`disable`字段为`no`。
- CentOS7上安装cobbler对于rsync无需额外配置。

# 五、配置dhcp服务
```
subnet *192.168.11.0* netmask 255.255.255.0

option routers *192.168.11.252*;

option domain-name-servers *192.168.11.252*;

option subnet-mask 255.255.255.0;

range dynamic-bootp*192.168.11.100 192.168.11.200*;
```
- 斜体ip地址根据需要配置

# 六、启动服务，并配置服务自启动
## 1.配置服务自启动
```
systemctl enable httpd
systemctl enable dhcpd  
systemctl enable cobblerd
systemctl enable rsyncd
```
## 2.加载配置文件生效
```
cobbler sync
```
## 3.检查cobbler配置
```
cobbler check
```

----------

The following are potential configuration items that you may want to fix:
1 : The 'server' field in /etc/cobbler/settings must be set to something other than localhost, or kickstarting features will not work. This should be a resolvable hostname or IP for the boot server as reachable by all machines that will use it.
2 : For PXE to be functional, the 'next_server' field in /etc/cobbler/settings must be set to something other than 127.0.0.1, and should match the IP of the boot server on the PXE network.
3 : change 'disable' to 'no' in /etc/xinetd.d/tftp
4 : some network boot-loaders are missing from /var/lib/cobbler/loaders, you may run 'cobbler get-loaders' to download them, or, if you only want to handle x86/x86_64 netbooting, you may ensure that you have installed a *recent* version of the syslinux package installed and can ignore this message entirely. Files in this directory, should you want to support all architectures, should include pxelinux.0, menu.c32, elilo.efi, and yaboot. The 'cobbler get-loaders' command is the easiest way to resolve these requirements.
5 : file /etc/xinetd.d/rsync does not exist
6 : debmirror package is not installed, it will be required to manage debian deployments and repositories
7 : The default password used by the sample templates for newly installed machines (default_password_crypted in /etc/cobbler/settings) is still set to 'cobbler' and should be changed, try: "openssl passwd -1 -salt 'random-phrase-here' 'your-password-here'" to generate new one
8 : fencing tools were not found, and are required to use the (optional) power management features. install cman or fence-agents to use them
Restart cobblerd and then run 'cobbler sync' to apply changes.

上面这段信息大意就是：
1，编辑/etc/cobbler/settings文件，找到 server选项，修改为提供服务的ip地址（也就是你这台机器的IP地址，不能是127.0.0.1）
2，编辑/etc/cobbler/settings文件，找到 next_server选项，修改为127.0.0.1以外的地址（也就是本机IP地址）
3，编辑/etc/xinetd.d/tftp文件，将文件中的disable字段的配置由yes改为no
4，执行 cobbler get-loaders，系统将自动下载loader程序，完成提示4的修复工作。
5，文件/etc/xinetd.d/rsync不存在
6，提示说debmirror没安装。如果不是安装 debian之类的系统，此提示可以忽略，如果需要安装，下载地址为：
http://rpmfind.net/linux/rpm2html/search.php?query=debmirror
7，修改cobbler用户的默认密码，可以使用如下命令生成密码，并使用生成后的密码替换/etc/cobbler/settings中的密码。生成密码命令：openssl passwd -1 -salt 'random-phrase-here' 'your-password-here'
8, 安装fence-agents

----------

# 七、导入和查看镜像
## 1.导入镜像
挂载镜像`mount /dev/cdrom /mnt/cdrom/`，导入`cobbler import --path=/mnt/cdrom/ --name=centos7 --arch=x86_64`
## 2.查看镜像
- 命令行查看
```
cobbler distro list
cobbler profile report
```
- 界面查看*https://192.168.2.185/cobbler_web/*