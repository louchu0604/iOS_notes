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
