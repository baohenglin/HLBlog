### 《iOS之性能优化篇》
&emsp;&emsp;App具有良好的用户体验对用户而言是非常重要的。业界诸多优秀的App都具有“启动速度快、运行流畅不卡顿、耗电量少且安装包体积小”的特点。那么为了提升App的用户体验，我们开发者应该如何进行性能优化呢？

&emsp;&emsp;我们可以围绕卡顿与否（流畅度）、耗电情况、启动时间以及安装包瘦身等四个方面来着手进行相应的优化。

### *1.流畅度（卡顿方面）*
#### 1.1屏幕显示图像的原理

&emsp;&emsp;在设备屏幕成像的过程中，CPU和GPU起着至关重要的作用。CPU（Central Processing Unit,中央处理器）主要处理的事物包括对象的创建与销毁、对象属性的调整、布局计算、文本的计算与排版（渲染）、图片格式的转换和解码、图像的绘制（Core Graphics）等。GPU（Graphics Processing Unit，图形处理器）主要负责纹理的渲染、视图的混合以及图形的生成。那么屏幕显示图像的原理是怎样的呢？

&emsp;&emsp;以下有关屏幕显示图像原理和卡顿原因的论述直接引用的ibireme大神的博客内容，详情请查阅[iOS保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)

![屏幕显示图像的原理](https://upload-images.jianshu.io/upload_images/4164292-fe2be48c935d35bb.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "成像原理")

&emsp;&emsp;“首先从过去的CRT显示器原理说起。CRT的电子枪按照上面的方式，从上到下一行行扫描，扫描完成后显示器就呈现一帧画面，随后电子枪回到初始位置继续下一次扫描。为了把显示器的显示过程和系统的视频控制器进行同步，显示器（或者其他硬件）会用硬件时钟产生一系列的定时信号。当电子枪换到新的一行，准备进行扫描时，显示器会发出一个水平同步信号（horizonal synchronization），简称 HSync；而当一帧画面绘制完成后，电子枪回复到原位，准备画下一帧前，显示器会发出一个垂直同步信号（vertical synchronization），简称 VSync。显示器通常以固定频率进行刷新，这个刷新率就是 VSync 信号产生的频率。尽管现在的设备大都是液晶显示屏了，但原理仍然没有变。”

![图像显示过程](https://blog.ibireme.com/wp-content/uploads/2015/11/ios_screen_display.png "图像显示")

&emsp;&emsp;“通常来说，计算机系统中 CPU、GPU、显示器是以上面这种方式协同工作的。CPU 计算好显示内容提交到 GPU，GPU 渲染完成后将渲染结果放入帧缓冲区，随后视频控制器会按照 VSync 信号逐行读取帧缓冲区的数据，经过可能的数模转换传递给显示器显示。”

&emsp;&emsp;“在最简单的情况下，帧缓冲区只有一个，这时帧缓冲区的读取和刷新都都会有比较大的效率问题。为了解决效率问题，显示系统通常会引入两个缓冲区，即双缓冲机制。在这种情况下，GPU 会预先渲染好一帧放入一个缓冲区内，让视频控制器读取，当下一帧渲染好后，GPU 会直接把视频控制器的指针指向第二个缓冲器。如此一来效率会有很大的提升。”

&emsp;&emsp;“双缓冲虽然能解决效率问题，但会引入一个新的问题。当视频控制器还未读取完成时，即屏幕内容刚显示一半时，GPU 将新的一帧内容提交到帧缓冲区并把两个缓冲区进行交换后，视频控制器就会把新的一帧数据的下半段显示到屏幕上，造成画面撕裂现象。”如下图：

![画面撕裂现象](https://blog.ibireme.com/wp-content/uploads/2015/11/ios_vsync_off.jpg "画面撕裂")

&emsp;&emsp;“为了解决这个问题，GPU 通常有一个机制叫做垂直同步（简写也是 V-Sync），当开启垂直同步后，GPU 会等待显示器的 VSync 信号发出后，才进行新的一帧渲染和缓冲区更新。这样能解决画面撕裂现象，也增加了画面流畅度，但需要消费更多的计算资源，也会带来部分延迟。”

&emsp;&emsp;“那么目前主流的移动设备是什么情况呢？从网上查到的资料可以知道，iOS 设备会始终使用双缓存，并开启垂直同步。而安卓设备直到 4.1 版本，Google 才开始引入这种机制，目前安卓系统是三缓存+垂直同步。”

#### 1.2卡顿原因分析
![卡顿原因分析图](https://blog.ibireme.com/wp-content/uploads/2015/11/ios_frame_drop.png)

![屏幕图像显示的处理流程](https://upload-images.jianshu.io/upload_images/4164292-b44e6ef4a73c9fee.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

&emsp;&emsp;“在 VSync 信号到来后，系统图形服务会通过 CADisplayLink 等机制通知 App，App 主线程开始在 CPU 中计算显示内容，比如视图的创建、布局计算、图片解码、文本绘制等。随后 CPU 会将计算好的内容提交到 GPU 去，由 GPU 进行变换、合成、渲染。随后 GPU 会把渲染结果提交到帧缓冲区去，等待下一次 VSync 信号到来时显示到屏幕上（按照60fps的刷帧率，每隔16ms就会有一次VSync信号）。由于垂直同步的机制，如果在一个 VSync 时间内，CPU 或者 GPU 没有完成内容提交，则那一帧就会被丢弃（“掉帧”现象），等待下一次VSync垂直信号再显示，而这时显示屏会保留之前的内容不变。这就是界面卡顿的原因。”

&emsp;&emsp;“从上面的图中可以看到，CPU和GPU不论哪个阻碍了显示流程，都会造成掉帧现象。所以开发时，也需要分别对CPU和GPU压力进行评估和优化。”

#### 1.3 卡顿优化
&emsp;&emsp;解决卡顿的主要思路是：尽可能减少CPU、GPU的资源消耗。

#### 1.3.1 CPU方面
* 对象创建方面，尽量使用轻量级的对象，比如不需要响应触摸事件的地方，可以考虑使用CALayer取代UIView；通过Storyboard创建视图对象时，其资源消耗会比直接通过代码创建对象要大很多；
* 对象调整方面，不要频繁地调用UIView的相关属性，比如Frame、bounds、transform等属性，尽量减少不必要的属性修改。也就是说，要尽量提前计算好布局，在有需要时一次性调整对应的属性，不要多次修改属性。因为UIView的关于显示的属性（比如frame/bounds/transform）等实际上都是CALayer属性映射来得，也就是说CALayer内部并没有属性，当调用属性方法时，它内部是通过运行时 resolveInstanceMethod 为对象临时添加一个方法，并把对应属性值保存到内部的一个Dictionary里，同时还会通知delegate、创建动画等等，非常消耗资源。

&emsp;&emsp;当视图层次调整时，UIView、CALayer之间会出现很多方法调用和通知，所以在优化性能时，应该尽量避免调整视图层次、添加和移除视图。

* 对象销毁方面，当容器持有大量对象时，其销毁时的资源消耗会非常明显。这时如果对象可以放到子线程去释放，那就挪到子线程去释放。比如：

```
NSArray *tmp = self.array;
self.array = nil;
dispatch_async(queue, ^{
    [tmp class];
});
```

* 布局计算方面，视图布局的计算是App中最为常见的消耗CPU资源的地方。如果能在后台线程提前计算好视图布局、并且对视图布局进行缓存，那么对性能提升也有一定作用。不论通过哪种技术对视图进行布局，其最终都会落到对UIView.frame/bounds/center等属性的调整上。对这些属性的调整非常消耗资源，所以尽量提前计算好布局，在需要时一次性调整好对应属性，而不要多次、频繁的计算和调整这些属性。
* Autolayout。对于复杂视图来说，Autolayout常常会产生严重的性能问题，随着视图数量的增长，Autolayout带来的CPU消耗会成指数级增长。如果不想手动调整frame等属性，可以使用一些工具方法替代（比如常见的left/right/top/bottom/width/height等属性），或者使用ComponentKit、AsyncDisplayKit等框架。
* 文本计算方面，如果一个界面包含大量文本（比如微博、微信、朋友圈等），文本的宽高计算不可避免地会占用很大一部分资源。此时可以参考UILabel内部的实现方式，用 [NSAttributedString boundingRectWithSize:options:context:] 来计算文本宽高，用 -[NSAttributedString drawWithRect:options:context:] 来绘制文本。并且将计算宽高和绘制文本这样的耗时操作放到子线程中，以避免阻塞主线程。
* 文本渲染方面，所有文本内容控件（UILabel、UITextView），其底层都是通过 CoreText排版、绘制为Bitmap显示的。而且排版、绘制的过程都是在主线程进行的。当显示大量文本时，CPU的资源消耗会非常大。对此解决方案只有一个，那就是自定义文本控件，使用TextKit或最底层的CoreText对文本进行异步绘制。这样做的好处是：CoreText对象创建好之后，能直接获取文本的宽高等信息，避免了多次计算（调整UILabel大小时会计算一遍，UILabel绘制时内部会再计算一遍）；CoreText对象占用内存较少，可以缓存下来以备稍后多次渲染。
* 图片解码方面，图片的解码默认是在主线程进行的。那么我们可以在子线程中先把图片绘制到CGBitmapContext中，然后使用CGBitmapContextCreateImage方法获取CGImage，进而创建图片并返回到主线程显示，实现子线程异步解码图片的目的。
* 图像绘制方面。图像的绘制通常是指用那些以CG开头的方法把图像绘制到画布中，然后从画布创建图片并显示这样一个过程。比如[UIView drawRect:]。我们可以将图像的绘制放到后台子线程中进行，以实现子线程异步绘制。具体实现代码如下：

```
dispatch_async(dispatch_get_global_queue(0, 0), ^{
        // 获取CGImage
        CGImageRef cgImage = [UIImage imageNamed:@"timg"].CGImage;
        // alphaInfo
        CGImageAlphaInfo alphaInfo = CGImageGetAlphaInfo(cgImage) & kCGBitmapAlphaInfoMask;
        BOOL hasAlpha = NO;
        if (alphaInfo == kCGImageAlphaPremultipliedLast ||
            alphaInfo == kCGImageAlphaPremultipliedFirst ||
            alphaInfo == kCGImageAlphaLast ||
            alphaInfo == kCGImageAlphaFirst) {
            hasAlpha = YES;
        }
        // bitmapInfo
        CGBitmapInfo bitmapInfo = kCGBitmapByteOrder32Host;
        bitmapInfo |= hasAlpha ? kCGImageAlphaPremultipliedFirst : kCGImageAlphaNoneSkipFirst;
        // size
        size_t width = CGImageGetWidth(cgImage);
        size_t height = CGImageGetHeight(cgImage);
        // context
        CGContextRef context = CGBitmapContextCreate(NULL, width, height, 8, 0, CGColorSpaceCreateDeviceRGB(), bitmapInfo);
        // draw
        CGContextDrawImage(context, CGRectMake(0, 0, width, height), cgImage);
        // get CGImage
        cgImage = CGBitmapContextCreateImage(context);
        // into UIImage
        UIImage *newImage = [UIImage imageWithCGImage:cgImage];
        // release
        CGContextRelease(context);
        CGImageRelease(cgImage);
        // back to the main thread
        dispatch_async(dispatch_get_main_queue(), ^{
            self.imageView.image = newImage;
        });
    });
```
* 图片的size最好刚好和UIImageView的size保持一致。
* 尽量把耗时操作放到子线程，并控制一下线程的最大并发数量。

#### 1.3.2 GPU方面
&emsp;&emsp;GPU的主要职责是接收提交的纹理（Texture）和定点描述（三角形），应用变化（transform）、混合并渲染，然后输出到屏幕上。为了减少GPU资源的消耗，可以从以下几个方面来进行优化：

* 纹理的渲染

&emsp;&emsp;尽量不要让图片和视图的大小超过纹理尺寸的上限值4096*4096。因为当图片过大，超过GPU的最大纹理尺寸时，图片需要先由CPU进行预处理，这对CPU和GPU都会带来额外的资源消耗。此外，当短时间显示大量图片时（比如TableView存在非常多的图片并且快速滑动时），CPU占用率很低，GPU占用率非常高，界面仍然会掉帧。避免这种情况的方法只能是尽量减少在短时间内大量图片的显示，尽可能将多张图片合成为一张进行显示。

* 视图的混合（Composing）

&emsp;&emsp;尽量减少视图的数量和层次，减少透明的视图（alpha<1），不透明的视图设置opaque为YES。因为当多个视图（或CALayer）重叠在一起显示时，GPU会首先把他们混合到一起。如果视图结构过于复杂，混合的过程也会消耗很多的GPU资源。

* 离屏渲染

&emsp;&emsp;尽量避免触发离屏渲染。在OpenGL中，GPU有2种渲染方式：On-Screen Rendering（当前屏幕渲染） 和 Off-Screen Rendering（离屏渲染）。On-Screen Rendering是指当前屏幕渲染，也就是在当前用于显示的屏幕缓冲区进行渲染操作。Off-Screen Rendering是指离屏渲染，也就是在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。离屏渲染操作之所以消耗性能是因为一方面离屏渲染需要创建新的缓冲区，另一方面离屏渲染的整个过程，需要多次切换上下文环境，先是从当前屏幕（On-Screen）切换到离屏（Off-Screen）；等到离屏渲染结束后，将离屏缓冲区的渲染结果显示到屏幕上，又需要将上下文环境从离屏切换到当前屏幕。那么哪些操作会触发离屏渲染呢？

&emsp;&emsp;（1）光栅化。开启layer.shouldRasterize = YES，会触发离屏渲染。

&emsp;&emsp;【注意】单次使用的视图或者视图有变动，开启shouldRasterize不会有任何用途，反而会牺牲内存。开启shouldRasterize会触发离屏渲染，所以会降低一次性渲染的效率。当shouldRasterize设成true时，layer被渲染成一个bitmap，并缓存起来，等下次使用时不会再重新去渲染了。实现圆角本身就是在做颜色混合（blending），如果每次页面出来时都blending，消耗太大，这时shouldRasterize = yes，下次就只是简单的从渲染引擎的cache里读取那张bitmap，节约系统资源。

&emsp;&emsp;由于光栅化会导致离屏渲染，影响图像性能，那么光栅化是否有助于优化性能，就取决于光栅化创建的位图缓存是否被有效复用，而减少渲染的频度。可以使用Instruments进行检测：当你使用光栅化时，你可以开启“Color Hits Green and Misses Red”来检查该场景下光栅化操作是否是一个好的选择。如果光栅化的图层是绿色，就表示这些缓存被复用；如果是红色就表示缓存会被重复创建，这就表示该处存在性能问题了。

&emsp;&emsp;（2）遮罩。lay.mask

&emsp;&emsp;（3）圆角。同时设置layer.masksToBounds = YES,layer.cornerRadius大于0。解决方法：考虑通过CoreGraphics绘制裁剪圆角，或者让美工提供圆角图片。

&emsp;&emsp;（4）阴影。设置了layer.shadowColor/shadowOffset/shadowRadius/shadowOpacity等会触发离屏渲染。但是如果设置了layer.shadowPath就不会产生离屏渲染。

### *2.耗电情况*
#### 2.1 耗电的主要原因有哪些？
* CPU处理，Processing
* 网络，Networking，联网状态或者请求服务器数据时
* 定位，Location
* 图像，Graphics，比如GPU渲染显示图片等

#### 2.2 如何降低耗电？
* 尽可能降低CPU、GPU功耗。比如上面卡顿优化所提到的方法。
* 少用定时器
* 优化I/O操作（数据读写）。比如：

&emsp;&emsp;（1）尽量不要频繁写入小数据，最好批量一次性写入。

&emsp;&emsp;（2）读写大量重要数据时，考虑用dispatch_io，其提供了基于GCD的异步操作文件I/O的API。用dispatch_io系统会优化磁盘访问。

&emsp;&emsp;（3）数据量较大时，建议使用数据库（比如SQLite、CoreData）

* 网络优化

&emsp;&emsp;（1）减少网络数据的传输（比如使用Json格式而非体积更大的XML数据格式）；压缩网络数据（上传文件或者图片时先压缩再上传）

&emsp;&emsp;（2）如果多次请求的结果是相同的，尽量使用缓存。

&emsp;&emsp;（3）使用断点续传，否则网络不稳定时可能多次传输相同的内容。

&emsp;&emsp;（4）网络不可用时，尽量不要执行网络请求。

&emsp;&emsp;（5）让用户可以取消长时间运行或者速度很慢的网络操作，设置合适的网络请求超时时间。

&emsp;&emsp;（6）批量传输。比如下载视频流时，不要传输很小的数据包，直接下载整个文件或者一大块一大块地下载。如果下载广告，一次性多下载一些，然后再慢慢展示。如果下载电子邮件，一次下载多封，不要一封一封地下载。

* 定位优化

&emsp;&emsp;（1）如果只是需要快速确定用户位置，最好用CLLocationManager的requestLocation方法。定位完成后，会自动让定位硬件断电。

&emsp;&emsp;（2）如果不是导航应用，尽量不要实时更新位置，定位完毕就关掉定位服务。

&emsp;&emsp;（3）尽量降低定位精度，比如尽量不要使用精度最高的kCLLocationAccuracyBest。

&emsp;&emsp;（4）需要后台定位时，尽量设置pausesLocationUpdatesAutomatically为YES，如果用户不太可能移动的时候系统会自动暂停位置更新。

&emsp;&emsp;（5）尽量不要使用startMonitoringSignificantLocationChanges，优先考虑startMonitoringForRegion:方法。

&emsp;&emsp;（6）硬件检测优化。比如用户移动、摇晃、倾斜设备时，会产生动作(motion)事件，这些事件由加速度计、陀螺仪、磁力计等硬件检测。在不需要检测的场合，应该及时关闭这些硬件。

### *3.启动时间优化*

#### 3.1 App的启动形式

* 冷启动（Cold Launch）：从零开始启动APP。
* 热启动（Warm Launch）：APP已经在内存中，在后台存活着，再次点击图标启动APP。

&emsp;&emsp; APP启动时间的优化，主要是针对冷启动进行优化。那么如何来打印App启动时间日志呢？Xcode给我们提供了这个功能。通过在Xcode中添加环境变量可以打印出APP的启动时间分析（Edit scheme -> Run -> Arguments）。将DYLD_PRINT_STATISTICS设置为1，如果需要更详细的信息，那就将DYLD_PRINT_STATISTICS_DETAILS设置为1。

#### 3.2 App的冷启动分为哪几个阶段？

&emsp;&emsp;App的冷启动可以概括为如下3大阶段：

* dyld阶段

&emsp;&emsp;dyld（dynamic link editor）,Apple的动态连接器，用来装载Mach-O文件（可执行文件、动态库等）。也就是说当启动App时，dyld首先会装载App的可执行文件，同时会递归加载所有依赖的动态库，当dyld把可执行文件、动态库都加载完毕后，会通知Runtime进行下一步的处理。

* runtime阶段

&emsp;&emsp;runtime阶段主要职责包括：（1）调用map_images进行可执行文件内容的解析和处理；（2）在load_images中调用call_load_methods，调用所有Class和Category的+load方法；（3）进行各种objc结构的初始化（注册Objc类、初始化类对象等等）；（4）调用C++静态初始化器和_attribute_((constructor))修饰的函数。
&emsp;&emsp;到此为止，可执行文件和动态库中所有的符号（Class，Protocol，Selector，IMP...）都已经按照相应的格式成功加载到内存中，被runtime所管理。

* main阶段

&emsp;&emsp;main阶段会调用UIApplicationMain函数，然后调用AppDelegate的application:didFinishLaunchingWithOptions:方法。

![App冷启动的3大阶段](https://upload-images.jianshu.io/upload_images/4164292-663a80feaee23044.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 'App冷启动的3大阶段')

【总结】App的启动由dyld主导，将可执行文件加载到内存中，并加载递归加载所有依赖的动态库；然后由runtime负责加载成objc定义的结构；所有初始化工作结束后，dyld就会调用main函数，调用UIApplicationMain函数，然后调用AppDelegate的application:didFinishLaunchingWithOptions:方法。


#### 3.3 App启动的优化方法

&emsp;&emsp;按照不同阶段，分别进行相应的优化。

* dyld阶段

&emsp;&emsp; ✅ 减少动态库、合并一些动态库（定期清理不必要的动态库）；

&emsp;&emsp; ✅ 减少Objc类、分类的数量、减少Selector数量（定期清理不必要的类、分类）

&emsp;&emsp; ✅ 减少C++虚函数数量

&emsp;&emsp; ✅ Swift尽量使用Struct（结构体），而尽量少使用Class（类）。

* runtime阶段

&emsp;&emsp; ✅ 尽量使用 +initialize 方法和 dispatch_once 取代所有的_attribute_((constructor))、C++静态构造器、ObjC的+load方法(轻量化+load方法中的内容)；

```
+ (void)initialize
{
	static dispatch_once_t onceToken;
	dispatch_once(&onceToken,^{
		
	});
}
```

* main函数阶段

&emsp;&emsp; ✅ 在不影响用户体验的前提下，尽可能将一些操作延迟，不要全部都放在finishLaunching方法中；

&emsp;&emsp; ✅ 按需加载，只加载必须展示的数据，其他非必须的数据可以稍后请求。

* 主⻚面Controller中的viewDidLoad和viewWillAppear方法中尽量少做事情.
* 如果APP有loading广告页并且对分辨率的要求较高,可以尝试做缓存处理。
* 如果启动流程依赖网络请求回来才能继续,那么需要考虑网络极差情况下的启动速度。
* 耗时操作进行异步处理

### *4.安装包瘦身*

&emsp;&emsp;App开发完成后，都要经过编译、打包为ipa文件，然后发布到App Store供用户下载使用。Apple审核要求通过OTA方式下载App最大是150M(也就是超过150M的安装包只能在 WiFi环境下下载)，为了提高上传发布和用户下载的速度，开发者有必要对ipa进行瘦身优化。在探索有哪些优化措施之前，我们先了解安装包（ipa）的组成。

&emsp;&emsp;安装包（ipa）主要由可执行文件、资源文件等组成。

#### 瘦身优化方法：

* 资源文件方面

&emsp;&emsp; ✅对资源文件（比如图片、音频、视频），采取无损压缩；

&emsp;&emsp; ✅去除没有用到的资源（[第三方检测工具](https://github.com/tinymind/LSUnusedResources)）；



* 可执行文件方面

&emsp;&emsp; ✅ 编译器优化。（1）开启编译优化。比如将Strip Linked Product、Make Strings Read-Only、Symbols Hidden by Default设置为YES；（2）去掉异常支持。比如将Enable C++ Exceptions、Enable Objective-C Exceptions设置为NO， Other C Flags添加-fno-exceptions；（3）避免编译多个架构。可以放弃对armv7，armv7s架构的支持，因为iPhone5s之后的所有设备都是arm64架构的，这样动态库和二进制可执行文件体积会减少很多。

&emsp;&emsp; ✅ 利用[AppCode](https://www.jetbrains.com/objc/)检测未使用的代码：菜单栏 -> Code -> Inspect Code

&emsp;&emsp; ✅ 生成LinkMap文件，可以查看可执行文件的具体组成。在Target->Build Settings->Linking中将Write Link Map File设置为YES；也可以借助第三方工具分析LinkMap文件，比如[第三方分析工具LinkMap](https://github.com/huanxsd/LinkMap) 。
<br>
<br>
<br>

#### 参考资料
[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)

[关于 IPA瘦身的探索与思考](http://www.cocoachina.com/ios/20180507/23298.html)

[iOS安装包瘦身的那些事儿](https://www.cnblogs.com/LeeGof/p/6803146.html?utm_source=itdadao&utm_medium=referral)



