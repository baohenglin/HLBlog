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

**安装目录如下：**

```
/www/wdlinux
```

**数据库文件目录如下：**

```
/www/wdlinux/wdcp/data/wdcpdb.db
```

**卸载方法如下：**

```
rm -fr /www/wdlinux
reboot
```

**wdcp后台的启动，重起，关闭方法如下：**

```
service wdcp start
service wdcp restart
service wdcp stop
```


## 登录WDCP面板和修改账户密码

根据WDCP面板的默认要求，我们需要登录账户，URL地址是我们VPS的"IP地址:8080"，然后用户名是admin，密码是wdlinux.cn，我们登录进去后需要修改密码。需要登录密码和MySQL的root密码。


## 更改登录名后无法登录的问题及解决方法

如果我们在修改WDCP账户密码的同时也对账户名进行了修改，而且修改后的账户名中含有某些特殊字符(如@、*、_或者汉字等)，那么再退出之后重新登录时会提示“账户名错误”。

解决方法如下：

* (1)确保root账户处于登录状态
* (2)打开“/www/wdlinux/wdcp/data/wdcpdb.db”数据库（在wdcpdb.db的wd_member中将username修改为最初的admin）

```
sqlite3 /www/wdlinux/wdcp/data/wdcpdb.db
```
* (3)列出所有的数据库的表

```
.tables
```

![WDCP更改登录问题.png](https://upload-images.jianshu.io/upload_images/4164292-39459604dfb48a30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* (4)列出wd_member数据表下的所有数据信息

```
select *from wd_member;
```

![修改前的username.png](https://upload-images.jianshu.io/upload_images/4164292-7d8af20268214d2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

需要特别注意的是，select *from wd_member语句后面必须带上分号，否则指令不起作用。**一定要带分号！一定要带分号！一定要带分号！**

* (5)修改wd_member表中的username的值。

```
update wd_member set username="admin" where id=1;
```

![修改后的username.png](https://upload-images.jianshu.io/upload_images/4164292-54c38f9deb2700b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样就可以解决正常登录了。



<br />
<br />
参考链接：

[wdcp(wdlinux) 最新安装教程](https://www.tracymc.cn/archives/2935)















