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

安装过程中，会向用户提示选择安装哪种模式（LAMP、LNMP、LNAMP、install all service）,建议选择“install all service”，安装所有服务的话，以后可以在各个模式之间切换，只是安装时间长一些。

```
LAMP是Linux、Apache、Mysql、Perl/PHP/Python的首字母简写；
LNMP是Linux、nginx、Mysql、Perl/PHP/Python的首字母简写；
LNAMP是Linux、nginx、Apache、Mysql、Perl/PHP/Python的首字母简写；
```

安装成功如下所示：

![WDCP安装成功示意图.png](https://upload-images.jianshu.io/upload_images/4164292-af3f6804423f3426.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们最后可以看到上图所示的界面，表示WDCP面板安装完毕。


## 登录WDCP面板和修改账户密码

根据WDCP面板的默认要求，我们需要登录账户，URL地址是我们VPS的"IP地址:8080"，然后用户名是admin，密码是wdlinux.cn，我们登录进去后需要修改密码。










