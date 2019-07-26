WDCP(WDlinux Control Panel) 是一套Linux云主机服务器管理系统，旨在易于使用和管理Linux服务器，通过web方式就可以管理操作服务器和虚拟主机。方便查看系统资源使用，开放端口，进程，防火墙(iptables)设置等。通过web后台，就可以管理站点、FTP帐号、mysql数据库等。简单、方便、易操作。总的来说，wdcp(wdlinux)不错的一款国人免费linux主机管理面板，非常适合接触linux系统的新手朋友使用。

## 关于WDCP的安装以及系统支持

wdcp_v3 支持wdOS/CentOS5.x/6.x/7.x。

## 安装方式

有两种安装方式。一种是只安装wdcp面板，另一种是和lanmp环境一起安装。执行安装终端指令之前需要先通过ssh命令登录root账户远程服务器。

登录root账户远程服务器的终端指令如下：

```
ssh -p port   username@IP地址
```
port指端口号，username是用户名，默认情况下就是root，IP地址是你将要登录的远程服务器的IP地址。

* 只安装wdcp面板(仅限看看面板的界面，无相关web环境，速度比较快)

```
wget http://down.wdlinux.cn/in/install_v3.sh
sh install_v3.sh
```

* 和lanmp环境一起安装(包含了WEB环境，安装速度较慢，需要耐心等待)

```
yum install -y wget
wget http://dl.wdlinux.cn/files/lanmp_v3.2.tar.gz
tar zxvf lanmp_v3.2.tar.gz
sh lanmp.sh
```

