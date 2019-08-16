# SDWebImage实现原理分析

SDWebImage是一个支持从远程服务器下载并缓存图片功能的开源的第三方库。为方便起见，SDWebImage为UIImageView，UIButton，MKAnnotationView等添加了分类(Category)。它提供了以下功能：

* (1)为UIImageView，UIButton和MKAnnotationView分别添加了分类，以支持网络图片的加载与缓存管理；
* (2)提供了一个异步的图片下载器；
* (3)异步的内存+磁盘图片缓存，且具有自动到期处理功能；
* (4)后台图片解压缩；
* (5)支持GIF图片；
* (6)自定义缓存控制；
* (7)可通过添加自定义的加载器或使用预构建的加载器（如FLAnimatedImage插件或照片库插件）来扩展图像加载功能；
* (8)确保同一个URL的图片不被下载多次；
* (9)确保虚假的URL不会被反复加载；
* (10)确保主线程永远不被阻塞(无论是下载还是缓存图片的时候)；
* (11)支持WebP图片；
* (12)使用了GCD和ARC。

## SDWebImage加载图片的原理

* (1)当需要获取网络图片时，SDWebImage并非直接通过URL请求远程图片资源，而是检查图片缓存(ImageCache)中是否存在与该URL匹配的图片，如果存在，直接返回image；否则，执行下一步操作。
* (2)当图片缓存(ImageCache)中没有图片时，SDWebImage接下来会检查沙盒中是否存在与URL匹配的图片。如果存在，则将沙盒中的图片存储到图片缓存(ImageCache)中，然后再按照第1步再次进行判断。
* (3)如果沙盒中也不存在与URL匹配的图片，则显示占位图，然后根据图片的下载队列缓存(OperationCache)判断是否正在下载，如果正在下载则等待，避免二次下载。如果不存在则创建下载队列并添加到下载队列缓存(OperationCache)中，下载完毕后将下载操作从下载队列缓存(OperationCache)中移除，并且将image存储到图片缓存(ImageCache)中。
* (4)刷新UI并将image存入沙盒缓存。
