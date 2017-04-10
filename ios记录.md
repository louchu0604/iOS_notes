全局队列 主线程 NSTimer
***
多用懒加载
在使用 UITableView 的时候, 尽可能的不要在 cell 上嵌套太多的 view, 这些会影响页面滑动的流畅度, 而且越多的 view 就会花费越多的内存. 如果有太多的 view, 影响了页面滑动的流畅度, 那么就可以考虑使用懒加载 view, 即不要一次性创建太多的 view, 有一些 view 到用的时候才去创建, 会更好

***
尽量减少透明图层：
首先我们要明白像素的概念，屏幕上每一个点都是一个像素，像素有R、G、B三种颜色构成（有时候还带有alpha值）。如果某一块区域覆盖了多个layer，最后的显示效果受到了这些layer的共同影响。举个例子，上层是蓝色（RGB=0，0，1），透明度为50%，下层是红色（RGB=1，0，0）。那么最终的显示效果是紫色（RGB=0.5，0，0.5）。这种颜色的混合（blending）需要消耗一定的GPU资源，因为实际上可能不只有两层。如果只想显示最上面的蓝色，可以把它的透明度设置为100%，这样GPU会忽略下面所有的layer，从而节约了很多不必要的运算。

假如最上层的view是不透明的，那直接使用这个view的对应颜色之就可以，但如果view是透明的，在计算像素的颜色值时就需要计算它下面图层，透明的视图越多，计算量就越大，因此也会对图形的性能产生一定的影响，所以可以的话也尽量减少透明图层的数目。


***
如何解决“直接把密码存储在NSUserDefaults中不安全”的问题？
1.把密码加密后再存储到NSUserDefaults中
iOS中提供了很多种加密算法，对于存储密码，可以使用不可逆的MD5加密。
2.不使用NSUserDefaults保存密码，使用keyChain来保存密码
***
CGImageRef
ARC is only for NSObject。在iOS 中使用malloc分配的内存，ARC是不会处理的，需要自己进行处理

***
Analyze-静态分析

检测出的常见的三种泄露
（1）.  创建了对象，但是并没有使用。
 Value Stored to 'XX' is never read
 存储在'XX'里的值从未被读取过，
（2）. 创建了一个（指针可变的）对象，且初始化了，但是初始化的值一直没读取过。
 Value Stored to 'str' during its initialization is never read
（3）.调用了让某个对象引用计数加1的函数，但没有调用相应让其引用计数减1的函数。
 Potential leak of an object stored into 'subImageRef'
 subImageRef对象的内存单元有潜在的泄露风险

- (void)leak3 {
    CGRect rect = CGRectMake(0, 0, 50, 50);
    UIImage *image;
    CGImageRef subImageRef = CGImageCreateWithImageInRect(image.CGImage, rect); // subImageRef 引用计数 + 1;
    UIImage* smallImage = [UIImage imageWithCGImage:subImageRef];
    // 应该调用对应的函数，让subImageRef的引用计数减1,就不会泄露了
    // CGImageRelease(subImageRef);
    [smallImage class];
    UIGraphicsEndImageContext();

    例子二：
        CTFontRef fontRef = CTFontCreateWithName((__bridge CFStringRef)_font.fontName, _font.pointSize, NULL);
[_string addAttributes:[NSDictionary dictionaryWithObjectsAndKeys:(__bridge NSObject*)fontRef, (NSString*)kCTFontAttributeName, nil]
                 range:NSMakeRange(0, [_string length])];

CGColorRef colorRef = _textColor.CGColor;
[_string addAttributes:[NSDictionary dictionaryWithObjectsAndKeys:(__bridge NSObject*)colorRef,(NSString*)kCTForegroundColorAttributeName, nil]
                 range:NSMakeRange(0, [_string length])];

    // 应该调用对应的函数，让subImageRef的引用计数减1,就不会泄露了
    //  CFRelease(fontRef);

}
***

