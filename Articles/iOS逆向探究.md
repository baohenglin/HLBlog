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



