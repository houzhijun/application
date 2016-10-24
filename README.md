# application
iPhone应用程序是由主函数main启动，它负责调用UIApplicationMain函数，该函数的形式如下所示：  int UIApplicationMain (  int argc,  char *argv[],  NSString *principalClassName,  NSString *delegateClassName  );  那么UIApplicationMain函数到底做了哪些事情呢？
这个函数主要负责三件 事情：   
1）从给定的类名初始化应用程序对象，也就是初始化UIApplication或者子类对象的一个实例，如果你在这里给定的是nil，那么 系统会默认UIApplication类，也就主要是这个类来控制以及协调应用程序的运行。在后续的工作中，你可以用静态方法sharedApplication 来获取应用程序的句柄。    
2）从给定的应用程序委托类，初始化一个应用程序委托。并把该委托设置为应用程序的委托，这里就有如果传入参数为nil，会调用函数访问 Info.plist文件来寻找主nib文件，获取应用程序委托。    
3）启动主事件循环，并开始接收事件。

上面是UIApplicationMain函数的工作，接下来一个问题是应用程序视图的显示、消息的控制怎么办？下面就是UIApplication（或 者子类）对象的职责，这个对象主要做下面几件事：    
1）负责处理到来的用户事件，并分发事件消息到应该处理该消息的目标对象（sender,  action)。  
2）管理以及控制视图，包括呈现、控制行为、当前显示视图等。  
3）该对象有一个应用程序委托对象，当一些生命周期内重要事件（可以包括系统事件或者生命周期控制事件）发生时，应用程序通知该对象。例如，应用程序启 动、内存不够了或者应用程序结束等，让这些事件发生时，应用程序委托去响应。   通 过上面的分析，可以知道UIApplication对开发者来说，是一个黑箱，它也可以是。因为所有的操作，都可以由它的委托来帮我们完成，它只需要在 后面维护一些不可更改的东西，如事件消息分发和传递、给委托发送事件处理请求等等，如，应用程序加载处理完毕，它会发送消息给委托，然后委托可以在 applicationDidFinishLanching委托函数中去实现开发者想要的动作。

