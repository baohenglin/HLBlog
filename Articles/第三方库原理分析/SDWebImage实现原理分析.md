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

* (1)当需要获取网络图片时，SDWebImage并非直接通过URL请求远程图片资源，而是检查**内存图片缓存**(ImageCache)中是否存在与该URL匹配的图片，如果存在，直接返回image；否则，执行下一步操作。
* (2)当内存中没有缓存图片时，SDWebImage接下来会检查沙盒中是否存在与URL匹配的图片。如果存在，则将沙盒中的图片存储到图片缓存(ImageCache)中，然后再按照第1步再次进行判断。
* (3)如果沙盒中也不存在与URL匹配的图片，说明所有缓存都不存在该图片，需要下载图片，此时先显示占位图，然后根据图片的下载队列缓存(OperationCache)判断是否正在下载，如果正在下载则等待，避免二次下载。如果不存在则创建下载队列并添加到下载队列缓存(OperationCache)中，下载完毕后由SDWebImageDecoder进行图片解码，同时将下载操作从下载队列缓存(OperationCache)中移除，然后将image存储到图片缓存(ImageCache)和沙盒中。
* (4)刷新UI并将image存入沙盒缓存。

## 缓存

为了减少网络流量的消耗，我们都希望下载下来的图片缓存到本地，下次再去获取同一张图片时，可以直接从本地获取，而不再从远程服务器获取。这样做的另一个好处是提升了用户体验，用户第二次查看同一幅图片时，能快速从本地获取图片直接呈现给用户。SDWebImage提供了对图片缓存的支持，而该功能是由SDImageCache类来完成的。该类负责处理内存及一个可选的磁盘缓存。其中磁盘缓存的写操作是异步的，这样就不会阻塞主线程。

## 内存缓存 & 磁盘缓存（沙盒缓存）

缓存包括内存缓存和磁盘缓存（沙盒缓存）。其中内存缓存的处理是使用NSCache对象来实现的。NSCache是一个类似集合的容器。它存储key-value键值对，这一点类似于NSDictionary类。我们通常使用缓存来临时存储短时间使用但是创建昂贵的对象。重用这些对象可以优化性能，因为它们的值不需要重新计算。另一方面，这些对象对于程序来说不是紧要的，在内存紧张时会被丢弃。

磁盘缓存的处理是使用NSFileManager对象来实现的。图片存储的位置是位于Cache文件夹。


## 解决图片错位问题

解决图片错位问题，需要判定cell对应的图片地址是否发生改变。具体做法是给每一个imageView都绑定一个下载地址。如果外界传入的下载地址改变，则让imageView绑定的地址变成新的地址，原来的下载操作取消，开始新的下载操作。那么如何给imageView绑定下载地址呢？可以利用Runtime(运行时)，在分类中动态地为 imageView 添加一个属性(urlString)。


<br />
<br />
<br />
<br />

## 参考链接

[SDWebImage](https://github.com/SDWebImage/SDWebImage)

[SDWebImage实现分析](http://southpeak.github.io/2015/02/07/sourcecode-sdwebimage/)


[SDWebImage原理和缓存机制](https://www.xubojoy.cn/2018/08/13/SDWebImage%E5%8E%9F%E7%90%86%E5%92%8C%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6/)

[SDWebImage源码解析](https://juejin.im/post/5bbec080e51d450e4d3021c0)

[SDWebImage源码解析](https://www.jianshu.com/p/29ab0939f7ec)



