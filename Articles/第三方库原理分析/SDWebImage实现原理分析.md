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

## SDWebImage加载图片的大致原理

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


## SDWebImage的加载及缓存图片的机制详解

* (1)UIImageView+WebCache: setImageWithURL: placeholderImage: options:先显示 placeholderImage ，同时由 SDWebImageManager 根据 URL 在本地缓存中查找对应的图片。
* (2)SDWebImageManager: downloadWithURL: delegate: options: userInfo:。SDWebImageManager是将UIImageView+WebCache同SDImageCache连接起来的类，SDImageCache: queryDiskCacheForKey: delegate: userInfo: 用来查找图片是否已经存储在缓存中(根据CacheKey查找)。
* (3)如果内存中已经缓存了该图片，SDWebImageManager会回调 SDImageCacheDelegate: imageCache: didFindImage: forKey: userInfo:方法。
* (4)而UIImageView+WebCache则回调SDWebImageManagerDelegate: webImageManager: didFinishWithImage: 来显示图片。
* (5)如果内存中没有图片缓存，那么生成 NSInvocationOperation 添加到队列，从硬盘(磁盘包括硬盘和软盘)查找图片是否已被下载缓存。
* (6)根据 URLKey 在硬盘缓存目录下尝试读取图片文件。这一步是在 NSOperation 进行的操作，所以回主线程进行结果回调。
* (7)notifyDelegate:
* (8)如果从硬盘中读取到了图片，那么就将图片添加到内存缓存中(如果空闲内存过小，会先清空内存缓存)。SDImageCacheDelegate 回调 imageCache: didFindImage: forKey: userInfo: 进而回调展示图片。
* (9)如果从硬盘缓存目录没有读取到图片，说明内存缓存和硬盘缓存都不存在该图片，那么就需要从远程服务器下载图片，此时会回调 imageCache: didNotFindImageForKey: userInfo:。
* (10)共享或重新生成一个下载器 SDWebImageDownloader开始下载图片。
* (11)图片下载由 NSURLConnection 来做，实现相关 delegate来判断图片是否处于下载中、下载完成或者下载失败等各种状态。
* (12)connection: didReceiveData: 利用 ImageIO做了按图片下载进度加载效果。
* (13)connectionDidFinishLoading: 数据下载完成后交给 SDWebImageDecoder 来进行图片解码处理。
* (14)图片解码处理在一个 NSOperationQueue里完成，不会阻塞主线程。如果需要对下载的图片进行二次处理，最好也在这里完成，效率会高很多。
* (15)在主线程 notifyDelegateOnMainThreadWithInfo: 宣告解码完成，imageDecoder: didFinishDecodingImage: userInfo: 回调给 SDWebImageDownloader。
* (16)imageDownloader: didFinishWithImage: 回调给 SDWebImageManager告知图片下载完成。 
* (17)通知所有的 downloadDelegates 下载完成，回调给需要的地方展示图片。
* (18)将图片保存到 SDImageCache中，内存缓存和硬盘缓存同时保存。
* (19)写文件到硬盘在单独 NSInvocationOperation中完成，避免阻塞主线程。
* (20)如果是在iOS上运行， SDImageCache 在初始化的时候会注册 notification到 UIApplicationDidReceiveMemoryWarningNotification 以及 UIApplicationWillTerminateNotification，在内存警告的时候清理内存图片缓存，应用结束的时候清理过期图片。
* (21)SDWebImagePrefetcher 可以预先下载图片，方便后续使用。




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