iOS定位到崩溃代码行数
举例：
4 Sections 0x000030eb -[BIDViewController searchDisplayController:didLoadSearchResultsTableView:] + 171
一共包括了5部分：分别是4/Sections/0x000030eb/-[BIDVi...ew:]/171
第1部分：堆栈输出序列号，序号越大表示代码越早被调用；
第2部分：调用的方法所属框架（库/工程），例如Sections就是楼主您的工程；
第3部分：调用方法的内存地址，即0x000030eb
第4部分：调用方法名，这个非常重要，即-[BIDViewController searchDisplayController:didLoadSearchResultsTableView:]
第5部分：调用方法编译之后的代码偏移量，这里再次强调不是行号，只是编译后的代码偏移量，但是基本上和字符数差不多，你可以通过一些文本工具，通过统计你的方法的字符偏移量大致的估计出错的行，一般误差在3~5行内。
控制套输入：
(lldb) image lookup --address 0x000030eb


***
avplayer：缓存足够但播放延迟  [_player setAutomaticallyWaitsToMinimizeStalling:false];

***
shader声明变量类型：
1.uniform变量
uniform变量是外部application程序传递给（vertex和fragment）shader的变量。因此它是application通过函数glUniform**（）函数赋值的。在（vertex和fragment）shader程序内部，uniform变量就像是C语言里面的常量（const ），它不能被shader程序修改。（shader只能用，不能改）

如果uniform变量在vertex和fragment两者之间声明方式完全一样，则它可以在vertex和fragment共享使用。（相当于一个被vertex和fragment shader共享的全局变量）

uniform变量一般用来表示：变换矩阵，材质，光照参数和颜色等信息。

以下是例子：

uniform mat4 viewProjMatrix; //投影+视图矩阵
uniform mat4 viewMatrix;        //视图矩阵
uniform vec3 lightPosition;     //光源位置

 

2.attribute变量
attribute变量是只能在vertex shader中使用的变量。（它不能在fragment shader中声明attribute变量，也不能被fragment shader中使用）

一般用attribute变量来表示一些顶点的数据，如：顶点坐标，法线，纹理坐标，顶点颜色等。

在application中，一般用函数glBindAttribLocation（）来绑定每个attribute变量的位置，然后用函数glVertexAttribPointer（）为每个attribute变量赋值。

以下是例子：

uniform mat4 u_matViewProjection;
attribute vec4 a_position;
attribute vec2 a_texCoord0;
varying vec2 v_texCoord;
void main(void)
{
gl_Position = u_matViewProjection * a_position;
v_texCoord = a_texCoord0;
}

3.varying变量
varying变量是vertex和fragment shader之间做数据传递用的。一般vertex shader修改varying变量的值，然后fragment shader使用该varying变量的值。因此varying变量在vertex和fragment shader二者之间的声明必须是一致的。application不能使用此变量。

以下是例子：

// Vertex shader
uniform mat4 u_matViewProjection;
attribute vec4 a_position;
attribute vec2 a_texCoord0;
varying vec2 v_texCoord; // Varying in vertex shader
void main(void)
{
gl_Position = u_matViewProjection * a_position;
v_texCoord = a_texCoord0;
}


// Fragment shader
precision mediump float;
varying vec2 v_texCoord; // Varying in fragment shader
uniform sampler2D s_baseMap;
uniform sampler2D s_lightMap;
void main()
{
vec4 baseColor;
vec4 lightColor;
baseColor = texture2D(s_baseMap, v_texCoord);
lightColor = texture2D(s_lightMap, v_texCoord);
gl_FragColor = baseColor * (lightColor + 0.25);
}



***

uiwebview 滚动卡顿可能原因 将滚动参数调整为 正常速度：
 _web.scrollView.decelerationRate = UIScrollViewDecelerationRateNormal;
 
 
 UIScrollViewDecelerationRateFast：更快,但是体现的效果是会有一点卡顿
***
缓冲位有GL_COLOR_BUFFER_BIT，GL_DEPTH_BUFFER_BIT和GL_STENCIL_BUFFER_BIT

图形渲染管线：Graphics Pipeline
 CADisplayLink是一个能让我们以和屏幕刷新率相同的频率将内容画到屏幕上的定时器。