利用XCODE在创建应用程序时，会默认实现一个应用程序 委托类。而对于加载的视图，则有视图相关的委托类来处理视图加载过程的生命事件。下面说明委托主要可以办哪些事情：  控制应用程序的行为    
- (void)applicationDidFinishLaunching:(UIApplication *)application            应用程序启动完毕。 
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions         当由于其它方法打开应用程序（如URL指定或者连接），通知委托启动完毕  
- (void)applicationWillTerminate:(UIApplication *)application           通知委托，应用程序将在关闭 退出，请做一些清理工作。  
- (void)applicationDidReceiveMemoryWarning:(UIApplication *)application          通知委托，应用程序收到了为来自系统的内存不足警告。
-(void)applicationSignificantTimeChange:(UIApplication *)application        通知委托系统时间发生改变（主要是指时间属性，而不是具体的时间值）  
打开URL  
- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url             打开指定的URL  
控制状态栏方位变化  
– application:willChangeStatusBarOrientation:duration:          
设备方向将要发生改变  
– application:didChangeStatusBarOrientation:  活动状态改变  
- (void)applicationWillResignActive:(UIApplication *)application     通知委托应用程序将进入非活动状态，在此期间，应用程序不接收消息或事件。
-(void)applicationDidBecomeActive:(UIApplication *)application        通知委托应用程序进入活动状态，请恢复数据  
1.设置icon上的数字图标      //设置主界面icon上的数字图标，在2.0中引进， 缺省为0     [UIApplicationsharedApplication].applicationIconBadgeNumber = 4;  
2.设置摇动手势的时候，是否支持redo,undo操作     //摇动手势，是否支持redo undo操作。    //3.0以后引进，缺省YES     [UIApplicationsharedApplication].applicationSupportsShakeToEdit =YES;  
3.判断程序运行状态      //判断程序运行状态，在2.0以后引入        if([UIApplicationsharedApplication].applicationState ==UIApplicationStateInactive){         NSLog(@"程序在运行状态");     }  
4.阻止屏幕变暗进入休眠状态     //阻止屏幕变暗，慎重使用,缺省为no 2.0     [UIApplicationsharedApplication].idleTimerDisabled =YES;  慎重使用本功能，因为非常耗电。  
5.显示联网状态      //显示联网标记 2.0     [UIApplicationsharedApplication].networkActivityIndicatorVisible =YES;  
6.在map上显示一个地址     NSString* addressText =@"1 Infinite Loop, Cupertino, CA 95014";    // URL encode the spaces     addressText =  [addressTextstringByAddingPercentEscapesUsingEncoding:NSASCIIStringEncoding];    NSString* urlText = [NSStringstringWithFormat:@"http://maps.google.com/maps?q=%@", addressText];         [[UIApplicationsharedApplication]openURL:[NSURLURLWithString:urlText]];  
7.发送电子邮件     NSString *recipients =@"mailto:first@example.com?cc=second@example.com,third@example.com&amp;subject=Hello from California!";    NSString *body =@"&amp;body=It is raining in sunny California!";         NSString *email = [NSStringstringWithFormat:@"%@%@", recipients, body];     email = [emailstringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];         [[UIApplicationsharedApplication]openURL:[NSURLURLWithString:email]];  
8.打电话到一个号码     // Call Google 411     [[UIApplicationsharedApplication]openURL:[NSURLURLWithString:@"tel://8004664411"]];  
9.发送短信      // Text to Google SMS     [[UIApplicationsharedApplication]openURL:[NSURLURLWithString:@"sms://466453"]];  10.打开一个网址     // Lanuch any iPhone developers fav site     [[UIApplicationsharedApplication]openURL:[NSURLURLWithString:@"http://itunesconnect.apple.com"]];   可以看到UIApplication的头文件实现  @interface UIApplication :UIResponder &lt;UIActionSheetDelegate>{  @package  id&lt;UIApplicationDelegate> _delegate ;  //这就是应用程序委托。  NSTimer .......  }  因此，在UIApplication中处理的系统事件时，只需转到_delegate这个类去处理， 这个类对象就是应用程序委托对象。我们可以从应用程序的单例类对象中得到应用程序委托的对象  UIApplicationDelegate* myDelegate = [[UIApplication sharedApplication] delegate];   UIApplication 接收到所有的系统事件和生命周期事件时，都会把事件传递给UIApplicationDelegate进行处理，对于用户输入 事件，则传递给相应的目标对象去处理。比如我们在应用程序被来电等消息后，可以调用应用程序委托类的 applicationWillResignActive（）方法，这个方法在用户锁住屏幕时，也会调用，与之相适应的是应用程序重新被用户打开时的委托 方法。
另外常用的就是内存不足的系统警告，此时会调用应用程序委托类的applicationDidReceiveMemoryWarning()方法， 然后我们就可以试着释放一些内存了。   
上面就是应用程序生命周期（启动，中止，恢复，退出等过程）的应用程序处理UIApplication sharedApplication       
IOS应用程序生命周期 UIViewController的生命周期程序的生命周期是指应用程序启动到应用程序结束整个 阶段的全过程 每一个IOS应用程序都包含一个UIApplication对象， IOS系统通过该UIApplication对象监控应用程序生命周 期全过程 每一个IOS应用程序都要为其UIApplication对象指定一 个代理对象，并由该代理对象处理UIApplication对象监 测到的应用程序生命周期事件。
IOS应用程序5种状态 Not running:应用还没有启动，或者应用正在运行但是途中被系 统停止 Inactive:当前应用正在前台运行，但是并不接收事件（当前 或许 正在执行其它代码）。一般每当应用要从一个状态切换到另一个不 同的状态时，中途过渡会短暂停留在此状态。唯一在此状态停留时 间比较长的情况是：当用户 锁屏时，或者系统提示用户去响应某 些（诸如电话来电、有未读短信等）事件的时候。 Active:当前应用正在前台运行，并且接收事件。这是应用正在前 台运行时所处的正常状态。 Background:应用处在后台，并且还在执行代码。大多数将 要进 入Suspended状态的应用，会先短暂进入此状态。然而，对于请求 需要额外的执行时间的应用，会在此状态保持更长一段时间。
另外， 如果一个应用要 求启动时直接进入后台运行，这样的应用会直接 从Not running状态进入Background状态，中途不会经过Inactive状 态。比如没有界面的应用。注此处并不特指没有界面的应用，其实 也可以是 有界面的应用，只是如果要直接进入background状态的 话，该应用界面不会被显示。 Suspended:应用处在后台，并且已停止执行代码。系统自动 的 将应用移入此状态，且在此举之前不会对应用做任何通知。当处在 此状态时，应用依然驻留内存但不执行任何程序代码。当系统发生 低内存告警时，系统将会将处 于Suspended状态的应用清除出内 存以为正在前台运行的应用提供足够的内存。 
创建UIApplication对象并指定其代理 通过UIApplicationMain函数创建UIApplication对象并 指定其代理对象AppDelegate;第三个参数为指定 UIApplication的子类来生成UIApplication对象，为nil时由 UIApplication类初始化默认对象；第四个参数为指定代理 对象。 UIApplication的代理对象 作为UIApplication的代理类，必须要先实现 UIApplicationDelegate协议，协议里明确了作为代理应 该做或可以做哪些事情。 UIApplication对象负责监听应用程序的生命周期事件， 并将生命周期事件交由UIApplication代理对象处理。 
UIApplication代理对象生命周期函数详解 
- (void)applicationWillResignActive:(UIApplication *)application 说明：当应用程序将要入非活动状态执行，在此期间，应用 程 序不接收消息或事件，比如来电话了 
- (void)applicationDidBecomeActive:(UIApplication *)application 说明：当应用程序入活动状态执行，这个刚好跟上面那个方 法相反 
- (void)applicationDidEnterBackground:(UIApplication *)application 说明：当程序被推送到后台的时候调用。所以要设置后台继 续运行，则在这个函数里面设置即可 UIApplication代理对象生命周期函数详解 
- (void)applicationWillEnterForeground:(UIApplication *)application 说明：当程序从后台将要重新回到前台时候调用，这个刚好 跟上面的那个方法相反。 
- (void)applicationWillTerminate:(UIApplication *)application 说明：当程序将要退出是被调用，通常是用来保存数据和一 些退出前的清理工作。这个需要要设置 UIApplicationExitsOnSuspend的键值 (void)applicationDidReceiveMemoryWarning:(UIApplic ation *)application 说明：ios设备只有有限的内存，如果为应用程序分配了太多 内存操作系统会终止应用程序的运行，在终止前会执行这个 方法，通常可以在这里进行内存清理工作防止程序被终止 UIApplication代理对象生命周期函数详解 (void)applicationDidFinishLaunching:(UIApplication*)a pplication 说明：当程序载入后执行。 
- (BOOL)application:(UIApplication*)application handleOpenURL:(NSURL*)url 说明：当打开URL时执行。 UIViewController UIViewController是IOS顶层视图的载体及控制器，用户 与程序界面的交互都是由UIViewController来控制的。 UIViewController管理UIView的生命周期及资源的加载 与释放。 UIView UIView与UIWindow共同展示了应用用户界面。 
UIViewController生命周期事件 
-(void)loadView 加载视图资源并初始化视图 
- (void)viewDidLoad 
- (void)viewDidUnload 释放视图资源 
- (void)viewWillAppear:(BOOL)animated 将要加载出视图 
- (void)viewDidAppear:(BOOL)animated 视图出现 
- (void)viewWillDisappear:(BOOL)animated 视图即将消失 (void)viewDidDisappear:(BOOL)animated 视图已经消失
