iOS之内存管理分析



## NSTimer的循环引用问题

```
@interface ViewController ()
@property (nonatomic, strong) NSTimer *timer;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    //使用scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:会产生循环引用。
    //__weak typeof(self) weakSelf = self;//不能解决NSTimer循环引用问题。可以解决block的循环引用。
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(timerTest) userInfo:nil repeats:YES];
    
}
- (void)timerTest
{
    NSLog(@"%s",__func__);
}
- (void)dealloc
{
    NSLog(@"%s",__func__);
    [self.link invalidate];
}
@end
```

以上代码产生循环引用的原因：self->(强引用)timer->(强引用)target:self.

解决方法1：使用scheduledTimerWithTimeInterval: repeats: block:方法

//打破循环引用的原因：self->(强引用)timer->(强引用)block->(弱引用)self

```
//解决方法1：使用scheduledTimerWithTimeInterval: repeats: block:替代scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:
    __weak typeof(self) weakSelf = self;
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 repeats:YES block:^(NSTimer * _Nonnull timer) {
        [weakSelf timerTest];
    }];
```

## 内存管理总结：

1. 使用CADisplayLink、NSTimer有什么注意点？

CADispalyLink、NSTimer会对target产生强引用，如果target又对它们产生强引用，那么就会导致循环引用。



2.简述内存的几大区域

3.谈谈对iOS内存管理的理解

4.autorelease在什么时候会被释放？

5.方法里有局部对象，出了方法后会立即释放吗？

6.ARC都帮我们做了什么？

7.weak指针的实现原理？
