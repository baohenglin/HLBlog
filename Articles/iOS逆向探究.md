## iOS Jailbreak(iOS越狱)

什么是iOS Jailbreak呢？iOS Jailbreak是指利用iOS系统的漏洞，获取iOS系统的最高权限（Root），解开之前的各种限制。

### iOS Jailbreak的优点

* (1)打造个性化、与众不同的iPhone
* (2)自由安装各种使用的插件、主题、App
* (3)修改系统App的一些默认行为
* (4)自由安装非AppStore来源的App，“付费App”秒变“免费App”
* (5)灵活管理文件系统，让iPhone可以像U盘那样灵活的管理文件
* (6)给开发者提供了逆向工程的环境

### iOS Jailbreak的缺点

* (1)不予保修
* (2)费电，越狱后的iOS系统会常驻一些进程，耗电速度提高10%~20%
* (3)在新的iOS固件版本出来之前，不能及时地进行更新。每个新版本的固件，都会修复上一个版本的越狱漏洞，使越狱失效。如果需要保持越狱状态，要等待新的越狱程序发布时，才能升级相应的固件版本。
* (4)不再受iOS系统默认的安全保护，容易被恶意软件攻击，个人隐私有被窃取的风险。
* (5)如果安装了不稳定的插件，容易让系统变得不稳定、变慢，甚至出现“白苹果”等问题。

## 完美越狱和不完美越狱

完美越狱：越狱后的iPhone可以正常关机和重启

不完美越狱：iPhone一旦关机后再开机时，屏幕就会一直停留在启动画面，也就是“白苹果”状态。或者能正常开机，但已经安装的破解软件都无法正常使用，需要将设备与PC连接后，使用软件进行引导才能使用。

越狱方法推荐[PP助手](http://jailbreak.25pp.com/)

## 如何判断是否越狱成功？

方法一：看iPhone桌面是否有Cydia，有的话，说明越狱成功。

方法二：可以可以通过PP助手查看。

方法三：通过代码判断当前设备是否已越狱。

```
+ (BOOL)re_isJailbreak {
    return [[NSFileManager defaultManager] fileExistsAtPath:@"/Application/Cydia.app"];
}
```

## Cydia

Cydia其实相当于越狱后的“App Store”，可以在Cydia中安装各种第三方的软件（插件、补丁、APP）。

### Cydia安装软件的步骤

* (1)添加软件源（不同软件的软件源可能不同） 。点击“软件源”->点击右上角的“编辑”->点击“添加”->输入Cydia/APT地址->“添加源”

* (2)进入软件源找到对应的软件，开始安装。

## 安装相关插件

（1）Apple File Conduit “2”插件([Apple File Conduit “2”软件源1](http://apt.saurik.com)、[Apple File Conduit “2”软件源2](http://apt.25pp.com))：其作用是可以访问整个iOS设备的文件系统。类似的补丁还有afc2、afc2add。

（2）AppSync Unified插件([AppSync Unified软件源](http://apt.25pp.com))：其作用是可以绕过系统验证，随意安装、运行破解的ipa安装包。

（3）iFile：其作用是可以在iPhone上自由访问iOS文件系统。[iFile软件源](http://apt.thebigboss.org/repofiles/cydia)

（4）PP助手：可以利用PP助手自由安装海量APP。[PP助手软件源](http://apt.25pp.com)


## 加固

什么是加固呢？加固是为了增加应用的安全性，防止应用被破解、盗版、二次打包、注入、反编译等

常见的加固方式有：

* (1)数据加密(字符串、网络数据、敏感数据等)

* (2)应用加壳（二进制加密）

* (3)代码混淆（类名、方法名、代码逻辑等）

### 代码混淆

iOS程序可以通过class-dump、Hopper、IDA等工具获取类名、方法名以及分析程序的执行逻辑。如果进行代码混淆，可以加大别人的分析难度。

iOS的代码混淆方案：

* (1)源码的混淆

✅ 类名

✅ 方法名

✅ 协议名

* (2)LLVM中间代码IR的混淆（容易产生Bug）

✅ 自己编写Pass

✅ ollvm：https://github.com/obfuscator-llvm/obfuscator


## Mac远程登录到iPhone

iOS和Mac OS X都是基于Darwin（苹果的一个基于Unix的开源系统内核），所以iOS中同样支持终端的命令行操作。在逆向工程中，我们经常会通过命令行来操纵iPhone。为了能够让Mac终端中的命令行能作用在iPhone上，我们得让Mac和iPhone建立连接。我们可以通过Mac远程登录到iPhone的方式建立连接。

### SSH、OpenSSH、SSL、OpenSSL

SSH是Secure Shell的缩写，意为“安全外壳协议”，是一种可以为远程登录提供安全保障的协议。使用SSH，可以把所有传输的数据进行加密，“中间人”攻击方式就不可能实现，能防止DNS欺骗和IP欺骗。

OpenSSH是SSH协议的免费开源实现。可以通过OpenSSH的方式让Mac远程登录到iPhone。因为SSH是通过TCP协议通信，所以要确保Mac和iPhone在同一局域网下，比如连接着同一个WiFi。

SSL是Secure Sockets Layer的缩写，是为网络通信提供安全及数据完整性的一种安全协议，在传输层对网络连接进行加密。OpenSSL是SSL的开源实现。绝大部分HTTPS请求等价于：HTTP + OpenSSL。OpenSSH的加密就是通过OpenSSL完成的。

在iPhone上通过Cydia安装OpenSSH工具（软件源http://apt.saurik.com）。

**使用OpenSSH远程登录，步骤如下**：

(1)在Mac的终端输入ssh 账户名@服务器主机地址。比如ssh root@10.1.1.168（这里的服务器是手机）。初始密码alpine。

(2)登录成功后就可以使用终端命令行操作iPhone。

(3)退出登录命令是exit。

## root、mobile

iOS中有2个常用账户：root和mobile。其中root账户是最高权限账户，$HOME是/var/root。mobile：普通权限账户，只能操作一些普通文件，不能操作系统级别的文件，$HOME是/var/mobile。

登录mobile用户：root mobile@服务器主机地址。root和mobile用户的初始登录密码都是alpine。最好修改一下root和mobile用户的登录密码（登录root账户后，分别通过passwd、passwd mobile完成）。


## SSH的通信过程

SSH协议一共2个版本，分别是SSH-1和SSH-2。现在用的比较多的是SSH-2，客户端和服务端要保持一致才能通信。那么如何查看SSH版本(查看配置文件的Protocol字段)呢？如果是客户端的话，查看路径/etc/ssh/ssh_config，如果是服务端，查看路径/etc/ssh/sshd_config。

**SSH的通信过程可以分为3大主要阶段**:

* (1)建立安全连接阶段。

![SSH通信建立安全连接示意图.png](https://upload-images.jianshu.io/upload_images/4164292-1ff46957e32a5a3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在建立安全连接过程中，服务器会生成一对公钥私钥，并将私钥保留，将公钥下发给客户端。如果是第一次登录该服务器，那么客户端就会根据公钥等身份信息进行安全验证并询问是否连接此服务器，如果连接的话，会将公钥信息保存到~/.ssh/known_hosts中；如果曾经登录过该服务器，即客户端已保存了服务器端的公钥信息，那么客户端将直接连接该服务器。

* (2)客户端认证

SSH-2提供了2种常用的客户端认证方式：

·基于密码的客户端认证:使用账号和密码即可认证

·基于密钥的客户端认证:免密码认证，最安全的一种认证方式。SSH-2默认会优先尝试“密钥认证”，如果认证失败，才会尝试“密码认证”

* (3)数据传输 