***
图形绘制：
CGContextMoveToPoint
CGContextAddLineToPoint
CGContextAddArcToPoint
CGContextClosePath
***
关联对象
OBJC_EXPORT void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy)
OBJC_EXPORT id objc_getAssociatedObject(id object, const void *key)
OBJC_EXPORT void objc_removeAssociatedObjects(id object)
设置关联对象： objc_setAssociatedObject(button, buttonKey, _imgView, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
取出关联对象：- (void)buttonPress:(UIButton *)sender//headButton点击
{    UIImageView *imageView =  objc_getAssociatedObject(sender,buttonKey);
}

***
LLDB调试：
p：
（用于输出基本类型）
po：
（用于输出 Objective-C 对象）
expr：  
可以在调试时动态执行指定表达式，并将结果打印出来。常用于在调试过程中修改变量的值。
call
call即是调用的意思。用于输出新声明对象的值
call [self.view setBackgroundColor:[UIColor redColor]] view的背景颜色变成红色
bt 
打印调用堆栈，加all可打印所有thread的堆栈。
image
image 命令可用于寻址，有多个组合命令。比较实用的用法是用于寻找栈地址对应的代码位置。
image lookup --address 0x0000000100004af8
***
跳转到设置
[[UIApplication sharedApplication] openURL:[NSURL
URLWithString:UIApplicationOpenSettingsURLString]];
***

解决Xcode断点调试值都为nil的问题：
在Build Settings中 Optimization Level 设置成 None
***
快捷键：
跳转到光标所在行：CTRL + L
Debug: command+enter
Build: command+B
Run: command+R
***
ios10:
Native://type=getSession    像这种URL scheme根本就不符合规范的～而下面两个符合URL scheme规范，当然就OK了～
标准的URL scheme是：<URL前缀>://<URL描述>?<参数1>=<值1>&<参数2>=<值2>
/*funcname:encodeURIComponent(params)导致 :% 出现，ios10中会出错*/


ios10之前：因为这些系统没有做严格筛选～ 



***
NSTimer fire 函数
You can use this method to fire a repeating timer without interrupting its regular firing schedule. If the timer is non-repeating, it is automatically invalidated after firing, even if its scheduled fire date has not arrived.

***
 播放器修改音量以及获取音量 需导入头文件<MediaPlayer/MediaPlayer.h>
 MPMusicPlayerController *mpc = [MPMusicPlayerController applicationMusicPlayer];
                    mpc.volume = ff;  //0.0~1.0

***
- (void)webViewDidFinishLoad:(UIWebView *)webView
使用：


***
虚线边框
 CAShapeLayer *borderLayer = [CAShapeLayer layer];
    borderLayer.fillColor = [UIColor clearColor].CGColor;
    borderLayer.strokeColor = RGB(200, 200, 200).CGColor;
    borderLayer.path = [UIBezierPath bezierPathWithRect:bk.bounds].CGPath;
    borderLayer.lineWidth = 1;
    borderLayer.lineCap = @"square";
    borderLayer.lineDashPattern = @[@2, @2];线的长度，和线的间隔
    [bk.layer addSublayer:borderLayer];


***

1、NSURLRequestUseProtocolCachePolicy NSURLRequest默认的cache policy，使用Protocol协议定义。
2、NSURLRequestReloadIgnoringCacheData 忽略缓存直接从原始地址下载。
3、NSURLRequestReturnCacheDataElseLoad 只有在cache中不存在data时才从原始地址下载。
4、NSURLRequestReturnCacheDataDontLoad 只使用cache数据，如果不存在cache，请求失败；用于没有建立网络连接离线模式；
5、NSURLRequestReloadIgnoringLocalAndRemoteCacheData：忽略本地和远程的缓存数据，直接从原始地址下载，与NSURLRequestReloadIgnoringCacheData类似。
6、NSURLRequestReloadRevalidatingCacheData:验证本地数据与远程数据是否相同，如果不同则下载远程数据，否则使用本地数据。


***
iOS开发－获取当前设备信息

1.iOS获取当前app的名称和版本号 
2.NSDictionary *infoDictionary = [[NSBundle mainBundle] infoDictionary];
CFShow(infoDictionary);
// app名称
NSString *app_Name = [infoDictionary objectForKey:@"CFBundleDisplayName"];
// app版本
NSString *app_Version = [infoDictionary objectForKey:@"CFBundleShortVersionString"];
// app build版本
NSString *app_build = [infoDictionary objectForKey:@"CFBundleVersion"];
2.iOS获取当前系统版本号

NSString* phoneVersion = [[UIDevice currentDevice] systemVersion];

float phoneVersion = [[[UIDevice currentDevice] systemVersion] floatValue]
3.iOS获取当前语言 
NSArray *languages = [NSLocale preferredLanguages];
NSString *currentLanguage = [languages objectAtIndex:0];
4.获得iphone唯一标识符UDID(序列号)

NSString *UDID_No = [[UIDevice currentDevice] uniqueIdentifier];
5.当前手机型号

NSString* phoneModel = [[UIDevice currentDevice] model];
6.获得手机别名（用户定义的名称）

NSString* userPhoneName = [[UIDevice currentDevice] name];
7.获得设备名称
 
NSString* deviceName = [[UIDevice currentDevice] systemName];
8.地方型号（国际化区域名称）
 
NSString* localPhoneModel = [[UIDevice currentDevice] localizedModel];



***
设置响应区域
-(BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch；

设置具体时间
- (void)handle_tap:(UITapGestureRecognizer *)recognizer
{
    CGPoint point = [recognizer locationInView:_headerView];
    if (CGRectContainsPoint(CGRectMake(SCREEN_WIDTH-scale_device_value(100), 0, scale_device_value(100), scale_device_value(100)), point)) {
        [self removeTip];
    }
}

***
支持简单手势返回上层页面，其他的可以用addTarget自定义方法
要加在第0位
self.navigationController.interactivePopGestureRecognizer.delegate = self;
    self.navigationController.interactivePopGestureRecognizer.enabled = true;

***
UITableViewCell使用cell和cell.contentView的区别
1、addSubview
在UITableViewCell实例上添加子视图，有两种方式：[cell  addSubview:view]或[cell.contentView addSubview:view],一般情况下，两种方式没有区别。但是在多选编辑状态，直接添加到cell上的子视图将不会移动，而添加在contentView上的子视图会随着整体右移。所以，推荐使用[cell.contentView addSubview:view]方式添加子视图。




2、backgroundColor
cell.backgroundColor = [UIColor grayColor];或cell.contentView.backgroudColor = [UIColor grayColor];一般情况下，两种方式效果一样。但是在多选编辑状态，直接设置cell的背景色可以保证左侧多选框部分的背景色与cell背景色一致，而设置contentView背景色，左侧多选框的背景色会是UITableView的背景色或UITableView父视图背景色，如果需要保证颜色一致，必须设置cell的背景色而不是cell.contentView的。
***
调整字间距
加入CoreText.framework 头导入#import <CoreText/CoreText.h>
 NSMutableAttributedString *attributedString =[[NSMutableAttributedString alloc]initWithString:time];
    long number = 2.0f;//间距
    CFNumberRef num = CFNumberCreate(kCFAllocatorDefault,kCFNumberSInt8Type,&number);
    [attributedString addAttribute:(id)kCTKernAttributeName value:(__bridge id)num range:NSMakeRange(0,[attributedString length])];
    CFRelease(num);
    _title.attributedText = attributedString;
***


日期型字符串排序
***
补充CPU、Energy impact知识
找出app运行一段时间后，手机过热的具体原因

***
iOS发布APP 报错 "No .app bundles found in the package"：
10.12下 用Xcode8自带的工具进行提交，亲测没有问题
***
友盟QQ平台分享utf8encoding 以及 http前不允许有空格 1.会报错 2.无法弹出正常的分享页面
***
iOS 交互测试
工具：新建一个Target-》UI Test
参考文档：

***
iOS 常用数学函数  

http://blog.163.com/gf_zl818/blog/static/83808068201262551444777/
http://www.cnblogs.com/Mr-Lin/archive/2016/08/24/5802763.html
***
iOS高效调试
http://mp.weixin.qq.com/s?__biz=MjM5OTM0MzIwMQ==&mid=2652547598&idx=1&sn=4a0b6c7bc7feb892ec28b6b67b27ab85&chksm=bcd2ef008ba5661676aed9f71eae901234b5b77f4a530dc0078b711ee3222c1e7859708ed165&scene=0#wechat_redirect
***
"Missing Push Notification Entitlement"警告-----以及解决方法
http://www.cnblogs.com/chongyangcui/p/5022364.html

***
　　　升到iOS10之后，需要设置权限的有：

麦克风权限：Privacy - Microphone Usage Description 是否允许此App使用你的麦克风？
相机权限： Privacy - Camera Usage Description 是否允许此App使用你的相机？
相册权限： Privacy - Photo Library Usage Description 是否允许此App访问你的媒体资料库？
通讯录权限： Privacy - Contacts Usage Description 是否允许此App访问你的通讯录？
蓝牙权限：Privacy - Bluetooth Peripheral Usage Description 是否许允此App使用蓝牙？
语音转文字权限：Privacy - Speech Recognition Usage Description 是否允许此App使用语音识别？
日历权限：Privacy - Calendars Usage Description 
定位权限：Privacy - Location When In Use Usage Description 
定位权限: Privacy - Location Always Usage Description 
位置权限：Privacy - Location Usage Description
媒体库权限：Privacy - Media Library Usage Description
健康分享权限：Privacy - Health Share Usage Description
健康更新权限：Privacy - Health Update Usage Description
运动使用权限：Privacy - Motion Usage Description
音乐权限：Privacy - Music Usage Description
提醒使用权限：Privacy - Reminders Usage Description
Siri使用权限：Privacy - Siri Usage Description
电视供应商使用权限：Privacy - TV Provider Usage Description
视频用户账号使用权限：Privacy - Video Subscriber Account Usage Description
***
状态栏高度20    导航栏高度44
***
使用第三方库TPKeyboardAvoidingScrollView出现的bug 解决方案：
http://blog.csdn.net/wenhaiwang/article/details/46472567
***
Undefined symbols for architecture i386：新版QQ去除了i386架构，直接在真机上测试就可以了
qq互联sdk  支持cpu指令集版本由arm7、arm7s、arm64、i386 、x86_64 变更到 armv7、x86_64、arm64
由于最新QQ sdk支持的CPU指令集调整，最新5.2.1版本支持的CPU架构为：armv7\x86_64\arm64，不再支持i386架构，各位开发者注意不要在架构支持中勾选i386或在模拟器调试！！！
***
枚举 switch 
***
 UILabel调整字间距
 1、引入 CoreText.framework
2\

#import <CoreText/CoreText.h>
2\

```

NSMutableAttributedString *attributedString =[[NSMutableAttributedString alloc]initWithString:@"你的字符串"];  
    long number = 5.0f;//间距  
    CFNumberRef num = CFNumberCreate(kCFAllocatorDefault,kCFNumberSInt8Type,&number);  
    [attributedString addAttribute:(id)kCTKernAttributeName value:(__bridge id)num range:NSMakeRange(0,[attributedString length])];  
    CFRelease(num);  
     [ColumnIntroduce setAttributedText:attributedString];
       
```
***
```


- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
    if (application.applicationState == UIApplicationStateActive) {
        NSLog(@"active");
        //程序当前正处于前台
        [application setApplicationIconBadgeNumber:0];
        
        NSString *messageAlert = [[userInfo objectForKey:@"aps"]objectForKey:@"alert"];  
        UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:@"远程通知" message:messageAlert delegate:nil cancelButtonTitle:@"取消" otherButtonTitles:@"前往", nil];
        alertView.delegate = self;

        [alertView show];
        // 8秒后将退出弹框
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(8.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertView dismissWithClickedButtonIndex:0 animated:YES];

        });
    }
else if(application.applicationState == UIApplicationStateInactive)
    {
        NSLog(@"inactive");
        //程序处于后台
       
        
        [[NSNotificationCenter defaultCenter]postNotificationName:@"123456" object:nil userInfo:userInfo];
        
    }
    // IOS 7 Support Required
    [APService handleRemoteNotification:userInfo];
    completionHandler(UIBackgroundFetchResultNewData);
}
```

```

在你首页控制器的添加通知，并且跳转到每个控制器
- （void）viewdidload
{
    [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(pushNotification:) name:@"123456" object:nil];
    
}
- (void)pushNotification:(NSNotification *)notification
{
    NSDictionary *dict = notification.userInfo;
    
    // kpiid  其实就是后台传出来需要点击cell的id
    GTLog(@"===notification===%@",[dict valueForKey:@"kpiid"]);
    
    [self selectKPIModuleId:[[dict valueForKey:@"kpiid"] integerValue]];
}
```
```

/************************************************
*
*
*  点击相对应的kpiid模块，跳转到相对应的kpi控制器
*
*
************************************************/
- (void)selectKPIModuleId:(int)kpiid     // 此处是tableview  didselect方法中单独提取的方法
{
    
    //销售利润
    if(kpiid==1){
        ProfitViewController *pyc=[[ProfitViewController alloc] initWithNibName:@"ProfitViewController" bundle:nil];
        [(UINavigationController *)self.mm_drawerController.centerViewController pushViewController:pyc animated:YES];
        [[TimeTool sharedTimeTool] timeToolBTypeOfStartWhenIntoViewCurrentModuleID:1];
        return;
    }

```

***
UIViewController self.view 位置问题：
translucent：判断是否覆盖内容
  self.tabBarController.tabBar.translucent = true;
  self.navigationController.navigationBar.translucent = true;

***
UITableView 留白问题
self.automaticallyAdjustsScrollViewInsets = NO;
看这个UIViewController的这个属性你就明白了，此属性默认为YES，这样UIViewController下如果只有一个UIScollView或者其子类，那么会自动留出空白，让scollview滚动经过各种bar下面时能隐约看到内容。但是每个UIViewController只能有唯一一个UIScollView或者其子类，如果超过一个，需要将此属性设置为NO,自己去控制留白以及坐标问题。
***
记录：
自定义了TabBarController 之后必须实现以下

-(void)viewWillAppear:(BOOL)animated
{
    [self.selectedViewController beginAppearanceTransition: YES animated: animated];
}

-(void) viewDidAppear:(BOOL)animated
{
    [self.selectedViewController endAppearanceTransition];
}

-(void) viewWillDisappear:(BOOL)animated
{
    [self.selectedViewController beginAppearanceTransition: NO animated: animated];
}

-(void) viewDidDisappear:(BOOL)animated
{
    [self.selectedViewController endAppearanceTransition];
} 
***
向字典2对象中添加整个字典对象3

[dic2 addEntriesFromDictionary:dic3];

将空字典1对象内容设置与字典2对象相同

[dic1 setDictionary:dic2];


***
Xcode 快捷键：
Command 1~ 8: 跳转到导航区的不同位置

Command 0 :显示/隐藏导航区
Command Alt 0: 显示/关闭工具区.
Command + Shift + Y:显示/隐藏调试区

Command Alt 1~ 6:在不同检测器之间跳转
Control Command Alt 1~4: 在不同库之间跳转
Control 1~ 6: 在Jump bar的不同标签页的跳转。


Command + Enter: 显示标准单窗口编辑器
Command Alt Enter:打开Assistant editor
Command Alt Shift Enter: 打开版本控制编辑器

Command + Shift + J，可展示当前你在工程导航器中打开的文件
Cmd + . 方便地暂停运行iOS模拟器

变量、类或者方法名 Option+双击 跳转到Xcode提供的文档
Option + Left点击

集体缩进 cmd+ { 或 }
