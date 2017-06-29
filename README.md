# 20170627
1. Object-c的类可以多 继承么 可以实现多个接 么 Category是 么  写 个类的 式 继承 好还是分类好 为 么
答: Object-c的类 可以多 继承;可以实现多个接 ，通过实现多个接 可以完成C++的多 继承
Category是类别， 般情况下 Category去 写类的 法，仅对本Category有效， 会影响到其 他类与原有类的关系，如果是要在 修改原有类的基础上增加其他原有类没有的 法，就要 类别
虽然OC在语法上禁 类使 多继承，但是在协议的遵守上却允许使 多继承。所以可以 协议来实 现多继承。但是协议只能提供接 ， 没有提供实现 式，如果只是想多继承基类的接 ，那么遵 守多协议 疑是最好的 法， 既需要多继承接 ， 要多继承其实现，那么协议是 能为  
2. #import 跟#include   么区别，@class呢, #import< 跟 #import””  么区别 答:#import是Objective-C导 头 件的关键字，#include是C/C++导 头 件的关键字,使  #import头 件会 动只导  次， 会 复导 ，相当于#include和#pragma once;@class告诉编 译 某个类的声明，当执 时，才去查看类的实现 件，可以解决头 件的相互包含;#import<   来包含系统的头 件，#import”” 来包含 户头 件。
3. 属性readwrite，readonly，assign，retain，copy，nonatomic 各是 么作 ，在那种情况下  答:
- 在多线程环境下，原 操作是必要的，否则有可能引起错误的结果。加 atomic，setter函数会 变成下 这样:
{lock}
if (property != newValue) { [property release];
property = [newValue retain]; }
{unlock}
nonatomic
禁 多线程，变 保护，提 性能。
- atomic是Objc使 的 种线程保护技术，基本上来讲，是防 在写未完成的时候被另外 个线程 读取，造成数据错误。 这种机制是耗费系统资源的，所以在iPhone这种 型设备上，如果没有使  多线程间的通讯编程，那么nonatomic是 个 常好的选择。
- 指出访问  是原 操作， 默认地，访问 是原 操作。这也就是说，在多线程环境下，解析 的访问 提供 个对属性的安全访问，从获取 得到的返回值或者通过设置 设置的值可以 次完 成，即 是别的线程也正在对其进 访问。如果你 指定 nonatomic ，在  管 内存的环境中，
 解析的访问 保 并 动释放返回的值，如果指定  nonatomic ，那么访问 只是简单地返回这个 值。
- nonatomic 原 操作，决定编译  成的setter getter是否为原 操作，atomic表示多线程安 全(防 在写未完成的时候被另 个线程读取，造成数据错误，但使 同步锁的开销太 会严 影 响性能)， 般使 nonatomic;
- atomic并 能保证线程安全，若要实现线程安全的操作，还需采  深层次的锁定机制才可以。  如， 个线程在连续多次读取某属性值的过程中 有别的线程在同时改写此值，那么即使将属性 声明为atomic，也还是会读到 同的属性值，iOS程序中 般都会使 nonatomic属性 但在开发 Mac OSX程序时 使 atomic属性 般 会有 么性能瓶颈
readwrite可读可写属性  成getter setter 法
readonly只读属性  会 成setter 法; 希望属性在类外发 改变
assign是赋值特性，简单赋值，  改索引计数，setter 法将传 参数赋值给实 变 ;仅设 置变 时
retain表示持有特性，setter 法将传 参数先保 在赋值，传 参数的retaincount+1
copy是赋值特性，setter 法将传 对象复制 份
4.写 个setter 法 于完成@property (nonatomic,retain)NSString *name,写 个setter 法 于 完成@property(nonatomic，copy)NSString *name
答:
- (void) setName:(NSString*) str {
[str retain];
[name release];
name = str;
}
- (void)setName:(NSString *)str
{
id t = [str copy];
[name release];
name = t;
}
5.对于语 NSString*obj = [[NSData alloc] init]; obj在编译时和运 时分别时 么类型的对象 答: 编译时是NSString的类型;运 时是NSData类型的对象 6.常 的object-c的数据类型有那些， 和C的基本数据类型有 么区别 如:NSInteger和int 答:object-c的数据类型有NSString，NSNumber，NSArray，NSMutableArray，NSData等等， 这些都是class，创建后 是对象， C语 的基本数据类型int，只是 定字节的内存空间， 于存 放数值;NSInteger是基本数据类型，并 是NSNumber的 类，当然也 是NSObject的 类。
 NSInteger是基本数据类型Int或者Long的别名(NSInteger的定义typedef long NSInteger)，它的区 别在于，NSInteger会根据系统是32位还是64位来决定是本身是int还是Long。
7.id 声明的对象有 么特性
答:Id 声明的对象具有运 时的特性，即可以指向任意类型的objcetive-c的对象; 8.Objective-C如何对内存管 的,说说你的看法和解决 法 答:Objective-C的内存管 主要有三种 式ARC( 动内存计数)、 动内存计数、内存池。
1). (Garbage Collection) 动内存计数:这种 式和Java类似，在你的程序的执 过程中。始终有  个  在背后准确地帮你收 垃圾，你  考虑它 么时候开始 作，怎样 作。你只需要明 ， 我申请  段内存空间，当我 再使 从 这段内存成为垃圾的时候，我就彻底的把它忘记掉，反 正那个  会帮我收 垃圾。遗憾的是，那个  需要消耗 定的资源，在携带设备  ，资源是 紧俏商品所以iPhone  持这个功能。所以“Garbage Collection” 是本  指南的范围，对 “Garbage Collection”内部机制感兴趣的同学可以参考 些其他的资 ， 过说 实话“Garbage Collection”  适合适初学者研究。
解决: 通过alloc – initial 式创建的, 创建后引 计数+1, 此后每retain 次引 计数+1, 那么在程序 中做相应次数的release就好 .
2). (Reference Counted) 动内存计数:就是说，从 段内存被申请之后，就存在 个变  于保 存这段内存被使 的次数，我们暂时把它称为计数 ，当计数 变为0的时候，那么就是释放这段内 存的时候。 如说，当在程序A   段内存被成功申请完成之后，那么这个计数 就从0变成1(我 们把这个过程叫做alloc)，然后程序B也需要使 这个内存，那么计数 就从1变成 2(我们把这个过 程叫做retain)。紧接着程序A 再需要这段内存 ，那么程序A就把这个计数 减1(我们把这个过程 叫做release);程序B也 再需要这段内存的时候，那么也把计数 减1(这个过程还是release)。当系 统(也就是Foundation)发现这个计数 变 成员 0，那么就会调 内存回收程序把这段内存回收(我 们把这个过程叫做dealloc)。顺 提  ，如果没有Foundation，那么维护计数 ，释放内存等等  作需要你  来完成。
解决: 般是由类的静态 法创建的, 函数名中 会出现alloc或init字样, 如[NSString string]和 [NSArray arrayWithObject:], 创建后引 计数+0, 在函数出栈后释放, 即相当于 个栈上的局部变  . 当然也可以通过retain延 对象的 存期.
3). (NSAutoRealeasePool)内存池:可以通过创建和释放内存池控制内存申请和回收的时机. 解决:是由autorelease加 系统内存池, 内存池是可以嵌套的, 每个内存池都需要有 个创建释放对, 就像main函数中写的 样. 使 也很简单,  如[[[NSString alloc]initialWithFormat:@”Hey you!”] autorelease], 即将 个NSString对象加 到最内层的系统内存池, 当我们释放这个内存池时, 其中的 对象都会被释放.
9. 原 (atomic)跟 原 (non-atomic)属性有 么区别
答:
1). atomic提供多线程安全。是防 在写未完成的时候被另外 个线程读取，造成数据错误
2). non-atomic:在  管 内存的环境中，解析的访问 保 并 动释放返回的值，如果指定  nonatomic ，那么访问 只是简单地返回这个值。
10. 看下 的程序,第 个NSLog会输出 么 这时str的retainCount是多少 第 个和第三个呢 为  么
NSMutableArrayarr= [[NSMutableArray array] retain];+1 
 NSStringstr= [NSString stringWithFormat:@"test"];
 [str retain];+1
 [arr addObject:str];+1
 NSLog(@"%@%d", str, [str retainCount]);
 [str retain]; +1
 [str release];-1
 
 [str release];-1
 NSLog(@"%@%d", str, [str retainCount]);
 [arr removeAllObjects]; -1
 NSLog(@"%@%d", str, [str retainCount]);
  str的retainCount创建+1，retain+1，加 数组 动+1 3
retain+1，release-1，release-1 2
数组删除所有对象，所有数组内的对象 动-1 1
11. 内存管 的 条原则时 么 按照默认法则.那些关键字 成的对象需要 动释放 在和property 结合的时候怎样有效的避免内存泄 
答:谁申请，谁释放
遵循Cocoa Touch的使 原则; 内存管 主要要避免“过早释放”和“内存泄 ”，对于“过早释放”需要注意@property设置特性时，  定要 对特性关键字，对于“内存泄 ”， 定要申请 要负责释放，要细 。
关键字alloc 或new  成的对象需要 动释放; 设置正确的property属性，对于retain需要在合适的地 释放，
- mrc
- NSAutorelease
- 内存管 的 条原则
- 谁申请谁释放
- 遵循CocoaTouch的使 原则
- 内存管 主要要避免过早释放和内存泄 ，对于过早释放是要注意@property 设置特性时， 对
特性关键字，对于内存泄 ，有增有减，申请 要负责释放
- 关键字alloc或new 成的对象需要 动释放
- 设置正确的property属性，对于retain需要在合适的地 释放
- 内存分析
内存分析的主要 的就是为 检测程序是否存在内存泄  ###静态内存分析(Analyze)
- 作 :
逻辑错误:访问未初始化的变 ,  指针等; 声明错误:从未使 过的对象; 内存管 错误:如内存泄 等;
- 分析 法:
 ! Objective-C内存管  - arc
##
 静态内存分析是 运 程序,直接对代码进 分析.
根据代码的上下 的语法结构,来分析是否有内存泄  - 缺点:
  定准确,但是如果发现有提示,那么去结合上下 看 下,这 的代码是否有问题
- 场景演练:
MRC 下桥接 - Foundation 和 CoreFoundation框架的数据类型转换 ARC 下桥接 - Foundation 和 CoreFoundation框架的数据类型转换
###内存分配 - 作 :
查看是内存的分配情况
查看内存是否有释放 - 场景演示:
UIImage 的两种创建 法测试 imageNamed: imageWithContentOfFile:
###动态内存分析 - 作 :
检测程序在运 过程中是否存在内存泄  - 场景演示:
模拟循环引 , 测试内存泄 
 12.如何对iOS设备进 性能测试
答: Profile- Instruments - Time Profiler 测试iOS版的 App 注意事项分享以下 点:
- app使 过程中，接听电话。可以测试 同的通话时间的 短，对于通话结束后，原先打开的app 的响应， 如是否停 在原先界 ，继续操作时的相应速度等。
- app使 过程中，有推送消息时，对app的使 影响
- 设备在充电时，app的响应以及操作流畅度
- 设备在 同电 时(低于10%，50%，95%)，app的响应以及操作流畅度
- 意外断电时，app数据丢失情况
-  络环境变化时，app的应对情况如何:是否有适当提示?从有 络环境到  络环境时，app的 反馈如何?从  络环境回到有 络环境时，是否能 动加载数据，多久才能开始加载数据
- 多点触摸的情况
- 跟其他app之间互相切换时的响应
- 进程关闭再 新打开的反馈
- iOS系统语 环境变化时
平时 第三 服务的话可以去DevStore(开发者服务商店)找，集成 很多的第三 服务，有服务 评测还可以源码下载，是个 错的 具 站。
13. Object C中创建线程的 法是 么 如果在主线程中执 代码， 法是 么 如果想延时执 代 码、 法 是 么 答:线程创建有三种 法:使 NSThread创建、使 GCD的dispatch、使  类化的 NSOperation,然后将其加 NSOperationQueue;在主线程执 代码， 法是 performSelectorOnMainThread，如果想延时执 代码可以  performSelector:onThread:withObject:waitUntilDone:
14. MVC设计模式是 么 你还熟悉 么设计模式
答: 设计模式:并 是 种新技术， 是 种编码经验，使  如java中的接 ，iphone中的协议，继 承关系等基本 段，  较成熟的逻辑去处 某 种类型的事情，总结为所谓设计模式。 向对象 编程中，java已经归纳 23种设计模式。
 mvc设计模式 :模型，视图，控制 ，可以将整个应 程序在思想上分成三 块，对应是的数据的 存储或处 ，前台的显示，业务逻辑的控制。 Iphone本身的设计思想就是遵循mvc设计模式。其  属于23种设计模式范畴。 代 模式:代 模式给某 个对象提供 个代 对象，并由代 对象控制对源对象的引 . 如 个    产 产品，并 想直接卖给 户， 是搞 很多代 商， 户可以直接找代 商买东 ，代  商从  进货.常 的如QQ的 动回复就属于代 拦截，代 模式在iphone中得到 泛应 . 单 模式:说  就是 个类 通过alloc 式创建对象， 是  个静态 法返回这个类的对象。 系统只需要拥有 个的全局对象，这样有 于我们协调系统整体的 为， 如想获得[UIApplication sharedApplication];任何地 调 都可以得到 UIApplication的对象，这个对象是全局唯 的。 观察者模式: 当 个物体发 变化时，会通知所有观察这个物体的观察者让其做出反应。实现起来   就是把所有观察者的对象给这个物体，当这个物体的发 改变，就会调 遍历所有观察者的对 象调 观察者的 法从 达到通知观察者的 的。
  模式:
public class Factory{
public static Sample creator(int which){
if (which==1)
return new SampleA();
else if (which==2)
return new SampleB();
}
}
15 浅复制和深复制的区别
答:浅层复制:只复制指向对象的指针，  复制引 对象本身。
深层复制:复制引 对象本身。 意思就是说我有个A对象，复制 份后得到A_copy对象后，对于浅复制来说，A和A_copy指向的是 同 个内存资源，复制的只 过是是 个指针，对象本身资源 还是只有 份，那如果我们对A_copy执  修改操作,那么发现A引 的对象同样被修改，这其实违 背 我们复制拷 的 个思想。深复制就好 解 ,内存中存在 
两份独 对象本身。
  上 哥们通俗的话将就是:
浅复制好 你和你的影 ，你完蛋，你的影 也完蛋 深复制好 你和你的克  ，你完蛋，你的克  还活着。
16. 类别的作  继承和类别在实现中有何区别
答:category 可以在 获悉， 改变原来代码的情况下往  添加新的 法，只能添加， 能删除 修改，并且如果类别和原来类中的 法产 名称冲突，则类别将覆盖原来的 法，因为类别具有   的优先级。
类别主要有3个作 :
1).将类的实现分散到多个 同 件或多个 同框架中。
2).创建对私有 法的前向引 。
3).向对象添加 正式协议。
继承可以增加，修改或者删除 法，并且可以增加属性。
- 分类中也可以声明属性，但这种做法要尽 避免
- 通过“class-continuation分类”向类中新增实 变 。
 - 如果某属性在主接 中声明为“只读”， 类的内部 要 设置 法修改此属性，那么就在 “class-continuation分类”中将其扩展为“可读写”。
- 把私有 法的原型声明在“class-continuation分类”  。
- 若想使 类所遵循的协议 为 所知，则可于“class-continuation分类”中声明。
- “class-continuation分类”和普通的分类 同，它必须定义在其所接续的那个类的实现 件 。 其 要之处在于，这是唯 能声明实 变 的分类， 且此分类没有特定的实现 件，其中的 法 应该定义在类的主实现 件 。与其他分类 同，“class-continuation分类”没有名字。 如:
@interface EOCPerson() //methods here
@end
- 私有 法 能在其他类或 类中使 ，如强制使 ，Xcode编译 直接报错。
- 如果 要在其他类中调 私有 法，需要给MyClass类添加 个分类(Category)，在分类.h中
写上该声 明，分类.m中  写实现。在要 的类中同时导 类.h和分类.h即可使 。这就是传说中的私有 
法的前向引 。
- 对于以下情况1， 法使 类别，须使 继承。 1)新扩展的 法与原 法同名，但是还需要使  类的实现。因为使 类别，会覆盖原类的实现，  法访问到原来的 法。
2)扩展类的属性，这个类别 法做到。如果 继承的话就 类扩展
17. 类别和类扩展的区别。
答:category和extensions的 同在于 后者可以添加属性。另外后者添加的 法是必须要实现的。 extensions可以认为是 个私有的Category。
18. oc中的协议和java中的接 概念有何 同
答:OC中的代 有2层含义，官 定义为 formal和informal protocol。前者和Java接  样。 informal protocol中的 法属于设计模式考虑范畴， 是必须实现的，但是如果有实现，就会改变 类的属性。 其实关于正式协议，类别和 正式协议我很早前学习的时候 致看过，也写在 学习教程  “ 正式协议概念其实就是类别的另 种表达 式“这 有 些你可能希望实现的 法，你可以使  他们 好的完成 作”。 这个意思是，这些是可选的。 如我 要 个 好的 法，我们就会申明 个这样的类别去实现。 然后你在后期可以直接使 这些 好的 法。 这么看，总觉得类别这玩意 有点像协议的可选协议。” 现在来看，其实protocal已经开始对两者都统 和规范起来操作，因为资 中说“ 正式协议使  interface修饰“， 现在我们看到协议中两个修饰词:“必须实现(@requied)”和“可选实现(@optional)”。
19.  么是KVO和KVC
 答:KVC:键 – 值编码是 种间接访问对象的属性使 字符 来标识属性，  是通过调 存取  法，直接或通过实 变 访问的机制。 很多情况下可以简化程序代码。apple 档其实给  个很好的  。 KVO:键值观察机制，他提供 观察某 属性变化的 法，极 的简化 代码。 具体 看到嗯哼 到过的 个地 是对于按钮点击变化状态的的监控。  如我 定义的 个button
[self addObserver:self forKeyPath:@"highlighted" options:0 context:nil];
#pragma mark KVO
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change: (NSDictionary *)change context:(void *)context
{
if ([keyPath isEqualToString:@"highlighted"] ) {
[self setNeedsDisplay];
}
}
对于系统是根据keypath去取的到相应的值发 改变， 论上来说是和kvc机制的道 是 样的。 对于kvc机制如何通过key寻找到value:
“当通过KVC调 对象时， 如:[self valueForKey:@”someKey”]时，程序会 动试图通过 种  同的 式解析这个调 。 先查找对象是否带有 someKey 这个 法，如果没找到，会继续查找对 象是否带有someKey这个实 变 (iVar)，如果还没有找到，程序会继续试图调  -(id) valueForUndefinedKey:这个 法。如果这个 法还是没有被实现的话，程序会抛出 个 NSUndefinedKeyException异常错误。
(cocoachina.com注:Key-Value Coding查找 法的时候， 仅仅会查找someKey这个 法，还会 查找getsomeKey这个 法，前 加 个get，或者_someKey以及_getsomeKey这 种形式。同时， 查找实 变 的时候也会 仅仅查找someKey这个变 ，也会查找_someKey这个变 是否存在。) 设计valueForUndefinedKey: 法的主要 的是当你使 -(id)valueForKey 法从对象中请求值时， 对象能够在错误发 前，有最后的机会响应这个请求。这样做有很多好处，下 的两个  说明  这样做的好处。“
来 cocoa，这个说法应该挺有道 。 因为我们知道button却是存在 个highlighted实 变 .因此为何上 我们只是add 个相关的 keypath就  ，
可以按照kvc查找的逻辑 解，就说的过去 。
20. 代 的作  答:代 的 的是改变或传递控制链。允许 个类在某些特定时刻通知到其他类，  需要获取到 那些类的指针。可以减少框架复杂度。 另外 点，代 可以 解为java中的回调监听机制的 种类似。
21. oc中可修改和 可以修改类型。 答:可修改 可修改的集合类。这个我个 简单 解就是可动态添加修改和 可动态添加修改 样。
 如NSArray和NSMutableArray。前者在初始化后的内存控件就是固定 可变的，后者可以添加等， 可以动态申请新的内存空间。
22. 我们说的oc是动态运 时语 是 么意思
答:多态。 主要是将数据类型的确定由编译时，推迟到 运 时。 这个问题其实浅涉及到两个概念，运 时和多态。
 简单来说，运 时机制使我们直到运 时才去决定 个对象的类别，以及调 该类别对象指定 法。
多态: 同对象以  的 式响应相同的消息的能 叫做多态。意思就是假设 物类(life)都 有  个相同的 法-eat; 那 类属于 物，猪也属于 物，都继承 life后，实现各 的eat，但是调 是我们只需调 各  的eat 法。
也就是 同的对象以  的 式响应 相同的消息(响应 eat这个选择 )。 因此也可以说，运 时机制是多态的基础 ~~~
23. 通知和协议的 同之处
答:协议有控制链(has-a)的关系，通知没有。  先我 开始也 太明 ， 么叫控制链(专业术语 ~)。但是简单分析下通知和代 的 为模式， 我们 致可以有  的 解 简单来说，通知的话，它可以 对多， 条消息可以发送给多个消息接受者。 代 按我们的 解，到 是直接说 能 对多， 如我们知道的明星经济代  ，很多时候 个经 济 负责好 个明星的事务。 只是对于 同明星间，代 的事物对象都是  样的，  对应， 可能说明天要处 A明星要 个 发布会，代  发出处 发布会的消息后，别称B的 发布会 。但是通知就  样，他只关 发出通知，  关 多少接收到感兴趣要处 。 因此控制链(has-a从英语单词 致可以看出，单 拥有和可控制的对应关系。
24.  么是推送消息
答:推送通知 是 种技术。
简单点就是客户端获取资源的 种 段。
普通情况下，都是客户端主动的pull。
推送则是服务 端主动push。 测试push的实现可以查看该博 。
25. 关于多态性
答:多态， 类指针可以赋值给 类。
这个题 其实可以出到 切 向对象语 中， 因此关于多态，继承和封装基本最好都有个 我意识的 解，也并  定要把书上资 上写的能背 出来
26. 对于单 的 解
答:在objective-c中要实现 个单 类， 少需要做以下四个步骤: 1).为单 对象实现 个静态实 ，并初始化，然后设置成nil， 2).实现 个实 构造 法检查上 声明的静态实 是否为nil，如果是则新建并返回 个本类的实 ，
3). 写allocWithZone 法， 来保证其他 直接使 alloc和init试图获得 个新实 的时候 产   个新实 ，
4).适当实现allocWitheZone，copyWithZone，release和autorelease。
27. 说说响应链
答: 事件响应链。包括点击事件，画 刷新事件等。在视图栈内从上 下，或者从下之上传播。 可以说点事件的分发，传递以及处 。具体可以去看下touch事件这块。因为问的太抽象化  严 怀疑题 出到越后 就越笼统。 可以从责任链模式，来讲通过事件响应链处 ，其拥有的扩展性
28. frame和bounds有 么 同 答:frame指的是:该view在 view坐标系统中的位置和  。(参照点是 亲的坐标系统) bounds指的是:该view在本身坐标系统中 的位置和  。(参照点是本身坐标系统)
 29.  法和选择 有何 同 答:selector是 个 法的名字，method是 个组合体，包含 名字和实现. 详情可以看apple 档。
30. OC的垃圾回收机制
答: OC2.0有Garbage collection，但是iOS平台 提供。  般我们 解的objective-c对于内存管 都是 动操作的，但是也有 动释放池。 但是差  部分资 ，貌似 要和arc机制搞混就好 。
31. NSOperation queue
答:存放NSOperation的集合类。 操作和操作队 ，基本可以看成java中的线程和线程池的概念。 于处 ios多线程开发的问题。  上部分资 提到 点是，虽然是queue，但是却并 是带有队 的概念，放 的操作并 是按照 严格的先进现出。 这边 有个疑点是，对于队 来说，先进先出的概念是Afunc添加进队 ，Bfunc紧跟着也进 队 ， Afunc先执 这个是必然的， 但是Bfunc是等Afunc完全操作完以后，B才开始启动并且执 ，因此队 的概念离乱上有点违背  多线程处 这个概念。
但是转念 想其实可以参考银 的取票和叫号系统。 因此对于A B先排队取票但是B率先执 完操作，我们亦然可以感性认为这还是 个队 。 但是后来看到 票关于这操作队 话题的 章，其中有  提到 “因为两个操作提交的时间间隔很近，线程池中的线程，谁先启动是 定的。” 瞬间觉得这个queue名字有点忽悠  ，还 如pool~ 综合 点，我们知道他可以 较 的 处在于可以帮组多线程编程就好 。
32.  么是延迟加载
答:懒汉模式，只在 到的时候才去初始化。
也可以 解成延时加载。
我觉得最好也最简单的 个  就是tableView中图 的加载显示 。  个延时载，避免内存过 ， 个异步加载，避免线程堵塞。
33. 是否在 个视图控制 中嵌 两个tableview控制  答: 个视图控制只提供  个View视图， 论上 个tableViewController也 能放吧， 只能说可以嵌  个tableview视图。当然，题 本身也有歧义，如果 是我们定性思维认为的 UIViewController， 是宏观的表示视图控制者，那我们倒是可以把其看成 个视图控制者，它可以 控制多个视图控制 ， 如TabbarController那样的感觉。
34.  个tableView是否可以关联两个 同的数据源 你会怎么处  答: 先我们从代码来看，数据源如何关联上的，其实是在数据源关联的代  法 实现的。 因此我们并 关 如何去关联他，他怎么关联上， 法只是让我返回根据  的需要去设置如相关 的数据源。
因此，我觉得可以设置多个数据源啊，但是有个问题是，你这是想 嘛呢 想让 表如何显示， 同 的数据源分区块显示
35.  么时候使 NSMutableArray， 么时候使 NSArray 答:当数组在程序运 时，需要 断变化的，使 NSMutableArray，当数组在初始化后，  再改 变的，使 NSArray。需要指出的是，使 NSArray只表明的是该数组在运 时 发 改变，即 能 往NSAarry的数组 新增和删除元素，但 表明其数组內的元素的内容 能发 改变。NSArray是线 程安全的，NSMutableArray 是线程安全的，多线程使 到NSMutableArray需要注意。
36. 给出委托 法的实 ，并且说出UITableVIew的Data Source 法
 答:CocoaTouch框架中 到   委托，其中UITableViewDelegate就是委托机制的典型应 ，是  个典型的使 委托来实现适配 模式，其中UITableViewDelegate协议是 标，tableview是适配  ，实现UITableViewDelegate协议，并将 身设置为talbeview的delegate的对象，是被适配 ，  般情况下该对象是UITableViewController。
UITableVIew的Data Source 法有- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath: (NSIndexPath *)indexPath;
37. 在应 中可以创建多少autorelease对象，是否有限制
答案: 
38. 如果我们 创建内存池，是否有内存池提供给我们 答:界 线程维护着  的内存池， 户  创建的数据线程，则需要创建该线程的内存池 39.  么时候需要在程序中创建内存池 答: 户  创建的数据线程，则需要创建该线程的内存池
40. 类NSObject的那些 法经常被使  答:NSObject是Objetive-C的基类，其由NSObject类及 系 协议构成。 其中类 法alloc、class、 description 对象 法init、dealloc、– performSelector:withObject:afterDelay:等经常被使 
41.  么是简 构造 法
答:简 构造 法 般由CocoaTouch框架提供，如NSNumber的 + numberWithBool: + numberWithChar: + numberWithDouble: + numberWithFloat: + numberWithInt: Foundation下 部分类均有简 构造 法，我们可以通过简 构造 法，获得系统给我们创建好的 对象，并且 需要 动释放。
42. 如何使 Xcode设计通 应  答:使 MVC模式设计应 ，其中Model层完成脱离界 ，即在Model层，其是可运 在任何设备 上，在controller层，根据iPhone与iPad(独有UISplitViewController)的 同特点选择 同的 viewController对象。在View层，可根据现实要求，来设计，其中以xib 件设计时，其设置其为 universal。
43. UIView的动画效果有那些
答:有很多，如 UIViewAnimationOptionCurveEaseInOut UIViewAnimationOptionCurveEaseIn UIViewAnimationOptionCurveEaseOut UIViewAnimationOptionTransitionFlipFromLeft UIViewAnimationOptionTransitionFlipFromRight UIViewAnimationOptionTransitionCurlUpUIViewAnimationOptionTransitionCurlDown
44. 在iPhone应 中如何保存数据
答:有以下 种保存机制:
1).通过web服务，保存在服务 上
2).通过NSCoder固化机制，将对象保存在 件中
3).通过SQlite或CoreData保存在 件数据库中
45.  么是coredata
答:coredata是苹果提供 套数据保存框架，其基于SQlite
46.  么是NSManagedObject模型
答:NSManagedObject是NSObject的 类 ，也是coredata的 要组成部分，它是 个通 的类, 实现 core data 模型层所需的基本功能， 户可通过 类化NSManagedObject，建   的数据 模型。
47.  么是NSManagedobjectContext
 答:NSManagedobjectContext对象负责应 和数据库之间的交互。
48.  么是谓词 答:谓词是通过NSPredicate，是通过给定的逻辑条件作为约束条件，完成对数据的筛选。 1
2
predicate = [NSPredicate predicateWithFormat:@"customerID == %d",n];
a = [customers filteredArrayUsingPredicate:predicate];
49. 和coredata 起有哪 种持久化存储机制
答:存 到 件、 存 到NSUserDefaults(系统plist 件中)、存 到Sqlite 件数据库
50. 谈谈对Block 的 解 并写出 个使 Block执 UIVew动画 答:Block是可以获取其他函数局部变 的 名函数，其 但  开发，并且可以 幅提 应 的执  效率(多核 CPU可直接处 Block指令)
[UIView transitionWithView:self.view
duration:0.2
options:UIViewAnimationOptionTransitionFlipFromLeft
animations:^{ [[blueViewController view] removeFromSuperview]; [[self view] insertSubview:yellowViewController.view atIndex:0]; }
completion:NULL];
51. 写出上 代码的Block的定义。
答:
1
2
typedef void(^animations) (void);
typedef void(^completion) (BOOL finished);
52. 试着使 + beginAnimations:context:以及上述Block的定义，写出 个可以完成
1
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options: (UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^) (BOOL finished))completion NS_AVAILABLE_IOS(4_0);
操作的函数执 部分
答案: 
53. 做过的项 是否涉及 络访问功能，使  么对象完成 络功能 答:ASIHTTPRequest与NSURLConnection
54. 简单介绍下NSURLConnection类及+ sendSynchronousRequest:returningResponse:error: 与– initWithRequest:delegate:两个 法的区别
答: NSURLConnection主要 于 络访问，其中+ sendSynchronousRequest:returningResponse:error:是同步访问数据，即当前线程会阻塞，并等待 request的返回的response， – initWithRequest:delegate:使 的是异步加载，当其完成 络访问 后，会通过delegate回到主线程，并其委托的对象。
55. 多线程是 么
答: 多线程是个复杂的概念，按字 意思是同步完成多项任务，提  资源的使 效率，从硬件、操 作系统、应 软件 同的 度去看，多线程被赋予 同的内涵，对于硬件，现在市 上多数的CPU 都是多核的，多核的CPU运算多线程 为出 ;从操作系统 度，是多任务，现在 的主流操作系统 都是多任务的，可以 边听歌、 边写博客;对于应 来说，多线程可以让应 有 快的回应，可以
 在 络下载时，同时响应 户的触摸操作。在iOS应 中，对多线程最初的 解，就是并发，它的含 义是原来先做烧 ，再摘菜，再炒菜的 作，会变成烧 的同时去摘菜，最后去炒菜。
56. iOS 中的多线程
答: iOS中的多线程，是Cocoa框架下的多线程，通过Cocoa的封装，可以让我们 为  的使 线 程，做过C++的同学可能会对线程有 多的 解， 如线程的创 ，信号 、共享变 有认识， Cocoa框架下会  很多，它对线程做 封装，有些封装，可以让我们创建的对象，本身 拥有线 程，也就是线程的对象化抽象，从 减少我们的 程，提供程序的健壮性。
GCD是(Grand Central Dispatch)的缩写 ，从系统级别提供的 个  地多线程类库，具有运 时 的特点，能充分  多核 硬件。GCD的API接 为C语 的函数，函数参数中多数有Block，关于 Block的使 参看这 ，为我们提供强 的“接 ”，对于GCD的使 参 本  NSOperation与Queue NSOperation是 个抽象类，它封装 线程的细节实现，我们可以通过 类化该对象，加上NSQueue 来同 向对象的思维，管 多线程程序。具体可参看这 : 个基于NSOperation的多线程 络访 问的项 。
NSThread NSThread是 个控制线程执 的对象，它 如NSOperation抽象，通过它我们可以  的得到 个 线程，并控制它。但NSThread的线程之间的并发控制，是需要我们  来控制的，可以通过 NSCondition实现。
参看 iOS多线程编程之NSThread的使 
其他多线程
在Cocoa的框架下，通知、Timer和异步函数等都有使 多线程，(待补充).
57. 在项  么时候选择使 GCD， 么时候选择NSOperation
答:
1. GCD是底层的C语 构成的API， NSOperationQueue及相关对象是Objc的对象。在GCD中，在 队 中执 的是由block构成的任务，这是 个轻 级的数据结构; Operation作为 个对象，为 我们提供  多的选择;
2. 在NSOperationQueue中，我们可以随时取消已经设定要准备执 的任务(当然，已经开始的任务 就 法阻  )， GCD没法停 已经加 queue的block(其实是有的，但需要许多复杂的代码);
3. NSOperation能够  地设置依赖关系，我们可以让 个Operation依赖于另 个Operation，这 样的话尽管两个Operation处于同 个并 队 中，但前者会直到后者执 完毕后再执 ;
4. 我们能将KVO应 在NSOperation中，可以监听 个Operation是否完成或取消，这样 能 GCD  加有效地掌控我们执 的后台任务;
5. 在NSOperation中，我们能够设置NSOperation的priority优先级，能够使同 个并 队 中的任 务区分先后地执 ， 在GCD中，我们只能区分 同任务队 的优先级，如果要区分block任务的优 先级，也需要  的复杂代码;
6. 我们能够对NSOperation进 继承，在这之上添加成员变 与成员 法，提 整个代码的复 度， 这 简单地将block任务排 执 队  有 由度，能够在其之上添加 多 定制的功能。
7. GCD 是严格的队 ，先进先出 FIFO;NSOperation可以改动 优先级(或者说服务质 )改变执  顺序 NSOperation的 级:最 并发数，控制线程个数，优化 线程的暂停、继续、取消功能(GCD实 现起来太难，可以  KVO )，依赖关系，可以让异步任务同步执  项 中使 NSOperation的优点是NSOperation是对线程的 度抽象，在项 中使 它，会使项  的程序结构 好， 类化NSOperation的设计思 ，是具有 向对象的优点(复 、封装)，使得实现 是多线程 持， 接 简单，建议在复杂项 中使 。
 项 中使 GCD的优点是GCD本身 常简单、  ，对于 复杂的多线程操作，会节省代码 ，  Block参数的使 ，会是代码 为 读，建议在简单项 中使 。
58.  么是block
答: 对于闭包(block),有很多定义，其中闭包就是能够读取其它函数内部变 的函数，这个定义即接 近本质 较好 解。对于刚接触Block的同学，会觉得有些绕，因为我们习惯写这样的程序main(){ funA();} funA(){funB();} funB(){.....}; 就是函数main调 函数A，函数A调 函数B... 函数们依次顺 序执 ，但现实中 全是这样的， 如项 经 M， 下有3个程序员A、B、C，当他给程序员A安 排实现功能F1时，他并 等着A完成之后，再去安排B去实现F2， 是安排给A功能F1，B功能F2，C 功能F3，然后可能去写技术 档， 当A遇到问题时，他会来找项 经 M，当B做完时，会通知M， 这就是 个异步执 的  。在这种情形下，Block 可 显身 ，因为在项 经 M，给A安排  作时，同时会告诉A若果遇到困难，如何能找到他报告问题( 如打他 机号)，这就是项 经 M给 A的 个回调接 ，要回掉的操作， 如接到电话，百度查询后，返回  内容给A，这就是 个 Block，在M交待 作时，已经定义好，并且取得 F1的任务号(局部变 )，却是在当A遇到问题时， 才调 执 ，跨函数在项 经 M查询百度，获得结果后回调该block。
59. block 实现原 
答: Objective-C是对C语 的扩展，block的实现是基于指针和函数指针。 从计算语 的发展，最早的goto， 级语 的指针，到 向对象语 的block，从机 的思维， 步 步接近 的思维，以  开发 员 为 效、直接的描述出现实的逻辑(需求)。
使 实 
cocoaTouch框架下动画效果的Block的调 
使 typed声明block
1
2
typedef void(^didFinishBlock) (NSObject *ob);
这就声明  个didFinishBlock类型的block，
然后 可 
1
@property (nonatomic,copy) didFinishBlock finishBlock; 声明 个blokc对象，注意对象属性设置为copy，接到block 参数时， 会 动复制 份。 __block是 种特殊类型， 使 该关键字声明的局部变 ，可以被block所改变，并且其在原函数中的值会被改变。 60.关于block
答:  试时， 试官会先问 些，是否 解block，是否使 过block，这些问题相当于开场 ，往往 是下  系 问题的开始，所以 定要如实根据  的情况回答。
1). 使 block和使 delegate完成委托模式有 么优点  先要 解 么是委托模式，委托模式在iOS中  应 ，其在设计模式中是适配 模式中的对象适 配 ，Objective-C中使 id类型指向 切对象，使委托模式 为简洁。 解委托模式的细节: iOS设计模式—-委托模式 使 block实现委托模式，其优点是回调的block代码块定义在委托对象函数内部，使代码 为紧凑; 适配对象 再需要实现具体某个protocol，代码 为简洁。
2). 多线程与block
GCD与Block
使  dispatch_async 系  法，可以以指定的 式执 block
GCD编程实 
dispatch_async的完整定义
 void dispatch_async(
dispatch_queue_t queue,
dispatch_block_t block); 功能:在指定的队  提交 个异步执 的block， 阻塞当前线程 通过queue来控制block执 的线程。主线程执 前 定义的 finishBlock对象 1
dispatch_async(dispatch_get_main_queue(),^(void){finishBlock();}); 62.谈谈Object-C的内存管  式及过程
答: 1).当你使 new,alloc和copy 法创建 个对象时,该对象的保 计数 值为1.当你 再使 该对 象时,你要负责向该对象发送 条release或autorelease消息.这样,该对象将在使 寿命结束时被销毁. 2).当你通过任何其他 法获得 个对象时,则假设该对象的保 计数 值为1, 且已经被设置为 动 释放,你 需要执 任何操作来确保该对象被清 .如果你打算在 段时间内拥有该对象,则需要保 它 并确保在操作完成时释放它. 3).如果你保  某个对象,你需要(最终)释放或 动释放该对象.必须保持retain 法和release 法的 使 次数相等.
63.Object-C有私有 法吗 私有变 呢
答: objective-c – 类  的 法只有两种, 静态 法和实  法. 这似乎就 是完整的 向对象 , 按照OO的原则就是 个对象只  有 的东 . 如果没有 私有 法的话, 对于 些 范围的代码   就 那么顺  . 在类  声名 个私有 法
@interface Controller : NSObject { NSString *something; } + (void)thisIsAStaticMethod;
– (void)thisIsAnInstanceMethod;
@end
@interface Controller (private) -
(void)thisIsAPrivateMethod;
@end
@private可以 来修饰私有变  在Objective‐C中，所有实 变 默认都是私有的，所有实  法默认都是公有的 64.Object-C有多继承吗 没有的话  么代替 cocoa 中所有的类都是NSObject 的 类 答: 多继承在这 是 protocol 委托代  来实现的
你  去考虑繁琐的多继承 ,虚基类的概念.
ood的多态特性 在 obj-c 中通过委托来实现.
65.内存管  Autorelease、retain、copy、assign的set 法和含义
答: 1).你初始化(alloc/init)的对象，你需要释放(release)它。 如:
NSMutableArray aArray = [[NSArray alloc] init]; 后，需要 [aArray release]; 2).你retain或copy的，你需要释放它。 如:
[aArray retain] 后，需要 [aArray release]; 3).被传递(assign)的对象，你需要斟酌的retain和release。 如:
obj2 = [[obj1 someMethod] autorelease]; 对象2接收对象1的 个 动释放的值，或传递 个基本数据类型(NSInteger，NSString)时:你或希 望将对象2进 retain，以防 它在被使 之前就被 动释放掉。但是在retain后， 定要在适当的 时候进 释放。
关于索引计数(Reference Counting)的问题
 retain值 = 索引计数(Reference Counting) NSArray对象会retain(retain值加 )任何数组中的对象。当NSArray被卸载(dealloc)的时候，所有数 组中的对象会 被 执  次释放(retain值减 )。 仅仅是NSArray，任何收集类(Collection Classes) 都执 类似操作。 如 NSDictionary，甚 UINavigationController。 Alloc/init建 的对象，索引计数为1。 需将其再次retain。
[NSArray array]和[NSDate date]等“ 法”建  个索引计数为1的对象，但是也是 个 动释放对 象。所以是本地临时对象，那么 所谓 。如果是打算在全Class中使 的变 (iVar)，则必须retain 它。
缺省的类 法返回值都被执  “ 动释放” 法。(*如上中的NSArray) 在类中的卸载 法“dealloc”中，release所有未被平衡的NS对象。(*所有未被autorelease，  retain值为1的)
66. C和obj-c 如何混 
答: 1).obj-c的编译 处 后缀为m的 件时，可以识别obj-c和c的代码，处 mm 件可以识别obj- c,c,c++代码，但cpp 件必须只能 c/c++代码， 且cpp 件include的头 件中，也 能出现obj- c的代码，因为cpp只是cpp
2).在mm 件中混 cpp直接使 即可，所以obj-c混cpp 是问题 3).在cpp中混 obj-c其实就是使 obj-c编写的模块是我们想要的。 如果模块以类实现，那么要按照cpp class的标准写类的定义，头 件中 能出现obj-c的东 ，包 括#import cocoa的。实现 件中，即类的实现代码中可以使 obj-c的东 ，可以import,只是后缀 是mm。 如果模块以函数实现，那么头 件要按c的格式声明函数，实现 件中，c++函数内部可以 obj-c， 但后缀还是mm或m。
总结:只要cpp 件和cpp include的 件中 包含obj-c的东 就可以  ，cpp混 obj-c的关键 是使 接 ，  能直接使  实现代 码，实际上cpp混 的是obj-c编译后的o 件，这个东 其实 是 差别的，所以可以 。obj-c的编译  持cpp
67. Objective-C堆和栈的区别
答: 管  式:对于栈来讲，是由编译  动管 ， 需我们  控制;对于堆来说，释放 作由程 序员控制，容 产 memory leak。
申请  : 栈:在Windows下,栈是向低地址扩展的数据结构，是 块连续的内存的区域。这 话的意思是栈顶 的地址和栈的最 容 是系统预先规定好的，在 WINDOWS下，栈的  是2M(也有的说是1M， 总之是 个编译时就确定的常数)，如果申请的空间超过栈的剩余空间时，将提示overflow。因 此， 能从栈获得的空间较 。 堆:堆是向 地址扩展的数据结构，是 连续的内存区域。这是由于系统是 链表来存储的空闲内 存地址的， 然是 连续的， 链表的遍历 向是由低地址向 地址。堆的  受限于计算机系统 中有效的虚拟内存。由此可 ，堆获得的空间 较灵活，也 较 。 碎 问题:对于堆来讲，频繁的new/delete势必会造成内存空间的 连续，从 造成  的碎 ， 使程序效率降低。对于栈来讲，则 会存在这个问题，因为栈是先进后出的队 ，他们是如此的   对应，以 于永远都 可能有 个内存块从栈中间弹出 分配 式:堆都是动态分配的，没有静态分配的堆。栈有2种分配 式:静态分配和动态分配。静态 分配是编译 完成的， 如局部变 的分配。动态分配由alloca函数进 分配，但是栈的动态分配和 堆是 同的，他的动态分配是由编译 进 释放， 需我们  实现。 分配效率:栈是机 系统提供的数据结构，计算机会在底层对栈提供 持:分配专 的寄存 存放 栈的地址，压栈出栈都有专 的指令执 ，这就决定 栈的效率 较 。堆则是C/C++函数库提供 的，它的机制是很复杂的。
 68. ViewController的didReceiveMemoryWarning怎么被调 :
答:[supper didReceiveMemoryWarning]; 69. 么时候 delegate, 么时候 Notification
答: delegate针对one-to-one关系， 于sender接受到reciever的某个功能反馈值。 notification针对one-to-one/many/none,reciver, 于通知多个object某个事件。 70. 预处 指令#define声明 个常数， 以表明1 中有多少秒(忽 闰 问题) 答:
#define SECONDS_PER_YEAR (60 * 60 * 24 * 365)UL
我在这想看到 件事情:
#define 语法的基本知识( 如: 能以分号结束，括号的使 ，等等) 懂得预处  将为你计算常数表达式的值，因此，直接写出你是如何计算  中有多少秒  是计 算出实际的值，是 清晰 没有代价的。 意识到这个表达式将使 个16位机的整型数溢出-因此要 到 整型符号L,告诉编译 这个常数是的  整型数。 如果你在你的表达式中 到UL(表示 符号 整型)，那么你有  个好的起点。记住，第 印象 很 要。
71.写 个”标准"宏MIN ，这个宏输 两个参数并返回较 的 个。
答:
1
#define MIN(A,B) ((A) <= (B) (A) : (B))
这个测试是为下 的 的 设的: 标识#define在宏中应 的基本知识。这是很 要的，因为直到嵌 (inline)操作符变为标准C的 部 分，宏是  产 嵌 代码的唯  
法，
对于嵌 式系统来说，为 能达到要求的性能，嵌 代码经常是必须的 法。 三 条件操作符的知识。这个操作符存在C语 中的原因是它使得编译 能产   if-then-else   优化的代码， 解这个 法是很 要的。
懂得在宏中  地把参数 括号括起来 我也 这个问题开始讨论宏的副作 ， 如:当你写下 的代码时会发  么事
1
least = MIN(*p++, b);
结果是:
1
((*p++) <= (b) (*p++) : (*p++))
这个表达式会产 副作 ，指针p会作三次++ 增操作。
72.关键字const有 么含意 修饰类呢 static的作 , 于类呢 还有extern c的作 
答:
const 意味着"只读"，下 的声明都是 么意思
const int a;
int const a;
const int *a;
int * const a;
int const * a const; 前两个的作 是 样，a是 个常整型数。
 第三个意味着a是 个指向常整型数的指针(也就是，整型数是 可修改的，但指针可以)。 第四个意思a是 个指向整型数的常指针(也就是说，指针指向的整型数是可以修改的，但指针是  可修改的)。 最后 个意味着a是 个指向常整型数的常指针(也就是说，指针指向的整型数是 可修改的，同时 指针也是 可修改的)。
结论: 关键字const的作 是为给读你代码的 传达 常有 的信息，实际上，声明 个参数为常 是为  告诉  户这个参数的应  的。 如果你曾花很多时间清 其它  下的垃圾，你就会很快学会感谢这点多余的信息。(当然，懂得  const的程序员很少会 下的垃圾让别 来清 的) 通过给优化  些附加的信息，使 关键字 const也许能产  紧凑的代码。合 地使 关键字const可以使编译 很 然地保护那些 希望被 改变的参数，防 其被 意的代码修改。简  之，这样可以减少bug的出现。 1).欲阻  个变 被改变，可以使  const 关键字。在定义该 const 变 时，通常需要对它进 初 始化，因为以后就没有机会再去改变它 ;
2).对指针来说，可以指定指针本身为 const，也可以指定指针所指的数据为 const，或 者同时指 定为 const;
3).在 个函数声明中，const 可以修饰形参，表明它是 个输 参数，在函数内部 能改变其值; 4).对于类的成员函数，若指定其为 const 类型，则表明其是 个常函数， 能修改类的成员变 ; 5).对于类的成员函数，有时候必须指定其返回值为 const 类型，以使得其返回值 为“左值”。
73. 关键字volatile有 么含意 并给出三个 同的  。
答: 个定义为 volatile的变 是说这变 可能会被意想 到地改变，这样，编译 就 会去假设这 个变 的值 。精确地说就是，优化 在 到这个变 时必须每次都  地 新读取这个变 的值，   是使 保存在寄存  的备份。
下 是volatile变 的 个  :
并 设备的硬件寄存 (如:状态寄存 )  个中断服务 程序中会访问到的  动变 (Non-automatic variables) 多线程应 中被 个任务共享的变 
74.  个参数既可以是const还可以是volatile吗  个指针可以是volatile 吗 解释为 么。 答:1).是的。 个  是只读的状态寄存 。它是volatile因为它可能被意想 到地改变。它是const 因为程序 应该试图去修改它。 2).是的。尽管这并 很常 。 个  是当 个中服务 程序修该 个指向 个buffer的指针时。 75 . static 关键字的作 :
答:
1).函数体内 static 变 的作 范围为该函数体， 同于 auto 变 ，该变 的内存只被分配 次， 因此其值在下次调 时仍维持上次的值;
2).在模块内的 static 全局变 可以被模块内所 函数访问，但 能被模块外其它函数访问; 3).在模块内的 static 函数只可被这 模块内的其它函数调 ，这个函数的使 范围被限制在声明 它的模块内;
4).在类中的 static 成员变 属于整个类所拥有，对类的所有对象只有 份拷 ;
5).在类中的 static 成员函数属于整个类所拥有，这个函数 接收 this 指针，因 只能访问类的 static 成员变 。
76. 线程与进程的区别和联系
答:
1). 进程和线程都是由操作系统所体会的程序运 的基本单元，系统  该基本单元实现系统对应  的并发性
 2). 进程和线程的主要差别在于它们是 同的操作系统资源管  式。
3). 进程有独 的地址空间， 个进程崩溃后，在保护模式下 会对其它进程产 影响， 线程只是  个进程中的 同执  径。 4.)线程有  的堆栈和局部变 ，但线程之间没有单独的地址空间， 个线程死掉就等于整个进程 死掉。所以多进程的程序要 多线程的程序健壮，但在进程切换时，耗费资源较 ，效率要差 些。
5). 但对于 些要求同时进 并且 要共享某些变 的并发操作，只能 线程， 能 进程。 77.  举 种进程的同步机制，并 较其优缺点。
答: 原 操作 信号 机制  旋锁 管程，会合，分布式系统
78. 进程之间通信的途径
答:共享存储系统消息传递系统管道:以 件系统为基础 79. 进程死锁的原因
答:资源竞争及进程推进顺序 法
80. 死锁的4个必要条件 答:互斥、请求保持、 可剥夺、环 
81. 死锁的处 
答:鸵 策 、预防策 、避免策 、检测与解除死锁
82. cocoa touch框架
答:iPhone OS 应 程序的基础 Cocoa Touch 框架   许多 Mac 系统的成熟模式，但是它 多 地专注于触摸的接 和优化。
UIKit 为您提供 在 iPhone OS 上实现图形，事件驱动程序的基本 具，其建 在和 Mac OS X 中  样的 Foundation 框架上，包括 件处 ， 络，字符 操作等。
Cocoa Touch 具有和 iPhone  户接  致的特殊设计。有  UIKit，您可以使  iPhone OS 上的 独特的图形接 控件，按钮，以及全屏视图的功能，您还可以使 加速仪和多点触摸 势来控制您 的应 。
各 俱全的框架 除 UIKit 外，Cocoa Touch 包含 创建世界 流 iPhone 应 程序需要的所有框 架，从三维图形，到专业 效，甚 提供设备访问 API 以控制摄像头，或通过 GPS 获知当前位置。
Cocoa Touch 既包含只需要  代码就可以完成全部任务的强 的 Objective-C 框架，也在需要 时提供基础的 C 语  API 来直接访问系统。这些框架包括:
Core Animation:通过 Core Animation，您就可以通过 个基于组合独 图层的简单的编程模型来 创建丰富的 户体验。
Core Audio:Core Audio 是播放，处 和录制 频的专业技术，能够轻松为您的应 程序添加强  的 频功能。
Core Data:提供  个 向对象的数据管 解决 案，它 于使 和 解，甚 可处 任何应 或  或 的数据模型。
功能 表:框架分类
下 是 Cocoa Touch 中  部分可 的框架:
 频和视频:Core Audio ，OpenAL ，Media Library ，AV Foundation 数据管  :Core Data ，SQLite
图形和动画 :Core Animation ，OpenGL ES ，Quartz 2D  络:Bonjour ，WebKit ，BSD Sockets
 户应 :Address Book ，Core Location ，Map Kit ，Store Kit
83.  动释放池是 么,如何 作
 答:当您向 个对象发送 个autorelease消息时，Cocoa就会将该对象的 个引 放 到最新的  动释放.它仍然是个正当的对象，因此 动释放池定义的作 域内的其它对象可以向它发送消息。当 程序执 到作 域结束的位置时， 动释放池就会被释放，池中的所有对象也就被释放。
84. Objective-C的优缺点。
答:objc优点:
1). Cateogies
2). Posing
3). 动态识别
4).指标计算
5).弹性讯息传递 6). 是 个过度复杂的 C 衍 语  7).Objective-C 与 C++ 可混合编程 objc缺点:
1).  援命名空间
2).  持运算符 载
3).  持多 继承 4).使 动态运 时类型，所有的 法都是函数调 ，所以很多编译时优化 法都  到。(如内联 函数等)，性能低劣。
85. sprintf,strcpy,memcpy使 上有 么要注意的地 。
答:
1). sprintf是格式化函数。将 段数据通过特定的格式，格式化到 个字符 缓冲区中去。sprintf格 式化的函数的 度 可控，有可能格式化后的字符 会超出缓冲区的  ，造成溢出。 2).strcpy是 个字符 拷 的函数，它的函数原型为strcpy(char *dst, const char *src 将src开始的 段字符 拷 到dst开始的内存中去，结束的标志符号为 ‘\0'，由于拷 的 度 是 由我们  控制的，所以这个字符 拷 很容 出错。
3). memcpy是具备字符 拷 功能的函数，这是 个内存拷 函数，它的函数原型为memcpy(char *dst, const char* src, unsigned int len);将 度为len的 段内存，从src拷 到dst中去，这个函数 的 度可控。但是会有内存叠加的问题。
86. readwrite，readonly，assign，retain，copy，nonatomic 属性的作  答:@property是 个属性访问声明，扩号内 持以下 个属性: 1).getter=getterName，setter=setterName，设置setter与 getter的 法名 2).readwrite,readonly，设置可供访问级别 2).assign，setter 法直接赋值， 进 任何retain操作，为 解决原类型与环循引 问题 3).retain，setter 法对参数进 release旧值再retain新值，所有实现都是这个顺序(CC上有相关资  )
4).copy，setter 法进 Copy操作，与retain处 流程 样，先旧值release，再 Copy出新的对象， retainCount为1。这是为 减少对上下 的依赖 引 的机制。 5).nonatomic， 原 性访问， 加同步，多线程并发访问会提 性能。注意，如果 加此属性， 则默认是两个访问 法都为原 型事务访问。锁被加到所属对象实 级。
87. http和scoket通信的区别。
答: http是客户端 http协议进 请求，发送请求时候需要封装http请求头，并绑定请求的数据， 服务  般有web服务 配合(当然也 绝对)。 http请求 式为客户端主动发起请求，服务 才 能给响应， 次请求完毕后则断开连接，以节省资源。服务  能主动给客户端响应(除 采取http  连接 技术)。iphone主要使 类是NSUrlConnection。
 scoket是客户端跟服务 直接使 socket“套接字”进 连接，并没有规定连接后断开，所以客户端 和服务 可以保持连接通道，双  都可以主动发送数据。 般在游戏开发或股票开发这种要求即时 性很强并且保持发送数据  较 的场合使 。主要使 类是CFSocketRef。
88. TCP和UDP的区别
答: TCP全称是Transmission Control Protocol，中 名为传输控制协议，它可以提供可靠的、  向连接的 络数据传递服务。传输控制协议主要包含下 任务和功能:
* 确保IP数据报的成功传递。
* 对程序发送的 块数据进 分段和 组。
* 确保正确排序及按顺序传递分段的数据。
* 通过计算校验和，进 传输数据的完整性检查。 TCP提供的是 向连接的、可靠的数据流传输， UDP提供的是  向连接的、 可靠的数据流传 输。
简单的说，TCP注 数据安全， UDP数据传输快点，但安全性 般
89. 你 解svn,cvs等版本控制 具么
答: 版本控制 svn,cvs 是两种版控制的 ,需要配套相关的svn，cvs服务 。 scm是xcode 配置版本控制的地 。版本控制的原 就是a和b同时开发 个项 ，a写完当天的代 码之后把代码提交给服务 ，b要做的时候先从服务 得到最新版本，就可以接着做。 如果a和b都 要提交给服务 ，并且同时修改 同 个 法，就会产 代码冲突，如果a先提交，那么b提交时， 服务 可以提示冲突的代码，b可以清晰的看到，并做出相应的修改或融合后再提交到服务 。
90.  么是push。
答: 客户端程序 下后 端 ，客户端总是监听针对这个后 的请求，于是 服务 可以主动像这个 端 推送消息。
91. 静态链接库 答:此为.a 件，相当于java 的jar包，把 些类编译到 个包中，在 同的 程中如果导 此  件就可以使   的类，具体使 依然是#import “ xx.h”。
92. fmmpeg框架
答:  视频编解码框架，内部使 UDP协议针对流媒体开发，内部开辟 六个端 来接受流媒体数 据，完成快速接受之 的。
93. fmdb框架 答:数据库框架，对sqllite的数据操作进  封装，使 着可把精 都放在sql语 上 。
94. 320框架
答: ui框架，导 320 程作为框架包如同添加 个普通框架 样。cover(open) flower框架 (2d 仿射技术)，内部核 类是CATransform3D.
94.  么是沙盒模型 哪些操作是属于私有api范畴 答:某个iphone 程进  件操作有此 程对应的指定的位置， 能逾越。 iphone沙箱模型的有四个 件夹documents，tmp，app，Library，永久数据存储 般放documents  件夹，得到模拟 的 径的可使 NSHomeDirectory() 法。Nsuserdefaults保存的 件在tmp  件夹 。
95. 在 个对象的 法  :self.name= “object”;和 name =”object” 有 么 同吗 答:self.name =”object”:会调 对象的setName() 法;
name = “object”:会直接把object赋值给当前对象的name属性。
96. 请简要说明viewDidLoad和viewDidUnload何时调  答:viewDidLoad在view从nib 件初始化时调 ，loadView在controller的view为nil时调 。此  法在编程实现view时调 ，view控制 默认会注册memory warning notification，当view
 controller的任何view没有 的时候，viewDidUnload会被调 ，在这 实现将retain的view release，如果是retain的IBOutlet view 属性则 要在这 release，IBOutlet会负责release 。 97. 简述内存分区情况
答:
1).代码区:存放函数 进制代码 2).数据区:系统运 时申请内存并初始化，系统退出时由系统释放。存放全局变 、静态变 、常  
3).堆区:通过malloc等函数或new等操作符动态申请得到，需程序员 动申请和释放 4).栈区:函数模块内申请，函数结束时由系统 动释放。存放局部变 、函数参数
98. 队 和栈有 么区别: 答:队 和栈是两种 同的数据容 。从”数据结构”的 度看，它们都是线性结构，即数据元素之 间的关系相同。 队 是 种先进先出的数据结构，它在两端进 操作， 端进  队 操作， 端进 出 队操作。
栈是 种先进后出的数据结构，它只能在栈顶进 操作， 栈和出栈都在栈顶操作。 99. HTTP协议中，POST和GET的区别是 么
答:
1).GET  法
GET  法提交数据 安全，数据置于请求 ，客户端地址栏可 ; GET  法提交的数据  有限
GET  法 可以设置书签
2).POST  法
POST  法提交数据安全，数据置于消息主体内，客户端 可  POST  法提交的数据  没有限制
POST  法可以设置书签
100. iOS的系统架构
答: iOS的系统架构分为( 核 操作系统层 theCore OS layer )、( 核 服务层theCore Services layer )、( 媒体层 theMedia layer )和( Cocoa 界 服务层 the Cocoa Touch layer )四个层次。
101. 控件主要响应3种事件
答:1). 基于触摸的事件 ; 2). 基于值的事件 ; 3).基于编辑的事件。
102. xib 件的构成分为哪3个图标 都具有 么功能。
答: File’s Owner 是所有 nib  件中的每个图标，它表示从磁盘加载 nib  件的对象;
First Responder 就是 户当前正在与之交互的对象;
View 显示 户界 ;完成 户交互;是 UIView 类或其 类。
103. 简述视图控件 的 命周期。
答: loadView 尽管 直接调 该 法，如多 动创建  的视图，那么应该覆盖这个 法并将它 们赋值给试图控制 的 view 属性。
viewDidLoad 只有在视图控制 将其视图载 到内存之后才调 该 法，这是执 任何其他初始化 操作的  。
viewDidUnload 当试图控制 从内存释放  的 法的时候调 ， 于清楚那些可能已经在试图控 制 中创建的对象。
viewVillAppear 当试图将要添加到窗 中并且还 可 的时候或者上层视图移出图层后本视图变成 顶级视图时调 该 法， 于执 诸如改变视图 向等的操作。实现该 法时确保调  [super viewWillAppear:
 viewDidAppear 当视图添加到窗 中以后或者上层视图移出图层后本视图变成顶级视图时调 ，  于放置那些需要在视图显示后执 的代码。确保调  [super viewDidAppear:] 。
104. 动画有基本类型有哪 种;表视图有哪 种基本样式。 答:动画有两种基本类型:隐式动画和显式动画。
105. 实现简单的表格显示需要设置UITableView的 么属性、实现 么协议 答:实现简单的表格显示需要设置 UITableView 的 dataSource 和 delegate 属性，实现 UITableViewDataSource 和 UITableViewDelegate 协议。
106. Cocoa Touch提供 哪 种Core Animation过渡类型
答: Cocoa Touch 提供  4 种 Core Animation 过渡类型，分别为:交叉淡化、推挤、显示和覆 盖。
107. UIView与CLayer有 么区别
答:
1).UIView 是 iOS 系统中界 元素的基础，所有的界 元素都是继承 它。它本身完全是由 CoreAnimation 来实现的。它真正的绘图部分，是由 个 CALayer 类来管 。 UIView 本身 像是  个 CALayer 的管  ，访问它的跟绘图和跟坐标有关的属性。
2).UIView 有个 要属性 layer ，可以返回它的主 CALayer 实 。
3).UIView 的 CALayer 类似 UIView 的  View 树形结构，也可以向它的 layer 上添加 layer ，来 完成某些特殊的表示。即 CALayer 层是可以嵌套的。
4).UIView 的 layer 树形在系统内部，被维护着三份 copy 。分别是逻辑树，这 是代码可以操纵的 ;动画树，是 个中间层，系统就在这 层上 改属性，进 各种渲染操作;显示树，其内容就是 当前正被显示在屏幕上得内容。
5).动画的运作:对 UIView 的 subLayer ( 主 Layer )属性进  改，系统将 动进 动画 成， 动画持续时间的缺省值似乎是 0.5 秒。
6).坐标系统: CALayer 的坐标系统  UIView 多  个 anchorPoint 属性，使 CGPoint 结构表 示，值域是 0~1 ，是个  值。这个点是各种图形变换的坐标原点，同时会 改 layer 的 position 的位置，它的缺省值是 {0.5,0.5} ，即在 layer 的中央。 7).渲染:当 新层，改变 能 即显示在屏幕上。当所有的层都准备好时，可以调  setNeedsDisplay  法来 绘显示。
8).变换:要在 个层中添加 个 3D 或仿射变换，可以分别设置层的 transform 或affineTransform 属性。
9).变形: Quartz Core 的渲染能 ，使 维图像可以被 由操纵，就好像是三维的。图像可以在  个三维坐标系中以任意 度被旋转，缩放和倾斜。 CATransform3D 的 套 法提供  些魔术般 的变换效果。
108. Quatrz 2D的绘图功能的三个核 概念是 么并简述其作 。 答:上下 :主要 于描述图形写 哪 ;
 径:是在图层上绘制的内容;
状态: 于保存配置变换的值、填充和轮廓， alpha 值等。
109. iPhone OS主要提供  种播放 频的 法
答: SystemSound Services
AVAudioPlayer 类
Audio Queue Services
OpenAL
110. 使 AVAudioPlayer类调 哪个框架、使 步骤
答: AVFoundation.framework
步骤:配置 AVAudioPlayer 对象;
 实现 AVAudioPlayer 类的委托 法; 控制 AVAudioPlayer 类的对象; 监控   平; 回放进度和拖拽播放。
111. 有哪 种 势通知 法、写清楚 法名
答: -(void)touchesBegan:(NSSet*)touchedwithEvent:(UIEvent*)event; -(void)touchesMoved:(NSSet*)touched withEvent:(UIEvent*)event; -(void)touchesEnded:(NSSet*)touchedwithEvent:(UIEvent*)event; -(void)touchesCanceled:(NSSet*)touchedwithEvent:(UIEvent*)event; 112. CFSocket使 有哪 个步骤。
答:创建 Socket 的上下 ;创建 Socket ;配置要访问的服务 信息;封装服务 信息;连接服务  ;
113. Core Foundation中提供 哪 种操作Socket的 法
答: CFNetwork 、 CFSocket 和 BSD Socket 。
114. 解析XML 件有哪 种 式
答:以 DOM  式解析 XML  件;以 SAX  式解析 XML  件;
115. ios 平台怎么做数据的持久化 coredata 和sqlite有 必然联系 coredata是 个关系型数据库吗 答:iOS 中可以有四种持久化数据的 式:属性 表(plist)、对象归档、 SQLite3 和 Core Data; core data 可以使你以图形界 的 式快速的定义 app 的数据模型，同时在你的代码中容 获取到 它。 coredata 提供 基础结构去处 常 的功能， 如保存，恢复，撤销和 做，允许你在 app 中继续创建新的任务。在使  core data 的时候，你  安装额外的数据库系统，因为 core data 使 内置的 sqlite 数据库。 core data 将你 app 的模型层放 到 组定义在内存中的数据对象。 coredata 会追踪这些对象的改变，同时可以根据需要做相反的改变， 如 户执 撤销命令。当 core data 在对你 app 数据的改变进 保存的时候， core data 会把这些数据归档，并永久性保 存。 mac os x 中sqlite 库，它是 个轻 级功能强 的关系数据引擎，也很容 嵌 到应 程序。 可以在多个平台使 ， sqlite 是 个轻 级的嵌 式 sql 数据库编程。与 core data 框架 同的 是， sqlite 是使 程序式的， sql 的主要的 API 来直接操作数据表。 Core Data  是 个关系型数 据库，也 是关系型数据库管 系统 (RDBMS) 。虽然 Core Dta  持SQLite 作为 种存储类型， 但它 能使 任意的 SQLite 数据库。 Core Data 在使 的过程种  创建这个数据库。 Core Data  持对 、对多的关系。
116. tableView 的  机制
答:UITableView 通过  单元格来达到节省内存的 的: 通过为每个单元格指定 个  标识符 (reuseIdentifier),即指定 单元格的种类,以及当单元格滚出屏幕时,允许恢复单元格以   .对于  同种类的单元格使  同的ID,对于简单的表格, 个标识符就够 .  先我们需要搞明 为 么要使   机制,它的原 是 么.  论是UITableView还是UICollectionView，都有许多需要显示的cell (item), 但是屏幕的  是有限 的， 次只能显示那么 个，如果我们把所有的数据全部都加载进去,暂时 看 到，就会 常浪费 内存.
那么该如何避免这种 必要的内存消耗呢 就是每次只显示屏幕能放得下的cell的数据，在 户滑动 屏幕的过程中，再去加载新的数据，于是就有 cell的  机制   机制实现 数据和显示的分离,并 会为每个要显示的数据都创建 个Cell, 般情况下只创建屏 幕可显示的最 的cell个数+1,每当有 个cell从屏幕消失，就将其放到缓存池中，如果有新的cell出 现，就去缓存池中取，如果缓存池中没有，再创建。
 这种机制下系统默认有 个可变数组 NSMutableArray* visiableCells,  来保存当前显示的cell. 还 有 个可变字典 NSMutableDictnery* reusableTableCells,  来保存可 复  的cell. 之所以 字 典是因为可  的cell有   种样式,我们需要根据它的reuseIdentifier(  标识符)来查找是否有 可  的该样式的cell.
  的写法如下:
//设置单元格(cell) indexPath :单元格当前所在位置-哪 组的哪  
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath: (NSIndexPath *)indexPath
{ //定义  标识
static NSString *identifier = @"cell" ;
//identifier: 因为 个表视图中可能存在多种样式的单元格(cell),所以要相同样式的单元格放 到同 个集合  ,并且为这个 集合绑定标识符,当我们需要 到某种样式的单元格的时 候,就根据 同的标识符,从 同的集合中找寻单元格.
//该 法会先去缓存池中寻找对应的cell 如果缓存池中没有，就看有没有注册对应的cell，如果也 没有注册，就看storyboard中有没 有绑定对应标识的cell 都没有的话就创建
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:identifier] ;
if (!cell) { //创建cell的时候需要标示符(Identifier)是因为,当该cell离开屏幕的时候需要根据标示符放到对应
的集合中.
cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle
reuseIdentifier:@"cell"];
return cell ; }
系统第 次执  cellForRowAtIndexPath:(NSIndexPath *)indexPath的时候, reusableTableCells为 空,
[tableView dequeueReusableCellWithIdentifier:identifier] 的返回值为nil, 我们需要通过 initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier: identifier]  式来创建. 当我们的数据过多,整个屏幕的cell显示 完全时,这个 法的执 情况是 :
(1) 先执 initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier: identifier]创建整个屏幕能显 示的cell数+1的cell (当我们拖动屏幕的时候,第 个cell没有移出屏幕,最下 的cell就已经存在), 并指 定相同或者 同的标示符identifier.把创建出的屏幕能显示的cell全部都加 到visiableCells数组中(最 后 个创建的先 加 数组)，reusableTableCells为空. (2)当我们拖动屏幕时,顶端的cell移出屏幕并加 到reusableTableCells字典中,键为identifier ,并把之 前已经创建的但是没有加 到visiableCells的cell加 到visiableCells数组中. (3)当我们接着拖动的时候,因为reusableTableCells中已经有值，当需要显示新的cell， cellForRowAtIndexPath再次被调 执 [tableView dequeueReusableCellWithIdentifier: identifier]，返回 个标示符为identifier的cell。该cell移出reusableTableCells之后加 到 visiableCells;顶端的cell移出visiableCells并加 到reusableTableCells.如果visiableCells数组中没 有找到identifier类型的cell,则再次 新alloc 个.
在iOS6之后系统加   种单元格注册的 法.
[self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier: identifier] ;
 这个 法的作 是,当我们从  队 中取cell的时候,如果没有,系统会帮我们创建我们给定类型的 cell,如果有,则直接  . 这种 式cell的样式为系统默认样式.
在设置cell的 法中只需要:
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath: (NSIndexPath *)indexPath
{
//   队 中取单元格 由于上 已经注册过单元格,系统会帮我们做判断,  再次 动判断单元
格是否存在
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier: identifier
forIndexPath:indexPath] ; return cell ;
}
117问:lldb(gdb)常 的调试命令
答:breakpoint 设置断点定位到某 个函数
n 断点指针下 步
po打印对象
118问:如何调试BAD_ACCESS错误
答:1.  写object的respondsToSelector 法，现实出现EXEC_BAD_ACCESS前访问的最后 个 object
2. 通过 Zombie
3. 设置全局断点快速定位问题代码所在 
4. Xcode 7 已经集成 BAD_ACCESS捕获功能:Address Sanitizer。  法如下:在配置中勾选/ Enable Address Sanitizer
119
问:IB中User Defined Runtime Attributes如何使  答:它能够通过KVC的 式配置 些你在interface builder 中 能配置的属性。当你希望在IB中作尽 可能多得事情，这个特性能够帮助你编写 加轻 级的viewcontroller 120问:IBOutlet连出来的视图属性为 么可以被设置成weak 答:因为既然有外链那么视图在xib或者storyboard中肯定存在，视图已经对它有 个强引  。
 过这个回答  个 要知识，使 storyboard(xib  )创建的vc，会有 个叫 _topLevelObjectsToKeepAliveFromStoryboard的私有数组强引 所有top level的对象，所以这时 即 outlet声明成weak也没关系
121问: apple  么 式实现对 个对象的KVO 答:当你观察 个对象时， 个新的类会被动态创建。这个类继承 该对象的原本的类，并 写 被 观察属性的 setter  法。 写的 setter  法会负责在调 原 setter  法之前和之后，通知所有观 察对象:值的 改。最后通过 isa 混写(isa-swizzling) 把这个对象的 isa 指针 ( isa 指针告诉 Runtime 系统这个对象的类是 么 ) 指向这个新创建的 类，对象就神奇的变成 新创建的 类的 实 。
122问:KVC和KVO的keyPath 定是属性么 答:KVO 持实 变  123问:KVC的keyPath中的集合运算符如何使 
 答:1.必须 在集合对象上或普通对象的集合属性上
2.简单集合运算符有@avg， @count ， @max ， @min ，@sum，
3.格式 @"@sum.age"或 @“集合属性.@max.age"
124问:若 个类有实 变  NSString *_foo ，调 setValue:forKey:时，可以以foo还是 _foo 作为 key
答:都可以。
125问:如何 动触发 个value的KVO
答:所谓的“ 动触发”是区别于“ 动触发”:
 动触发是指类似这种场景:在注册 KVO 之前设置 个初始值，注册之后，设置 个  样的值， 就可以触发 。
想知道如何 动触发，必须知道 动触发 KVO 的原 :
键值观察通知依赖于 NSObject 的两个 法: willChangeValueForKey: 和 didChangevlueForKey: 。 在 个被观察属性发 改变之前， willChangeValueForKey:  定会被调 ，这就 会记录旧的值。  当改变发 后， didChangeValueForKey: 会被调 ，继  observeValueForKey:ofObject:change:context: 也会被调 。如果可以 动实现这些调 ，就可以 实现“ 动触发” 。
那么“ 动触发”的使 场景是 么  般我们只在希望能控制“回调的调 时机”时才会这么做。 具体做法如下:
如果这个 value 是 表示时间的 self.now ，那么代码如下:最后两 代码缺  可。
// .m 件
// Created by https://github.com/ChenYilong
// 微博@iOS程序 袁(http://weibo.com/luohanchenyilong/). //  动触发 value 的KVO，最后两 代码缺  可。
//@property (nonatomic, strong) NSDate *now; - (void)viewDidLoad
{
[super viewDidLoad];
[self willChangeValueForKey:@"now"]; // “ 动触发self.now的KVO”，必写。 [self didChangeValueForKey:@"now"]; // “ 动触发self.now的KVO”，必写。
} 但是平时我们 般 会这么 ，我们都是等系统去“ 动触发”。“ 动触发”的实现原 :
 如调  setNow: 时，系统还会以某种 式在中间插  wilChangeValueForKey: 、 didChangeValueForKey: 和 observeValueForKeyPath:ofObject:change:context: 的调 。
 家可能以为这是因为 setNow: 是合成 法，有时候我们也能看到 们这么写代码:
 - (void)setNow:(NSDate *)aDate {
[self willChangeValueForKey:@"now"]; // 没有必要 _now = aDate;
[self didChangeValueForKey:@"now"];// 没有必要
}
这是完全没有必要的代码， 要这么做，这样的话，KVO代码会被调 两次。KVO在调 存取 法 之前总是调  willChangeValueForKey: ，之后总是调  didChangeValueForkey: 。怎么做到的呢 答案是通过 isa 混写(isa-swizzling)。
126问:addObserver:forKeyPath:options:context:各个参数的作 分别是 么，observer中需要实 现哪个 法才能获得KVO回调
答:// 添加键值观察
/*
1 观察者，负责处 监听事件的对象 2 观察的属性
3 观察的选项
4 上下 
*/
[self.person addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld context:@"Person Name"];
observer中需要实现 下 法:
// 所有的 kvo 监听到事件，都会调 此 法 /*
1. 观察的属性
2. 观察的对象
3. change 属性变化字典(新/旧) 4. 上下 ，与监听的时候传递的 致 */
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change: (NSDictionary *)change context:(void *)context;
127问:以下代码运 结果如何 答:- (void)viewDidLoad
{
[super viewDidLoad];
NSLog(@"1"); dispatch_sync(dispatch_get_main_queue(), ^{
NSLog(@"2"); });
NSLog(@"3"); }
只输出:1 。发 主线程锁死。
 128问:苹果为 么要废弃dispatch_get_current_queue 答:dispatch_get_current_queue容 造成死锁
129问:dispatch_barrier_async的作 是 么 答:在并 队 中，为 保持某些任务的顺序，需要等待 些任务完成后才能继续进 ，使  barrier 来等待之前任务完成，避免数据竞争等问题。 dispatch_barrier_async 函数会等待追加到 Concurrent Dispatch Queue并 队 中的操作全部执 完之后，然后再执  dispatch_barrier_async 函数追加的处 ，等 dispatch_barrier_async 追加的处 执 结束之后， Concurrent Dispatch Queue才恢复之前的动作继续执 。
打个  : 如你们公司周末跟团旅游， 速休息站上，司机说: 家都去上厕所，速战速决，上 完厕所就上 速。超 的公共厕所， 家同时去，程序猿很快就结束 ，但程序媛就可能会慢 些， 即使你第 个回来，司机也 会出发，司机要等待所有 都回来后，才能出发。 dispatch_barrier_async 函数追加的内容就如同 “上完厕所就上 速”这个动作。
130问:如何 GCD同步若 个异步调  (如根据若 个url异步加载多张图 ，然后在都下载完成后 合成 张整图)
答:使 Dispatch Group追加block到Global Group Queue,这些block如果全部执 完毕，就会执  Main Dispatch Queue中的结束处 的block。
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0); dispatch_group_t group = dispatch_group_create(); dispatch_group_async(group, queue, ^{ /*加载图 1 */ }); dispatch_group_async(group, queue, ^{ /*加载图 2 */ }); dispatch_group_async(group, queue, ^{ /*加载图 3 */ }); dispatch_group_notify(group, dispatch_get_main_queue(), ^{
// 合并图  });
131 问:GCD的队 (dispatch_queue_t)分哪两种类型 答:1.  队 Serial Dispatch Queue 2.并 队 Concurrent Dispatch Queue
132问:使 系统的某些block api(如UIView的block版本写动画时)，是否也考虑引 循环问题 答:系统的某些block api中，UIView的block版本写动画时 需要考虑，但也有 些api 需要考虑:
所谓“引 循环”是指双向的强引 ，所以那些“单向的强引 ”(block 强引  self )没有问题，  如这些:
[UIView animateWithDuration:duration animations:^{ [self.superview layoutIfNeeded]; }]; [[NSOperationQueue mainQueue] addOperationWithBlock:^{ self.someProperty = xyz; }];
 [[NSNotificationCenter defaultCenter] addObserverForName:@"someNotification" object:nil
queue:[NSOperationQueue mainQueue] usingBlock:^(NSNotification * notification) {
self.someProperty = xyz; }];
这些情况 需要考虑“引 循环”。
但如果你使  些参数中可能含有 ivar 的系统 api ，如 GCD 、NSNotificationCenter就要    点: 如GCD 内部如果引   self， 且 GCD 的其他参数是 ivar，则要考虑到循环引 :
__weak __typeof__(self) weakSelf = self; dispatch_group_async(_operationsGroup, _operationsQueue, ^ {
__typeof__(self) strongSelf = weakSelf;
[strongSelf doSomething];
[strongSelf doSomethingElse];
} );
类似的:
__weak __typeof__(self) weakSelf = self;
_observer = [[NSNotificationCenter defaultCenter] addObserverForName:@"testKey"
object:nil queue:nil
usingBlock:^(NSNotification *note) { __typeof__(self) strongSelf = weakSelf;
[strongSelf dismissModalViewControllerAnimated:YES]; }];
self -- _observer -- block -- self 显然这也是 个循环引 。
133问:在block内如何修改block外部变  答:默认情况下，在block中访问的外部变 是复制过去的，即:写操作 对原变  效。但是你可以 加上__block来让其写操作 效，示 代码如下:
block int a = 0;
void (^foo)(void) = ^{
a = 1; }
f00(); //这 ，a的值被修改为1
134问:使 block时 么情况会发 引 循环，如何解决 答: 个对象中强引  block，在block中 使  该对象，就会发射循环引 。 解决 法是将该对 象使 __weak或者__block修饰符修饰之后再在block中使 。
 id weak weakSelf = self; 或者 weak __typeof(&*self)weakSelf = self该 法可以设置宏 id __block weakSelf = self;
135问:苹果是如何实现autoreleasepool的 答:autoreleasepool以 个队 数组的形式实现,主要通过下 三个函数完成.
objc_autoreleasepoolPush
objc_autoreleasepoolPop
objc_aurorelease 看函数名就可以知道，对autorelease分别执 push，和pop操作。销毁对象时执 release操作。
136问:BAD_ACCESS在 么情况下出现 答:访问  指针， 如对 个已经释放的对象执  release、访问已经释放对象的成员变 或者发 消息。 死循环
137问:  动指定autoreleasepool的前提下， 个autorealese对象在 么时刻释放 ( 如在 个 vc的viewDidLoad中创建)
答:分两种情况: 动 预释放时机、系统 动去释放。
 动 预释放时机--指定autoreleasepool 就是所谓的:当前作 域 括号结束时释放。 系统 动去释放--  动指定autoreleasepool
Autorelease对象会在当前的 runloop 迭代结束时释放。
如果在 个vc的viewDidLoad中创建 个 Autorelease对象，那么该对象会在 viewDidAppear  法 执 前就被销毁 。
138
问:ARC通过 么 式帮助开发者管 内存 答:编译时根据代码上下 ，插  retain/release
139问:objc使  么机制管 对象内存
答:通过 retainCount 的机制来决定对象是否需要释放。 每次 runloop 的时候，都会检查对象的 retainCount，如果retainCount 为 0，说明该对象没有地 需要继续使  ，可以释放掉 。
140问:猜想runloop内部是如何实现的 答: 般来讲， 个线程 次只能执  个任务，执 完成后线程就会退出。如果我们需要 个机制， 让线程能随时处 事件但并 退出，通常的代码逻辑 是这样的:
function loop() {
initialize(); do {
var message = get_next_message();
process_message(message); } while (message != quit);
} 或使 伪代码来展示下:
 //
// http://weibo.com/luohanchenyilong/ (微博@iOS程序 袁) // https://github.com/ChenYilong
int main(int argc, char * argv[]) {
//程序 直运 状态 while (AppIsRunning) {
//睡眠状态，等待唤醒事件
id whoWakesMe = SleepForWakingUp(); //得到唤醒事件
id event = GetEvent(whoWakesMe); //开始处 事件
HandleEvent(event);
}
return 0; }
141问:以+ scheduledTimerWithTimeInterval...的 式触发的timer，在滑动  上的 表时，timer 会暂定回调，为 么 如何解决 答:RunLoop只能运 在 种mode下，如果要换mode，当前的loop也需要停下 启成新的。  这 个机制，ScrollView滚动过程中NSDefaultRunLoopMode(kCFRunLoopDefaultMode)的mode会 切换到UITrackingRunLoopMode来保证ScrollView的流畅滑动:只能在NSDefaultRunLoopMode 模式下处 的事件会影响scrllView的滑动。
如果我们把 个NSTimer对象以NSDefaultRunLoopMode(kCFRunLoopDefaultMode)添加到主 运 循环中的时候, ScrollView滚动过程中会因为mode的切换， 导致NSTimer将 再被调度。
同时因为mode还是可定制的，所以:
Timer计时会被scrollView的滑动影响的问题可以通过将timer添加到NSRunLoopCommonModes (kCFRunLoopCommonModes)来解决。代码如下:
//
// http://weibo.com/luohanchenyilong/ (微博@iOS程序 袁) // https://github.com/ChenYilong
//将timer添加到NSDefaultRunLoopMode中 [NSTimer scheduledTimerWithTimeInterval:1.0
target:self selector:@selector(timerTick:) userInfo:nil
repeats:YES];
//然后再添加到NSRunLoopCommonModes  NSTimer *timer = [NSTimer timerWithTimeInterval:1.0
target:self
 selector:@selector(timerTick:) userInfo:nil
repeats:YES];
[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
142问:runloop的mode作 是 么
答:model 主要是 来指定事件在运 循环中的优先级的，分为:
NSDefaultRunLoopMode(kCFRunLoopDefaultMode):默认，空闲状态 UITrackingRunLoopMode:ScrollView滑动时 UIInitializationRunLoopMode:启动时 NSRunLoopCommonModes(kCFRunLoopCommonModes):Mode集合 苹果公开提供的 Mode 有两个:
NSDefaultRunLoopMode(kCFRunLoopDefaultMode) NSRunLoopCommonModes(kCFRunLoopCommonModes)
143问:runloop和线程有 么关系
答:总的说来，Run loop，正如其名，loop表示某种循环，和run放在 起就表示 直在运 着的循 环。实际上，run loop和线程是紧密相连的，可以这样说run loop是为 线程  ，没有线程，它就 没有存在的必要。Run loops是线程的基础架构部分， Cocoa 和 CoreFundation 都提供  run loop 对象  配置和管 线程的 run loop (以下都以 Cocoa 为 )。每个线程，包括程序的主线 程( main thread )都有与之相应的 run loop 对象。
runloop 和线程的关系:
1. 主线程的run loop默认是启动的。 iOS的应 程序  ，程序启动后会有 个如下的main()函数
int main(int argc, char * argv[]) {
@autoreleasepool { return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
}
}
 点是UIApplicationMain()函数，这个 法会为main thread设置 个NSRunLoop对象，这就解释  :为 么我们的应 可以在  操作的时候休息，需要让它 活的时候 能  响应。
2. 对其它线程来说，run loop默认是没有启动的，如果你需要 多的线程交互则可以 动配置和启 动，如果线程只是去执  个 时间的已确定的任务则 需要。
3. 在任何 个 Cocoa 程序的线程中，都可以通过以下代码来获取到当前线程的 run loop 。
 NSRunLoop *runloop = [NSRunLoop currentRunLoop];
144问:能否向编译后得到的类中增加实 变  能否向运 时创建的类中添加实 变  为 么 答: 能向编译后得到的类中增加实 变 ;
能向运 时创建的类中添加实 变 ;
解释下:
因为编译后的类已经注册在 runtime 中，类结构体中的 objc_ivar_list 实 变 的链表 和 instance_size 实 变 的内存  已经确定，同时runtime 会调  class_setIvarLayout 或 class_setWeakIvarLayout 来处  strong weak 引 。所以 能向存在的类中添加实 变 ; 运 时创建的类是可以添加实 变 ，调  class_addIvar 函数。但是得在调  objc_allocateClassPair 之后，objc_registerClassPair 之前，原因同上。 145问:runtime如何实现weak变 的 动置nil
答:runtime 对注册的类， 会进 布局，对于 weak 对象会放  个 hash 表中。   weak 指向的对 象内存地址作为 key，当此对象的引 计数为0的时候会 dealloc，假如 weak 指向的对象内存地址 是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从 设置为 nil。
146问:_objc_msgForward函数是做 么的，直接调 它将会发  么
答:_objc_msgForward是 IMP 类型， 于消息转发的:当向 个对象发送 条消息，但它并没有实 现的时候，_objc_msgForward会尝试做消息转发 147问:objc中的类 法和实  法有 么本质区别和联系
答:类 法:
类 法是属于类对象的
类 法只能通过类对象调 
类 法中的self是类对象
类 法可以调 其他的类 法
类 法中 能访问成员变 
类 法中 定直接调 对象 法
实  法:
实  法是属于实 对象的 实  法只能通过实 对象调  实  法中的self是实 对象 实  法中可以访问成员变  实  法中直接调 实  法 实  法中也可以调 类 法(通过类名)
148问:使 runtime Associate 法关联的对象，需要在主对象dealloc的时候释放么 答: 论在MRC下还是ARC下均 需要
对象的内存销毁时间表，分四个步骤:
// 对象的内存销毁时间表
 // http://weibo.com/luohanchenyilong/ (微博@iOS程序 袁)
// https://github.com/ChenYilong
// 根据 WWDC 2011, Session 322 (36分22秒)中发布的内存销毁时间表
1. 调  -release :引 计数变为零
* 对象正在被销毁， 命周期即将结束.
*  能再有新的 __weak 弱引 ， 否则将指向 nil. * 调  [self dealloc]
2.  类 调  -dealloc
* 继承关系中最底层的 类 在调  -dealloc
* 如果是 MRC 代码 则会 动释放实 变 们(iVars) * 继承关系中每 层的 类 都在调  -dealloc
3. NSObject 调 -dealloc
* 只做 件事:调  Objective-C runtime 中的 object_dispose()  法
4. 调  object_dispose()
* 为 C++ 的实 变 们(iVars)调  destructors
* 为 ARC 状态下的 实 变 们(iVars) 调  -release * 解除所有使  runtime Associate 法关联的对象
* 解除所有 __weak 引 
* 调  free()
149问: runtime如何通过selector找到对应的IMP地址 (分别考虑类 法和实  法) 答:每 个类对象中都 个 法 表, 法 表中记录着 法的名称, 法实现,以及参数类型,其实 selector本质就是 法名称,通过这个 法名称就可以在 法 表中找到对应的 法实现. 150问: 个objc对象的isa的指针指向 么 有 么作  答:指向他的类对象,从 可以找到对象上的 法
151问: 个objc对象如何进 内存布局 (考虑有 类的情况) 答:所有 类的成员变 和  的成员变 都会存放在该对象所对应的存储空间中. 每 个对象内部都有 个isa指针,指向他的类对象,类对象中存放着本对象的 1)对象 法 表(对象能够接收的消息 表，保存在它所对应的类对象中)
2)成员变 的 表 3)属性 表
它内部也有 个isa指针指向元对象(meta class),元对象内部存放的是类 法 表,类对象内部还有  个superclass的指针,指向他的 类对象。
1)根对象就是NSobject，它的superclass指针指向nil。
2)类对象既然称为对象，那它也是 个实 。类对象中也有 个isa指针指向它的元类(meta class)， 即类对象是元类的实 。元类内部存放的是类 法 表，根元类的isa指针指向  ，superclass指 针指向NSObject类。
152问: 么时候会报unrecognized selector的异常
 答:简单来说:当该对象上某个 法, 该对象上没有实现这个 法的时候， 可以通过“消息转发”进  解决。
简单的流程如下，在上 题中也提到过:objc是动态语 ，每个 法在运 时会被动态转为消息发 送，即:objc_msgSend(receiver, selector)。
objc在向 个对象发送消息时，runtime库会根据对象的isa指针找到该对象实际所属的类，然后在 该类中的 法 表以及其 类 法 表中寻找 法运 ，如果，在最顶层的 类中依然找 到相应 的 法时，程序在运 时会挂掉并抛出异常unrecognized selector sent to XXX 。但是在这之前， objc的运 时会给出三次拯救程序崩溃的机会:
Method resolution
objc运 时会调 +resolveInstanceMethod:或者 +resolveClassMethod:，让你有机会提供 个函 数实现。如果你添加 函数并返回 YES，那运 时系统就会 新启动 次消息发送的过程，如果 resolve  法返回 NO ，运 时就会移到下 步，消息转发(Message Forwarding)。
Fast forwarding
如果 标对象实现 -forwardingTargetForSelector:，Runtime 这时就会调 这个 法，给你把这 个消息转发给其他对象的机会。 只要这个 法返回的 是nil和self，整个消息发送的过程就会被  启，当然发送的对象会变成你返回的那个对象。否则，就会继续Normal Fowarding。 这 叫Fast， 只是为 区别下 步的转发机制。因为这 步 会创建任何新的对象，但下 步转发会创建 个 NSInvocation对象，所以相对 快点。
Normal forwarding 这 步是Runtime最后 次给你挽救的机会。 先它会发送-methodSignatureForSelector:消息获 得函数的参数和返回值类型。如果-methodSignatureForSelector:返回nil，Runtime则会发出- doesNotRecognizeSelector:消息，程序这时也就挂掉 。如果返回  个函数签名，Runtime就会 创建 个NSInvocation对象并发送-forwardInvocation:消息给 标对象。
153问:objc中向 个对象发送消息[obj foo]和objc_msgSend()函数之间有 么关系 答:具体原因同上题:该 法编译之后就是objc_msgSend()函数调 .如果我没有记错的 概是这样 的:
((void ()(id, SEL))(void )objc_msgSend)((id)obj, sel_registerName("foo")); 也就是说:
[obj foo];在objc动态编译时，会被转意为:objc_msgSend(obj, @selector(foo));。
154问:objc中向 个nil对象发送消息将会发  么 答:在Objective-C中向nil发送消息是完全有效的——只是在运 时 会有任何作 :
如果 个 法返回值是 个对象，那么发送给nil的消息将返回0(nil)。 如:
Person * motherInlaw = [[aPerson spouse] mother]; 如果spouse对象为nil，那么发送给nil的消息mother也将返回nil。
 1)如果 法返回值为指针类型，其指针  为 于或者等于sizeof(void*)，float，double，long double 或者long long的整型标 ，发送给nil的消息将返回0。
2)如果 法返回值为结构体,发送给nil的消息将返回0。结构体中各个字段的值将都是0。
3)如果 法的返回值 是上述提到的 种情况，那么发送给nil的消息的返回值将是未定义的。
具体原因如下:
objc是动态语 ，每个 法在运 时会被动态转为消息发送，即:objc_msgSend(receiver, selector)。
那么，为    解这个内容，还是贴 个objc的源代码:
struct objc_class {
Class isa OBJC_ISA_AVAILABILITY; //isa指针指向Meta Class，因为Objc的类的本身也是 个
Object，为 处 这个关系，runtime就创造 Meta Class，当给类发送[NSObject alloc]这样消息 时，实际上是把这个消息发给 Class Object
#if !__OBJC2__
Class super_class OBJC2_UNAVAILABLE; //  类
const char *name OBJC2_UNAVAILABLE; // 类名
long version OBJC2_UNAVAILABLE; // 类的版本信息，默认为0
long info OBJC2_UNAVAILABLE; // 类信息，供运 期使 的 些位标识
long instance_size OBJC2_UNAVAILABLE; // 该类的实 变   
struct objc_ivar_list *ivars OBJC2_UNAVAILABLE; // 该类的成员变 链表
struct objc_method_list **methodLists OBJC2_UNAVAILABLE; //  法定义的链表
struct objc_cache *cache OBJC2_UNAVAILABLE; //  法缓存，对象接到 个消息会根据isa指
针查找消息对象，这时会在method Lists中遍历，如果cache ，常 的 法调 时就能够提 调  的效率。
struct objc_protocol_list *protocols OBJC2_UNAVAILABLE; // 协议链表 #endif
} OBJC2_UNAVAILABLE;
objc在向 个对象发送消息时，runtime库会根据对象的isa指针找到该对象实际所属的类，然后在 该类中的 法 表以及其 类 法 表中寻找 法运 ，然后在发送消息的时候，objc_msgSend  法 会返回值，所谓的返回内容都是具体调 时执 的。 那么，回到本题，如果向 个nil对象发送 消息， 先在寻找对象的isa指针时就是0地址返回 ，所以 会出现任何错误。 155问:在有  动合成属性实 变 之后，@synthesize还有哪些使 场景 答:回答这个问题前，我们要搞清楚 个问题， 么情况下 会autosynthesis( 动合成)
同时 写 setter和getter时  写 只读属性的getter时 使  @dynamic时
在 @protocol 中定义的所有属性
 在 category 中定义的所有属性
 载的属性
当你在 类中 载  类中的属性，你必须 使 @synthesize来 动合成ivar。
除 后三条，对其他 个我们可以总结出 个规 :当你想 动管 @property的所有内容时，你就 会尝试通过实现@property的所有“存取 法”(the accessor methods)或者使 @dynamic来达 到这个 的，这时编译 就会认为你打算 动管 @property，于是编译 就禁  autosynthesis ( 动合成)。
因为有 autosynthesis( 动合成)， 部分开发者已经习惯 去 动定义ivar， 是依赖于 autosynthesis( 动合成)，但是 旦你需要使 ivar， autosynthesis( 动合成) 失效 ， 如果 去 动定义ivar，那么你就得借助@synthesize来 动合成ivar。 156问:@synthesize合成实 变 的规则是 么 假如property名为foo，存在 个名为_foo的实 变  ，那么还会 动合成新变 么 答:如果使  属性的话，那么编译 就会 动编写访问属性所需的 法，此过程叫做“ 动合成”( auto synthesis)。需要强调的是，这个过程由编译 在编译期执 ，所以编辑  看 到这些“合成  法” (synthesized method)的源代码。除  成 法代码之外，编译 还要 动向类中添加适当类 型的实 变 ，并且在属性名前 加下划线，以此作为实 变 的名字。
@interface CYLPerson : NSObject
@property NSString *firstName;
@property NSString *lastName;
@end
在上 中，会 成两个实 变 ，其名称分别为 _firstName与_lastName。也可以在类的实现代码  通过@synthesize语法来指定实 变 的名字:
@implementation CYLPerson
@synthesize firstName = _myFirstName;
@synthesize lastName = _myLastName;
@end 上述语法会将 成的实 变 命名为_myFirstName与_myLastName，  再使 默认的名字。  般情况下 须修改默认的实 变 名，但是如果你 喜欢以下划线来命名实 变 ，那么可以 这 个办法将其改为  想要的名字。笔者还是推荐使 默认的命名 案，因为如果所有 都坚持这套  案，那么写出来的代码 家都能看得懂。
总结下@synthesize合成实 变 的规则，有以下 点: 1)如果指定 成员变 的名称,会 成 个指定的名称的成员变 , 2)如果这个成员已经存在 就 再 成 .
3)如果是 @synthesize foo; 还会 成 个名称为foo的成员变 ，也就是说:如果没有指定成员变  的名称会 动 成 个属性同名的成员变 。
 4)如果是 @synthesize foo = _foo; 就 会 成成员变  . 假如property名为foo，存在 个名为_foo的实 变 ，那么还会 动合成新变 么  会。
157问: @property声明的NSString(或NSArray，NSDictionary)经常使 copy关键字，为 么 如果改 strong关键字，可能造成 么问题 答:1)因为 类指针可以指向 类对象,使 copy的 的是为 让本对象的属性 受外界影响,使  copy 论给我传 是 个可变对象还是 可对象,我本身持有的就是 个 可变的副本.
2)如果我们使 是strong,那么这个属性就有可能指向 个可变对象,如果这个可变对象在外部被修 改 ,那么会影响该属性.
copy此特质所表达的所属关系与strong类似。然 设置 法并 保 新值， 是将其“拷 ” (copy)。 当属性类型为NSString时，经常 此特质来保护其封装性，因为传递给设置 法的新值有 可能指向 个NSMutableString类的实 。这个类是NSString的 类，表示 种可修改其值的字符  ，此时若是 拷 字符 ，那么设置完属性之后，字符 的值就可能会在对象 知情的情况下遭   改。所以，这时就要拷  份“ 可变” (immutable)的字符 ，确保对象中的字符 值 会  意间变动。只要实现属性所 的对象是“可变的” (mutable)，就应该在设置新属性值时拷  份。
为  解这种做法， 先要知道，对 集合类对象的copy操作:
在 集合类对象中:对immutable对象进 copy操作，是指针复制，mutableCopy操作时内容复制 ;对mutable对象进 copy和mutableCopy都是内容复制。 代码简单表示如下:
[immutableObject copy] // 浅复制 [immutableObject mutableCopy] //深复制 [mutableObject copy] //深复制 [mutableObject mutableCopy] //深复制  如以下代码:
NSMutableString *string = [NSMutableString stringWithString:@"origin"];//copy
NSString *stringCopy = [string copy];
查看内存，会发现 string、stringCopy 内存地址都  样，说明此时都是做内容拷 、深拷 。即 使你进 如下操作:
1
[string appendString:@"origion!"] stringCopy的值也 会因此改变，但是如果 使 copy，stringCopy的值就会被改变。 集合类对象 以此类推。 所以，
 @property声明 NSString、NSArray、NSDictionary 经常使 copy关键字，是因为他们有对应的 可变类型:NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进 赋值 操作，为确保对象中的字符 值 会 意间变动，应该在设置新属性值时拷  份。
158问:ARC下， 显式指定任何属性关键字时，默认的关键字都有哪些 答:对应基本数据类型默认关键字是
 atomic,readwrite,assign
对于普通的OC对象
atomic,readwrite,strong
159问:@synthesize和@dynamic分别有 么作  答:1)@property有两个对应的词， 个是@synthesize， 个是@dynamic。如果@synthesize和 @dynamic都没写，那么默认的就是@syntheszie var = _var;
2)@synthesize的语义是如果你没有 动实现setter 法和getter 法，那么编译 会 动为你加上 这两个 法。
3)@dynamic告诉编译 :属性的setter与getter 法由 户  实现，  动 成。(当然对于 readonly的属性只需提供getter即可)。假如 个属性被声明为@dynamic var，然后你没有提供 @setter 法和@getter 法，编译的时候没问题，但是当程序运 到instance.var = someVar，由 于缺setter 法会导致程序崩溃;或者当运 到 someVar = var时，由于缺getter 法同样会导致崩 溃。编译时没问题，运 时才执 相应的 法，这就是所谓的动态绑定。 160问:weak属性需要在dealloc中置nil么
答: 需要。 在ARC环境 论是强指针还是弱指针都 需在deallco设置为nil，ARC会 动帮我们处 。 即 是编译  帮我们做这些，weak也 需要在dealloc中置nil:
正如上 的:runtime 如何实现 weak 属性 中提到的:
我们模拟下weak的setter 法，应该如下: - (void)setObject:(NSObject *)object
{
objc_setAssociatedObject(self, "object", object, OBJC_ASSOCIATION_ASSIGN); [object cyl_runAtDealloc:^{
_object = nil; }];
}
也即:在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 161问:@property中有哪些属性关键字 / @property 后 可以有哪些修饰符 答:属性可以拥有的特质分为四类:
原 性---nonatomic特质 在默认情况下，由编译 合成的 法会通过锁定机制确保其原 性(atomicity)。如果属性具备 nonatomic特质，则 使 同步锁。请注意，尽管没有名为“atomic”的特质(如果某属性 具备 nonatomic特质，那它就是“原 的” ( atomic) )，但是仍然可以在属性特质中写明这 点，编译   会报错。若是  定义存取 法，那么就应该遵从与属性特质相符的原 性。
读/写权限---readwrite(读写)、readooly (只读) 内存管 语义---assign、strong、 weak、unsafe_unretained、copy
  法名---getter=、setter= getter=的样式:
1
@property (nonatomic, getter=isOn) BOOL on;
( setter=这种 常 ，也 推荐使 。故 在这 给出写法。)
 常 的:nonnull,null_resettable,nullable 162问:runtime 如何实现 weak 属性 答:要实现weak属性， 先要搞清楚weak属性的特点:
weak 此特质表明该属性定义  种“ 拥有关系” (nonowning relationship)。为这种属性设置新值 时，设置 法既 保 新值，也 释放旧值。此特质同assign类似， 然 在属性所指的对象遭到摧 毁时，属性值也会清空(nil out)。
那么runtime如何实现weak变 的 动置nil
runtime 对注册的类， 会进 布局，对于 weak 对象会放  个 hash 表中。   weak 指向的对象 内存地址作为 key，当此对象的引 计数为0的时候会 dealloc，假如 weak 指向的对象内存地址是a， 那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从 设置为 nil。 163问: @protocol 和 category 中如何使  @property 答:1)在protocol中使 property只会 成setter和getter 法声明,我们使 属性的 的,是希望遵守 我协议的对象能实现该属性
2)category 使  @property 也是只会 成setter和getter 法的声明,如果我们真的需要给category 增加属性的实现,需要借助于运 时的两个函数:
1objc_setAssociatedObject
2objc_getAssociatedObject
164问:@property 的本质是 么 ivar、getter、setter 是如何 成并添加到这个类中的 答:@property 的本质是 么
@property = ivar + getter + setter; 下 解释下:
“属性” (property)有两 概念:ivar(实 变 )、存取 法(access method = getter + setter)。
“属性” (property)作为 Objective-C 的 项特性，主要的作 就在于封装对象中的数据。 Objective-C 对象通常会把其所需要的数据保存为各种实 变 。实 变  般通过“存取  法”(access method)来访问。其中，“获取 法” (getter) 于读取变 值， “设置 法” (setter)  于写 变 值。这个概念已经定型，并且经由“属性”这 特性 成为Objective-C 2.0的 部分。  在正规的 Objective-C 编码 格中，存取 法有着严格的命名规范。 正因为有 这种严格的命名
 规范，所以 Objective-C 这 语 才能根据名称 动创建出存取 法。其实也可以把属性当做 种 关键字，其表示:
编译 会 动写出 套存取 法， 以访问给定类型中具有给定名称的变 。 所以你也可以这么说 :
@property = getter + setter;  如下 这个类:
@interface Person : NSObject
@property NSString *firstName; @property NSString *lastName;
@end 上述代码写出来的类与下 这种写法等效:
@interface Person : NSObject
- (NSString *)firstName;
- (void)setFirstName:(NSString *)firstName;
- (NSString *)lastName;
- (void)setLastName:(NSString *)lastName;
@end
ivar、getter、setter 是如何 成并添加到这个类中的
“ 动合成”( autosynthesis)
完成属性定义后，编译 会 动编写访问这些属性所需的 法，此过程叫做“ 动合成”( autosynthesis)。需要强调的是，这个过程由编译  在编译期执 ，所以编辑  看 到这些“合成  法”(synthesized method)的源代码。除  成 法代码 getter、setter 之外，编译 还要 动向 类中添加适当类型的实 变 ，并且在属性名前 加下划线，以此作为实 变 的名字。在前 中， 会 成两个实 变 ，其名称分别为 _firstName与_lastName。也可以在类的实现代码 通过 @synthesize语法来指定实 变 的名字.
@implementation Person
@synthesize firstName = _myFirstName;
@synthesize lastName = myLastName;
@end 我为 搞清属性是怎么实现的,曾经反编译过相关的代码, 致 成 五个东 :
1)OBJC_IVAR_$类名$属性名称 :该属性的“偏移 ” (offset)，这个偏移 是“硬编码” (hardcode)，表示该变 距离存放对象的内存区域的起始地址有多远。
2)setter与getter 法对应的实现函数 3)ivar_list :成员变  表
 4)method_list : 法 表 5)prop_list :属性 表
也就是说我们每次在增加 个属性,系统都会在ivar_list中添加 个成员变 的描述,在method_list中 增加setter与getter 法的描述,在属性 表中增加 个属性的描述,然后计算该属性在对象中的偏移  ,然后给出setter与getter 法对应的实现,在setter 法中从偏移 的位置开始赋值,在getter 法中 从偏移 开始取值,为 能够读取正确字节数,系统对象偏移 的指针类型进  类型强转. 165问:如何让  的类  copy 修饰符 如何 写带 copy 关键字的 setter 答:若想令  所写的对象具有拷 功能，则需实现NSCopying协议。如果 定义的对象分为可变版 本与 可变版本，那么就要同时实现NSCopyiog与NSMutableCopying协议。
具体步骤:
1)需声明该类遵从NSCopying协议 2)实现NSCopying协议。该协议只有 个 法:
- (id)copyWithZone: (NSZone*) zone
注意: 提到让  的类  copy 修饰符，我们总是想覆写copy 法，其实真正需要实现的却是 “copyWithZone” 法。
166问:这个写法会出 么问题: @property (copy) NSMutableArray *array;
答:两个问题: 1、添加,删除,修改数组内的元素的时候,程序会因为找 到对应的 法 崩溃.因为copy就是复制 个  可变NSArray的对象;
2、使  atomic属性会严 影响性能。
第1条的同上 个问题。 第2条原因，如下:
该属性使  同步锁，会在创建时 成 些额外的代码 于帮助编写多线程程序，这会带来性能问 题，通过声明nonatomic可以节省这些虽然很 但是 必要额外开销。
在默认情况下，由编译 所合成的 法会通过锁定机制确保其原 性(atomicity)。如果属性具备 nonatomic特质，则 使 同步锁。请注意，尽管没有名为“atomic”的特质(如果某属性 具备 nonatomic特质，那它就是“原 的”(atomic))。
在iOS开发中，你会发现， 乎所有属性都声明为nonatomic。
 般情况下并 要求属性必须是“原 的”，因为这并 能保证“线程安全” ( thread safety)，若要实 现“线程安全”的操作，还需采  为深层的锁定机制才 。 如， 个线程在连续多次读取某属性 值的过程中有别的线程在同时改写该值，那么即 将属性声明为atomic，也还是会读到 同的属性 值。
 因此，开发iOS程序时 般都会使 nonatomic属性。但是在开发Mac OS X程序时，使  atomic属 性通常都 会有性能瓶颈。
167问:怎么  copy 关键字
答: 途:
1)NSString、NSArray、NSDictionary 等等经常使 copy关键字，是因为他们有对应的可变类型 :NSMutableString、NSMutableArray、NSMutableDictionary;
2)block也经常使 copy关键字，具体原因 官  档:Objects Use Properties to Keep Track of Blocks:
block使 copy是从MRC遗 下来的“传统”,在MRC中, 法内部的block是在栈区的,使 copy可以 把它放到堆区.在ARC中写 写都 :对于block使 copy还是strong效果是 样的，但写上copy也  伤 雅，还能时刻提醒我们:编译  动对block进  copy操作。
copy此特质所表达的所属关系与strong类似。然 设置 法并 保 新值， 是将其“拷 ” (copy)。 当属性类型为NSString时，经常 此特质来保护其封装性，因为传递给设置 法的新值有 可能指向 个NSMutableString类的实 。这个类是NSString的 类，表示 种可修改其值的字符  ，此时若是 拷 字符 ，那么设置完属性之后，字符 的值就可能会在对象 知情的情况下遭   改。所以，这时就要拷  份“ 可变” (immutable)的字符 ，确保对象中的字符 值 会  意间变动。只要实现属性所 的对象是“可变的” (mutable)，就应该在设置新属性值时拷  份。
 @property声明 NSString、NSArray、NSDictionary 经常使 copy关键字，是因为他们有对应的 可变类型:NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进 赋值 操作，为确保对象中的字符 值 会 意间变动，应该在设置新属性值时拷  份。 168问: 么情况使  weak 关键字，相  assign 有 么 同 答:1)在ARC中,在有可能出现循环引 的时候,往往要通过让其中 端使 weak来解决, 如:delegate 代 属性
2) 身已经对它进  次强引 ,没有必要再强引  次,此时也会使 weak, 定义IBOutlet控件属 性 般也使 weak;当然，也可以使 strong。
 同点:
1)weak 此特质表明该属性定义  种“ 拥有关系” (nonowning relationship)。为这种属性设置 新值时，设置 法既 保 新值，也 释放旧值。此特质同assign类似， 然 在属性所指的对象遭 到摧毁时，属性值也会清空(nil out)。   assign 的“设置 法”只会执 针对“纯 类型” (scalar type， 如 CGFloat 或 NSlnteger 等)的简单赋值操作。
2)assigin 可以  OC对象, weak必须 于OC对象 169问:定义 个OC标准的枚举命令
答:官 示 1:
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
UITableViewCellStyleDefault,
 UITableViewCellStyleValue1, UITableViewCellStyleValue2, UITableViewCellStyleSubtitle
};
官 示 2:
typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {
UIViewAutoresizingNone = 0, UIViewAutoresizingFlexibleLeftMargin = 1 << 0, UIViewAutoresizingFlexibleWidth = 1 << 1, UIViewAutoresizingFlexibleRightMargin = 1 << 2, UIViewAutoresizingFlexibleTopMargin = 1 << 3, UIViewAutoresizingFlexibleHeight = 1 << 4, UIViewAutoresizingFlexibleBottomMargin = 1 << 5
};
170问:对 函数指针和Blocks使  法
答:函数指针定义:int func (int count)( return count +1) ; int (*funcptr)(int) = &func; 函数指针使 :int result = (*funcptr)(10);
Blocks定义:int (^blk)(int) = ^(int count){return count+1;};
Blocks使 :int result = blk(10);
171问:const常 与宏定义区别
答:(1) 编译 处  式 同
  define宏是在预处 阶段展开。
  const常 是编译运 阶段使 。
(2) 类型和安全检查 同
  define宏没有类型， 做任何类型检查，仅仅是展开。   const常 有具体的类型，在编译阶段会执 类型检查。
(3) 存储 式 同   define宏仅仅是展开，有多少地 使 ，就展开多少次， 会分配内存。   const常 会在内存中分配(可以是堆中也可以是栈中)。
(4)const 可以节省空间，避免 必要的内存分配。  如:
#define PI 3.14159 //常 宏
const doulbe Pi=3.14159; //此时并未将Pi放 ROM中 ......
double i=Pi; //此时为Pi分配内存，以后 再分配!
double I=PI; //编译期间进 宏替换，分配内存
double j=Pi; //没有内存分配
double J=PI; //再进 宏替换，  次分配内存! const定义常 从汇编的 度来看，只是给出 对应的内存地址，  是象#define 样给出的
是 即数，所以，const定义的常 在程序运 过程中只有 份拷 ，  #define定义的常 在内存 中有若 个拷 。
(5) 提  效率。 编译 通常 为普通const常 分配存储空间， 是将它们保存在符号表中，这使 得它成为 个编译期间的常 ，没有 存储与读内存的操作，使得它的效率也很 。
const 与 #define的 较
 C++ 语 可以 const来定义常 ，也可以  #define来定义常 。但是前者 后者有 多的优 点:
(1) const常 有数据类型， 宏常 没有数据类型。编译 可以对前者进 类型安全检查。  对后者只进 字符替换，没有类型安全检查，并且在字符替换可能会产 意  到的错误(边际效 应)。
(2) 有些集成化的调试 具可以对const常 进 调试，但是 能对宏常 进 调试。
172问:讲讲OC和其他语 好在哪 , 好在哪  答:objc优点:
1)Cateogies
2) Posing
3) 动态识别
4) 指标计算
5)弹性讯息传递
6)  是  个过度复杂的 C 衍  语   7) Objective-C++ 与 C++ 可混合编程 缺点:
1)    援命名空間 2)    持运算符 载 3)   持多 继承
173问:const的 法 答:(1)欲阻  个变 被改变,可以使 const关键字。在定义该const变 时,通常需要对它进 初始 化,因为以后就没有机会再去改变它 ;
(2)对指针来说,可以指定指针本身为const,也可以指定指针所指的数据为const,或 者同时指定为 const;
(3)在 个函数声明中,const可以修饰形参,表明它是 个输 参数,在函数内部 能改变其值; (4)对于类的成员函数,若指定其为const类型,则表明其是 个常函数,  能修改类的成员变 ; (5)对于类的成员函数,有时候必须指定其返回值为const类型,以使得其 返回值 为“左值”。174问:
把你知道的framework写出来，写出功能
答:UIKit.framework 包含iOS应 程序 户界 层使 的类和 法，CoreGraphics.framework 包含 Quartz 2D 绘图API 接 ，Foundation.framework 为CoreFoundation 框架的许多功能提供 Objective-C 封装，CoreFoundation.framework  组C 语 接 ,它们为iOS 应 程序提供基本数 据管 和服务功能，MapKit.framework 该框架供 个可被嵌 到应 程序的地图界 ,该界 包含  个可以滚动的地图视图，CoreLocation.framework 可 于定位某个设备当前经纬度， CFNetwork.framework提供 组 性能基于C 语 的接 ,它们为使  络协议提供 向对象抽象。 175问:project workspace scheme的区别
答:Project:
  般的某个应  单独新建  个project就可以 ,然后把所有的程序  件都放在    , 这个可以满    部分普通的需求。
Workspace:
项   有可能要使  其他的项     件,或者引  其他的静态库  件,这个时候 workspace就派上  场 ,workspace既可以单独管 多个项   ,  可以通过配置,让各个项    相互依赖。
Scheme:
 Xcode scheme定义 编译集合中的若 target,编译时的  些设置以及要执  的测试集合。 可以  定义若 个scheme,但是同  时刻只能运    个。
176
问:SEL和 个函数指针调 同 个 法的结果是 是相同 答:得到 SEL变 之后,可以通过下  的调  来给  个对象发送消息:
[对象performSelector:SEL变 withObject:参数1 withObject:参数2];
结论:SEL只是  法名标识,实际运  时需要通过消息发送来调  ,IMP是”implementation” 的缩 写,它是objetive-C   法 (method)实现代码块的地址,类似函数指针,通过它可以 直接访问任 意  个  法。免去发送消息的代价。 177问:对于Objective-C，你认为它最 的优点和最 的  是 么 对于  之处，现在有没有可  的 法绕过这些  来实现需求。如果可以的话，你有没有考虑或者实践过 新实现OC的 些功能， 如果有，具体会如何做 答:最 的优点是它的运 时特性，  是没有命名空间，对于命名冲突，可以使  命名法或特殊 前缀解决，如果是引 的第三 库之间的命名冲突，可以使 link命令及flag解决冲突。
178问:是否做过异步的 络处 和通讯  的 作 如果有，能具体介绍 些实现策 么 答:使 NSOperation发送异步 络请求，使 NSOperationQueue管 线程数 及优先级，底层是  NSURLConnetion。
179问:使 GCD以及block时要注意些 么 它们两是 回事 么 block在ARC中和传统的MRC中的  为和 法有没有 么区别，需要注意些 么 如何避免循环引  答:使 block是要注意，若将block做函数参数时，需要把它放到最后，GCD是Grand Central Dispatch，是 个对线程开源类库， Block是闭包，是能够读取其他函数内部变 的函数。 180问:你 过NSOperationQueue么 如果 过或者 解的话，你为 么要使  NSOperationQueue，实现  么 请描述它和GCD的区别和类似的地  答:使 NSOperationQueue 来管  类化的NSOperation对象，控制其线程并发数 。GCD和 NSOperation都可以实现对线程的管 ，区别是 NSOperation和NSOperationQueue是多线程的  向对象抽象。项 中使 NSOperation的优点是NSOperation是对线程的 度抽象，在项 中使  它，会使项 的程序结构 好， 类化NSOperation的设计思 ，是具有 向对象的优点(复 、 封装)，使得实现是多线程 持， 接 简单，建议在复杂项 中使 。 项 中使 GCD的优点是GCD本身 常简单、  ，对于 复杂的多线程操作，会节省代码 ，  Block参数的使 ，会是代码 为 读，建议在简单项 中使 。  181问:NSNotification和KVO的区别和 法是 么  么时候应该使 通知， 么时候应该使  KVO，它们的实现上有 么区别吗 如果 protocol和delegate(或者delegate的Array)来实现类似 的功能可能吗 如果可能，会有 么潜在的问题 如果 能，为 么 答:NSNotification是通知模式在iOS的实现，KVO的全称是键值观察(Key-value observing),其是基 于KVC(key-value coding)的，KVC是 个通过属性名访问属性变 的机制。 如将Module层的 变化，通知到多个Controller对象时，可以使 NSNotification;如果是只需要观察某个对象的某个 属性，可以使 KVO。 对于委托模式，在设计模式中是对象适配 模式，其是delegate是指向某个对象的，这是 对 的 关系， 在通知模式中，往往是 对多的关系。委托模式，从技术上可以现在改变delegate指向的 对象，但 建议这样做，会让 迷惑，如果 个delegate对象 断改变，指向 同的对象。 182问:是否使 过CoreText或者CoreImage等 如果使 过，请谈谈你使 CoreText或者CoreImage 的体验 答:CoreText可以解决复杂 字内容排版问题。CoreImage可以处 图 ，为其添加各种效果。体验 是很强 ，挺复杂的。
 183问:是否使 过CoreAnimation和CoreGraphics。UI框架和CA，CG框架的联系是 么 分别 CA 和CG做过些 么动画或者图像上的内容 答:UI框架的底层有CoreAnimation，CoreAnimation的底层有CoreGraphics。
UIKit |
------------ |
Core Animation |
Core Graphics |
Graphics Hardware| 使 CA做过menu菜单的展开收起
184问:使 过Objective-C的运 时编程(Runtime Programming)么 如果使 过，你 它做   么 你还能记得你所使 的相关的头 件或者某些 法的名称吗
答:Objecitve-C的 要特性是Runtime(运 时),在#import <objc/runtime.h 法， 过objc_getClass()和class_copyMethodList()获取过私有API;使  ```objective-c
Method method1 = class_getInstanceMethod(cls, sel1);
Method method2 = class_getInstanceMethod(cls, sel2); method_exchangeImplementations(method1, method2); ```
代码交换两个 法，在写unit test时使 到。
185问:iOS 创建单 的两种 法(MRC ARC) 答:MRC
static AccountManager *DefaultManager = nil;
+ (AccountManager *)defaultManager {
if (!DefaultManager) DefaultManager = [[self allocWithZone:NULL] init]; return DefaultManager;
}
ARC
+ (AccountManager *)sharedManager {
static AccountManager *sharedAccountManagerInstance = nil; static dispatch_once_t predicate;
dispatch_once(&predicate, ^{
sharedAccountManagerInstance = [[self alloc] init]; });
return sharedAccountManagerInstance; }
186问:写 个NSString类的实现
答:+ (id)initWithCString:(c*****t char *)nullTerminatedCString encoding: (NSStringEncoding)encoding;
+ (id) stringWithCString: (c*****t char*)nullTerminatedCString
下能看到相关的 
 encoding: (NSStringEncoding)encoding {
NSString *obj;
obj = [self allocWithZone: NSDefaultMallocZone()];
obj = [obj initWithCString: nullTerminatedCString encoding: encoding]; return AUTORELEASE(obj);
}
187问: 标-动作机制是 么 答: 标是动作消息的接收者。 个控件，或者 为常 的是它的单元，以插座变 (参 "插座变  "部分)
的形式保有其动作消息的 标。 动作是控件发送给 标的消息，或者从 标的 度看，它是 标为 响应动作 实现的 法。 程序需要某些机制来进 事件和指令的翻译。这个机制就是 标-动作机制。
188问: 么是键-值,键 径是 么 答:模型的性质是通过 个简单的键(通常是个字符 )来指定的。视图和控制 通过键来查找相应 的属性值。在 个给定的实体中，同 个属性的所有值具有相同的数据类型。键-值编码技术 于进  这样的查找—它是 种间接访问对象属性的机制。 键 径是 个由 点作分隔符的键组成的字符 ， 于指定 个连接在 起的对象性质序 。第  个键的 性质是由先前的性质决定的，接下来每个键的值也是相对于其前 的性质。键 径使您可以以独  于模型 实现的 式指定相关对象的性质。通过键 径，您可以指定对象图中的 个任意深度的 径，使其 指向相
关对象的特定属性。
189问: 动释放池是 么,如何 作
答:当 您向 个对象发送 个autorelease消息时，Cocoa就会将该对象的 个引 放 到最新的  动释放池。它仍然是个正当的对象，因此 动释放池定 义的作 域内的其它对象可以向它发送消息。 当程序执 到作 域结束的位置时， 动释放池就会被释放，池中的所有对象也就被释放。
1. ojc-c 是 通过 种"referring counting"(引 计数)的 式来管 内存的, 对象在开始分配内存 (alloc)的时候引 计数为 ,以后每当碰到有copy,retain的时候引 计数都会加 , 每当碰到release 和autorelease的时候引 计数就会减 ,如果此对象的计数变为 0, 就会被系统销毁.
2. NSAutoreleasePool 就是 来做引 计数的管  作的,这个东  般  你管的.
3. autorelease和release没 么区别,只是引 计数减 的时机 同 已,autorelease会在对象的使  真正结束的时候才做引 计数减 .
190问: 法和选择 有何 同 答:selector是 个 法的名字，method是 个组合体，包含 名字和实现.
191问:id、nil代表 么
答:id和void *并 完全 样。在上 的代码中，id是指向struct objc_object的 个指针，这个意思 基本上是说，id是 个指向任何 个继承 Object(或者NSObject)类的对象。需要注意的是id是  个指针，所以你在使 id的时候 需要加星号。 如id foo=nil定义  个nil指针，这个指针指向
 NSObject的 个任意 类。 id *foo=nil则定义  个指针，这个指针指向另 个指针，被指向的 这个指针指向NSObject的 个 类。 nil和C语 的NULL相同，在objc/objc.h中定义。nil表示 个Objctive-C对象，这个对象的指针指 向空(没有东 就是空)。  字  写的Nil和nil有 点  样，Nil定义 个指向空的类(是Class，  是对象)。 SEL是“selector”的 个类型，表示 个 法的名字 Method(我们常说的 法)表示 种类型，这种类型与selector和实现(implementation)相关 IMP定义为 id (*IMP) (id, SEL, ...)。这样说来， IMP是 个指向函数的指针，这个被指向的函数包 括id(“self”指针)，调 的SEL( 法名)，再加上 些其他参数.说  IMP就是实现 法。
192问:层和UIView的区别是 么 答:两者最 的区别是,图层 会直接渲染到屏幕上，UIView是iOS系统中界 元素的基础，所有的界  元素都是继承 它。它本身完全是由CoreAnimation来实现的。它真正的绘图部分，是由 个 CALayer类来管 。UIView本身 像是 个CALayer的管  。 个UIView上可以有n个CALayer， 每个layer显示 种东 ，增强UIView的展现能 。
193问:APNS 实现步骤
答:APNS 是Apple Push Notification Service(Apple Push服务 )的缩写，是苹果的服务 。
第 阶段:.net应 程序把要发送的消息、 的iPhone的标识打包，发给APNS。 第 阶段:APNS在 身的已注册Push服务的iPhone 表中，查找有相应标识的iPhone，并把消息 发到iPhone。
第三阶段:iPhone把发来的消息传递给相应的应 程序， 并且按照设定弹出Push通知。 http://blog.csdn.net/zhuqilin0/article/details/6527113 //消息推送机制
194问:ASIDownloadCache 设置下载缓存 答:它对Get请求的响应数据进 缓存(被缓存的数据必需是成功的200请求): [ASIHTTPRequest setDefaultCache:[ASIDownloadCache sharedCache]]; 当设置缓存策 后，所有的请求都被 动的缓存起来。 另外，如果仅仅希望某次请求使 缓存操作，也可以这样使 :
ASIHTTPRequest *request = [ASIHTTPRequest requestWithURL:url];
[request setDownloadCache:[ASIDownloadCache sharedCache]];
缓存存储 式
你可以设置缓存的数据需要保存多 时间，ASIHTTPRequest提供 两种策 : a，ASICacheForSessionDurationCacheStoragePolicy，默认策 ，基于session的缓存数据存 储。当下次运 或[ASIHTTPRequest clearSession]时，缓存将失效。 b，ASICachePermanentlyCacheStoragePolicy，把缓存数据永久保存在本地，
如:
ASIHTTPRequest *request = [ ASIHTTPRequest requestWithURL:url ];
[ request setCacheStoragePolicy:ASICachePermanentlyCacheStoragePolicy ];
195问:HTTP协议详解 答:HTTP是 个属于应 层的 向对象的协议，由于其简捷、快速的 式，适 于分布式超媒体信 息系统。 前在WWW中使 的是HTTP/1.0的第六版，HTTP/1.1的规范化 作正在进 之中。
 http(超 本传输协议)是 个基于请求与响应模式的、 状态的、应 层的协议，常基于TCP的 连接 式，HTTP1.1版本中给出 种持续连接的机制，绝 多数的Web开发，都是构建在HTTP协议 之上的Web应 。
HTTP协议的主要特点可概括如下:
1. 持客户/服务 模式。 2.简单快速:客户向服务 请求服务时，只需传送请求 法和 径。请求 法常 的有GET、HEAD、 POST。每种 法规定 客户与服务 联系的类型 同。由于HTTP协议简单，使得HTTP服务 的 程序规模 ，因 通信速度很快。 3.灵活:HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。 4. 连接: 连接的含义是限制每次连接只处  个请求。服务 处 完客户的请求，并收到客户 的应答后，即断开连接。采 这种 式可以节省传输时间。 5. 状态:HTTP协议是 状态协议。 状态是指协议对于事务处 没有记忆能 。缺少状态意味着 如果后续处 需要前 的信息，则它必须 传，这样可能导致每次连接传送的数据 增 。另    ，在服务  需要先前信息时它的应答就较快。
196问:TCP/UDP区别联系 答:TCP---传输控制协议,提供的是 向连接、可靠的字节流服务。当客户和服务 彼此交换数据前， 必须先在双 之间建  个TCP连接，之后才能传输数据。TCP提供超时 发，丢弃 复数据，检 验数据，流 控制等功能，保证数据能从 端传到另 端。 UDP--- 户数据报协议，是 个简单的 向数据报的运输层协议。UDP 提供可靠性，它只是把 应 程序传给IP层的数据报发送出去，但是并 能保证它们能到达 的地。由于UDP在传输数据报 前  在客户和服务 之间建  个连接，且没有超时 发等机制，故 传输速度很快 TCP(Transmission Control Protocol，传输控制协议)是基于连接的协议，也就是说，在正式收 发数据前，必须和对 建 可靠的连接。 个TCP连接必须要经过三次“对话”才能建 起来，我们 来看看这三次对话的简单过程:1.主机A向主机B发出连接请求数据包;2.主机B向主机A发送同意连 接和要求同步(同步就是两台主机 个在发送， 个在接收，协调 作)的数据包;3.主机A再发出  个数据包确认主机B的要求同步:“我现在就发，你接着吧!”，这是第三次对话。三次“对话”的  的是使数据包的发送和接收同步，经过三次“对话”之后，主机A才向主机B正式发送数据。 UDP(User Data Protocol， 户数据报协议)是与TCP相对应的协议。它是 向 连接的协议， 它 与对 建 连接， 是直接就把数据包发送过去! UDP适 于 次只传送少 数据、对可靠性 要求  的应 环境。
tcp协议和udp协议的差别
是否连接  向连接  向 连接
传输可靠性 可靠  可靠
应 场合 传输  数据 少 数据
速度 慢 快
197问:socket连接和http连接的区别 答:简单说，你浏览的  ( 址以http://开头)都是http协议传输到你的浏览 的,  http是基于 socket之上的。socket是 套完成tcp，udp协议的接 。 HTTP协议:简单对象访问协议，对应于应 层 ，HTTP协议是基于TCP连接的
tcp协议: 对应于传输层
ip协议: 对应于 络层 TCP/IP是传输层协议，主要解决数据如何在 络中传输; HTTP是应 层协议，主要解决如何包 装数据。
 Socket是对TCP/IP协议的封装，Socket本身并 是协议， 是 个调 接 (API)，通过Socket， 我们才能使 TCP/IP协议。 http连接:http连接就是所谓的短连接，即客户端向服务 端发送 次请求，服务 端响应后连接即 会断掉; socket连接:socket连接就是所谓的 连接， 论上客户端和服务 端 旦建 起连接将 会主动 断掉;但是由于各种环境因素可能会是连接断开， 如说:服务 端或客户端主机down ， 络故 障，或者两者之间 时间没有数据传输， 络防 墙可能会断开该连接以释放 络资源。所以当  个socket连接中没有数据的传输，那么为 维持连接需要发送 跳消息~~具体 跳消息格式是开发 者  定义的
我们已经知道 络中的进程是通过socket来通信的，那 么是socket呢 socket起源于Unix， Unix/ Linux基本哲学之 就是“ 切皆 件”，都可以 “打开open – 读写write/read – 关闭close” 模式来操作。我的 解就是Socket就是该模式的 个实现，socket即是 种特殊的 件， 些 socket函数就是对其进 的操作(读/写IO、打开、关闭)，这些函数我们在后 进 介绍。我们在 传输数据时，可以只使 (传输层)TCP/IP协议，但是那样的话，如果没有应 层，  法识别数 据内容，如果想要使传输的数据有意义，则必须使 到应 层协议，应 层协议有很多， 如HTTP、 FTP、TELNET等，也可以  定义应 层协议。WEB使 HTTP协议作应 层协议，以封装HTTP  本信息，然后使 TCP/IP做传输层协议将它发到 络上。 1)Socket是 个针对TCP和UDP编程的接 ，你可以借助它建 TCP连接等等。 TCP和UDP协议 属于传输层 。
 http是个应 层的协议，它实际上也建 在TCP协议之上。 (HTTP是轿 ，提供 封装或者显示数据的具体形式;Socket是发动机，提供  络通信的能 。) 2)Socket是对TCP/IP协议的封装，Socket本身并 是协议， 是 个调 接 (API)，通过 Socket，我们才能使 TCP/IP协议。Socket的出现只是使得程序员   地使 TCP/IP协议栈  已，是对TCP/IP协议的抽象，从 形成 我们知道的 些最基本的函数接 。
198问: 么是TCP连接的三次握  答:第 次握 :客户端发送syn包(syn=j)到服务 ，并进 SYN_SEND状态，等待服务 确认; 第 次握 :服务 收到syn包，必须确认客户的SYN(ack=j+1)，同时  也发送 个SYN包 (syn=k)，即SYN+ACK包，此时服务 进 SYN_RECV状态; 第三次握 :客户端收到服务 的SYN+ACK包，向服务 发送确认包ACK(ack=k+1)，此包发送完 毕，客户端和服务 进 ESTABLISHED状态，完成三次握 。 握 过程中传送的包  包含数据，三次握 完毕后，客户端与服务 才正式开始传送数据。 想 状态下，TCP连接 旦建 ，在通信双 中的任何  主动关闭连接之前，TCP 连接都将被 直保 持下去。断开连接时服务 和客户端均可以主动发起断开TCP连接的请求，断开过程需要经过“四次 握 ”(过程就 细写 ，就是服务 和客户端交互，最终确定断开)
199问:  Socket建  络连接的步骤 答:建 Socket连接 少需要 对套接字，其中 个运 于客户端，称为ClientSocket ，另 个运  于服务 端，称为ServerSocket 。 套接字之间的连接过程分为三个步骤:服务 监听，客户端请求，连接确认。 1。服务 监听:服务 端套接字并 定位具体的客户端套接字， 是处于等待连接的状态，实时监 控 络状态，等待客户端的连接请求。
 2。客户端请求:指客户端的套接字提出连接请求，要连接的 标是服务 端的套接字。为此，客户 端的套接字必须 先描述它要连接的服务 的套接字，指出服务 端套接字的地址和端 号，然后 就向服务 端套接字提出连接请求。 3。连接确认:当服务 端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套 接字的请求，建  个新的线程，把服务 端套接字的描述发给客户端， 旦客户端确认 此描述， 双 就正式建 连接。 服务 端套接字继续处于监听状态，继续接收其他客户端套接字的连接请 求。
200问:多线程 之 NSOperation 答:如果需要让线程同时并 运 多个，可以将线程加 队 (Queue)中，NSOperationQueue类 就是 个线程队 管 类，他提供 线程并 、队 的管 。可以认为NSOperationQueue就是  个线程管  ，通过addOperations 法，我们可以 次性把多个(数组形式)线程添加到队 中。 同时，NSOperationQueue允许通过setMaxConcurrentOperationCount 法设置队 的并 (同  时间)运 数 
201问:多线程 之 NSThread 答:NSThree是官 推荐的线程处  式，它在处 机制上，需要开发者负责 动管 Thread的 命 周期，包括 线程与主线程之间的同步等。线程共享同 应 程序的部分内存空间，它们拥有对数 据相同的访问权限。你得协调多个线程 对同 数据的访问， 般做法是在访问之前加锁，这会导致  定的性能开销。在 iOS 中我们可以使 多种形式的 thread。  其他两个轻 级 需要  管 线 程的 命周期，线程同步。 线程同步对数据的加锁会有 定的系统开销
202问:多线程 之 GCD
答:Grand Central Dispatch (GCD)是Apple开发的 个多核编程的解决 法。该 法在Mac OS X 10.6雪豹中 次推出，并随后被引 到 iOS4.0中。GCD是 个替代诸如NSThread, NSOperationQueue, NSInvocationOperation等技术的很 效和强 的技术，它看起来象就其它语  的闭包(Closure) 样，但苹果把它叫做blocks。
203问:Objective-C的优点
答:Objective-C语 有C++ Java等 向对象的特点，那是远远 能体现它的优点的。Objective-C 的优点是它是动态的。动态能 有三种:
动态类-运 时确定类的对象
动态绑定-运 时确定要调 的 法
动态加载--运 时为程序加载新的模块
204问:Socket连接和Http连接的区别 答:简单说，你浏览的  ( 址以http://开头)都是http协议传输到你的浏览 的,  http是基于 socket之上的。socket是 套完成tcp，udp协议的接 。 HTTP协议:简单对象访问协议，对应于应 层 ，HTTP协议是基于TCP连接的
tcp协议: 对应于传输层 ip协议: 对应于 络层
 TCP/IP是传输层协议，主要解决数据如何在 络中传输; HTTP是应 层协议，主要解决如何包 装数据。
Socket是对TCP/IP协议的封装，Socket本身并 是协议， 是 个调 接 (API)，通过Socket， 我们才能使 TCP/IP协议。
http连接:http连接就是所谓的短连接，即客户端向服务 端发送 次请求，服务 端响应后连接即 会断掉;
socket连接:socket连接就是所谓的 连接， 论上客户端和服务 端 旦建 起连接将 会主动 断掉;但是由于各种环境因素可能会是连接断开， 如说:服务 端或客户端主机down ， 络故 障，或者两者之间 时间没有数据传输， 络防 墙可能会断开该连接以释放 络资源。所以当  个socket连接中没有数据的传输，那么为 维持连接需要发送 跳消息~~具体 跳消息格式是开发 者  定义的
我们已经知道 络中的进程是通过socket来通信的，那 么是socket呢 socket起源于Unix， Unix/ Linux基本哲学之 就是“ 切皆 件”，都可以 “打开open – 读写write/read – 关闭close” 模式来操作。我的 解就是Socket就是该模式的 个实现，socket即是 种特殊的 件， 些 socket函数就是对其进 的操作(读/写IO、打开、关闭)，这些函数我们在后 进 介绍。我们在 传输数据时，可以只使 (传输层)TCP/IP协议，但是那样的话，如果没有应 层，  法识别数 据内容，如果想要使传输的数据有意义，则必须使 到应 层协议，应 层协议有很多， 如HTTP、 FTP、TELNET等，也可以  定义应 层协议。WEB使 HTTP协议作应 层协议，以封装HTTP  本信息，然后使 TCP/IP做传输层协议将它发到 络上。 1)Socket是 个针对TCP和UDP编程的接 ，你可以借助它建 TCP连接等等。 TCP和UDP协议 属于传输层 。
 http是个应 层的协议，它实际上也建 在TCP协议之上。 (HTTP是轿 ，提供 封装或者显示数据的具体形式;Socket是发动机，提供  络通信的能 。)
2)Socket是对TCP/IP协议的封装，Socket本身并 是协议， 是 个调 接 (API)，通过 Socket，我们才能使 TCP/IP协议。Socket的出现只是使得程序员   地使 TCP/IP协议栈  已，是对TCP/IP协议的抽象，从 形成 我们知道的 些最基本的函数接 。
205问:TCP/UDP区别联系 答:TCP---传输控制协议,提供的是 向连接、可靠的字节流服务。当客户和服务 彼此交换数据前， 必须先在双 之间建  个TCP连接，之后才能传输数据。TCP提供超时 发，丢弃 复数据，检 验数据，流 控制等功能，保证数据能从 端传到另 端。
UDP--- 户数据报协议，是 个简单的 向数据报的运输层协议。UDP 提供可靠性，它只是把 应 程序传给IP层的数据报发送出去，但是并 能保证它们能到达 的地。由于UDP在传输数据报 前  在客户和服务 之间建  个连接，且没有超时 发等机制，故 传输速度很快
TCP(Transmission Control Protocol，传输控制协议)是基于连接的协议，也就是说，在正式收 发数据前，必须和对 建 可靠的连接。 个TCP连接必须要经过三次“对话”才能建 起来，我们 来看看这三次对话的简单过程:1.主机A向主机B发出连接请求数据包;2.主机B向主机A发送同意连
 接和要求同步(同步就是两台主机 个在发送， 个在接收，协调 作)的数据包;3.主机A再发出  个数据包确认主机B的要求同步:“我现在就发，你接着吧!”，这是第三次对话。三次“对话”的  的是使数据包的发送和接收同步，经过三次“对话”之后，主机A才向主机B正式发送数据。 UDP(User Data Protocol， 户数据报协议)是与TCP相对应的协议。它是 向 连接的协议， 它 与对 建 连接， 是直接就把数据包发送过去! UDP适 于 次只传送少 数据、对可靠性 要求  的应 环境。
tcp协议和udp协议的差别 是否连接  向连接  向 连接 传输可靠性 可靠  可靠 应 场合 传输  数据 少 数据 速度 慢 快
206问:GCD 级 解
答:Grand Central Dispatch (GCD)是Apple开发的 个多核编程的较新的解决 法。在Mac OS X 10.6雪豹中 次推出，并在最近引 到 iOS4.0。  GCD是 个替代诸如NSThread等技术的很  效和强 的技术。GCD完全可以处 诸如数据锁定和资源泄 等复杂的异步编程问题。 
 GCD可以完成很多事情，但是这 仅关注在iOS应 中实现多线程所需的 些基础知识。  在 开始之前，需要 解是要提供给GCD队 的是代码块， 于在系统或者 户创建的的队 上调度运  。  声明 个队    
如下会返回 个 户创建的队 :
  dispatch_queue_t myQueue = dispatch_queue_create("com.iphonedevblog.post", NULL); 其中，第 个参数是标识队 的，第 个参数是 来定义队 的参数( 前  持，因此传  NULL)。 
执  个队   
 如下会异步执 传 的代码: 
 dispatch_async(myQueue, ^{ [self doSomething]; });其中， 先传 之前创建的队 ，然后提 供由队 运 的代码块。  
声明并执  个队     如果 需要保 要运 的队 的引 ，可以通过如下代码实现之前的功能:   dispatch_async(dispatch_queue_create ("com.iphonedevblog.post", NULL), ^{ [self
doSomething]; });  如果需要暂停 个队 ，可以调 如下代码。暂停 个队 会阻 和该队  相关的所有代码运 。  dispatch_suspend(myQueue);暂停 个队    如果暂停 个队  要忘记恢复。暂停和恢复的操作和内存管 中的retain和release类似。调  dispatch_suspend会增加暂停计数， dispatch_resume则会减少。队 只有在暂停计数变成零的 情况下才开始运 。dispatch_resume(myQueue);恢复 个队     从队 中在主线程运 代码    有些操作 法在异步队 运 ，因此必须在主线程(每个应 都有 个)上运 。UI绘图以及任何 对NSNotificationCenter的调 必须在主线程 进 。在另 个队 中访问主线程并运 代码的示  如下:  dispatch_sync(dispatch_get_main_queue(), ^{ [self dismissLoginWindow]; });注意， dispatch_suspend (以及dispatch_resume)在主线程上 起作 。 使 GCD，可以让你的程序 会失去响应. 多线程 容 使 ，  GCD，会让它变得简单。你  需专 进 线程管 , 很棒!
dispatch_queue_t t1=dispatch_queue_create("1", NULL);
dispatch_queue_t t2=dispatch_queue_create("2", NULL); dispatch_async(t1, ^{
 [self print1]; });
dispatch_async(t2, ^{ [self print2];
}); 207问:obj-c的优缺点 答:objc优点:
1) Cateogies
2) Posing
3) 动态识别
4) 指标计算
5)弹性讯息传递
6)  是 个过度复杂的 C 衍 语  7) Objective-C 与 C++ 可混合编程
缺点:
1)   援命名空间
2)   持运算符 载
3)   持多 继承
4) 使 动态运 时类型，所有的 法都是函数调 ，所以很多编译时优化 法都  到。(如内
联函数等)，性能低劣。
208问:  C++设计 个 能被继承的类
答:template <typename T friend T; private:
A() {} ~A() {}
};
class B : virtual public A<B public:
B() {}
~B() {} };
class C : virtual public B { public:
C() {}
~C() {} };
class A {
{
void main( void ) {
B b; //C c; return;
} 注意:构造函数是继承实现的关键,每次 类对象构造时, 先调 的是 类的构造函数,然后才 是  的。
209问: 同屏幕怎么适配
答:iphone, iphone3G, iphone3GS 320x480
iphone4, iphoen4S 640x960 retina
iphone5, iphone5S, 640x1136
写程序需要有 2 套图  demo.png demo@2x.png iphone5 适配. 1136/2-44-49
 [[UIScreen mainScreen] applicationFrame] = (320x460, 320x548) [[UIScreen mainScreen] bounds] = (320x480, 320x568)
iPad, iPad2, iPad Mini 1024x768
iPad3, iPad4, 2048x1563
210问:数据库能 能存图 ,怎么存
答:可以存图 , nsdata 存,但是我们 般 这样存,我们存 径把 图 存在沙盒中
211问:成员变 的作 域有哪 种 答:@public, @protected, @private
212问: OC 中有私有成员变 吗 答:在.m  件中实现 名类别 Category
213问: OC 中有私有 法吗
答:在.m  件中实现 Category,只是在.m 中 法
214问:解释关键字static、const、inline、 static inline、volatile 答:static 修饰变 表示静态变 ,根据作 域  同
extern 和 static 是相反的。
Static 修饰函数是表示函数在本 件有效
Static inline 联合在 起表示内联函数。类似于宏
Const 修饰变 和变 的内容只读
Volatile 表示每次都从内存中真正的读取。主要是 于嵌 式中, 读取硬件。
215问: 么是通知中 
答:通知中 是多对多的平等的通讯模式。主要 在多个对象之间松 耦合的通讯模式。对象和对象之 间通过通知的名字就可以进 关 联通讯。
216问: 么是委托代 模式
答:委托代 是 2 个对象之间的 种通讯 式。   使 协议,代 ，另外  实现协议,类似于回调,blocks 等语法。  的是为 软件设计的低耦合
217问:ScrollView 的复 机制
答:滚动过程中把 scrollview   的 超出屏幕的 view 放在  队 中, 在滚动过程中如果有新的 view 进来, 先从  队 中取得 view, 如果没有就新创建 个。
218问:Frame 和 bounds 有 么区别
答:Frame 是 视图相对于 视图的坐标系统,x y width height, bounds 是 uiview   的坐标系统 bounds x y=0
219问:Blocks 语法有 么优点
答:Blocks 类似于 c 中回调函数,和代 类似的,类似于 java 中的 listener  名函数
 220
C++怎么调  C 的 法 答:extern “C” int foo(){ };
221问:NSArray, NSDictionary 这些是如何做的
答:NSArray 使 链表做的 NSDictionary 使  hash 表做出来的
222问:遍历数组的三种 式有 么区别
答:for forin iterator
forin 也叫快速循环。为 么他叫快速循环,原因是速度快 为  么速度快 可以通过优化提前把 forin   的 array 数组数组全部 导 到 cpu cache 中。因为 cache 访问速度把内存快 少 100 倍
223问:HTTP 协议、Socket 协议的区别
答:HTTP 启动 个 NSUrlConnection 在 didFinish  连接, 期的和服务 保持连接。 的是为  避免每次都要连 接。因为和服务 连接的过程 常复杂。也耗时。所以连接 次。 HTTP 短连接只 要处 完就断掉,下次 新连接 对服务 的负担  .
 连接对服务 压 很 。
224问:怎么实现对象的本地存储 答:归档 NSArchieve
225问:copy 和 mutableCopy 区别
答:copy 是拷 ,mutableCopy 是可变拷 , 如把 NSString 通过 mutableCopy 变 成 NSMutableString, 把 NSArray 通 过 mutableCopy 变成 NSMutableArray.
NSString -à NSMutableString, NSArrayà NSMutableArray, NSDictionaryà NSMutableDictionary, NSData- NSMutableData;
226copy 是深拷 还是浅拷 
答:copy 缺省是浅拷 .但是 copy 也可以做成深拷 
227问: retain、copy、readonly、readwrite 的区别
答:retain对计数 +1 copy是创建 个新对象,readonly只产 getter 函数,readwrite 产  setter, getter 函数
228问:解释OC 的id类型
答:id 是泛型指针,可以指向任何 oc 类型,
id =~ NSObject *
id 是 objc_object 的 typedef,执  Class 对象
229问:本地存储 式有哪 种
答:数据库,nsuserdefauts, file , plist,archieve 归档  较 的就放在 nsuserdefaults(以 件设计的 存放在 Libraray 中) 数据库  适合存放 条 条的记录
file  般存普通 件,图 ,视频, 频等
 plist 存放  nsuserdefaults   些的 归档 nsarchieve 可以存对象
230问:POST 和 GET 有何区别
答:GET 和 POST 都是 HTTP 请求 式的 2 中。
POST 是安全的。GET 是 安全的。GET 是放在浏览 中地址   出来 。POST  会。但是在 App 上 GET 和 POST 都看  。 GET 和 POST 都是和服务 提交参数/通讯的 种 式。
GET 参数 能太 <1024B POST 没有限制<4G
GET  能上传 件, POST 可以上传 件
231问:POST 请求的链接参数怎么拼接
答:POST 参数有 2 中, 种 件 POST  种  件 POST,对于   件 POST 格 式 ( form-data/ x-www-urlencoded ) 是 name=xxx&id=22&sxx=33
对于 件 POST(multiple/form-data)
232 C/C++和 OC 怎么混  答:.m - .mm
233问:NSString *name = @”1000phone.com”,[name release]会出现 么 情况 答:这个代码 满  objective-c 的内存管   法则。没有 alloc 就
release
234问: 么是单 模式
答:单 就是在多个对象之间共享数据,类似于全局变 , 如数据 库打开 次,多个界 都可以使 
235问:解释KVC和KVO
答:Key value coding, Key value observer.
Kvc 是 径访问的规范,kvo 是观察某个变 的变化过程
KVO 可以观察某个对象的变 变化过程,KVC 是满 被观察的编 码规范。 KVC/KVO 类似于代 ,通知中 。都是 种通讯 法。
236问:OC  怎么实现多继承
答:OC 没有多继承。 协议实现多继承,把协议中的 法在实现的类 中 写 C++有多继承,多继承其 实 好 很  。C++多继承很容 出现   性 如:
类 A { int a;}
类B: 类A,类C:类A
类D : B, C那么 int a到底是继承 谁的 单继承
237问:解释 const, static, inline 关键字
答:const 修饰指针,或者常 , 如 可变,
static 修饰变 表示作 域, 如全局的私有变 ,函数内部的 static 是内部的私有变 。
Static 修饰函数表示函数是 件作 域
Inline 表示内联。 般来说 inline 需要和 static 联合   般 法是 static inline int max(int a, int b) {
 return a b a:b; }
static inline作 是和宏类似,只 过是  调试(宏 能断掉调 试,static inline 可以)。运 时候是  样的。
 般 c/c++短 的函数都  static inline 内联函数
238问:下拉刷新需要实现哪 个 法,刷新流程
答:下来 般使  EGORefresh 进 。原 是   scrollview 的反弹效 果把刷新 view 加载 scrollview 的负坐标上。通过代  法去触发。
239问:如何调  iOS 打电话,发短信
答:[[UIApplication sharedApplication] openURL:[NSURL urlwithString:@”tel://1543434”]]; [[UIApplication sharedApplication] openURL:[NSURL urlwithString:@”sms://1572234”]]; [[UIApplication sharedApplication] openURL:[NSURL urlwithString:@”mail:// hello@hello.com”]];
[[UIApplication sharedApplication] openURL:[NSURL urlwithString:@”http://1000phone.net”]];
240问:Objective-C 如何和 javascript 通讯
答:Objective-c --à javascript ---à html   内容
Objective-c 是通过 stringByEvaluatingJavaScriptFromString 函数 来执  html 中的 javascript Javascript -à url --à objective-c 本地 法
需要通过 uiwebview 中的代 函数
-(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;
241问:怎么解析 HTML 源码
答: 般 HTML 是通过 webview 来显示的。 般简单的 html 是通过 解析字符 来解析。没有成熟 现成 html 解析 。  般服务 去抓取html解析html形成json xml 件供 机端读 取。
242UITextField、UITextView 的区别 答:UITextField 能换 ,UITextView可以换 。TextView可以点击 字体放 ,可以复制。相同的是他们都有代 类似
243问:代 和通知中 / 播/NoficationCenter  么区别 答:代 主要是反向传值, 般 来 1:1 的两个对象通讯上。 通知中 是通过注册然后接收事件的 种 n:n(多对对) 的 法
244问:代 和协议 么区别
答:代 是 种概念,协议是 种技术,代 是 协议来实现的,代  是 2 个对象之间通讯的 种 式。 代 主要做反向传值的。实现系统的 些回调 法, 如 scrollview 滑动事件,选择照 ,asi  络下载 完成等。
245问:  么是异步
答:相对于同步来说,单独起 个或者多线程去处  异步是 个概念 线程还是 个技术,异步就是 线 程这种技术 实现的
  如界 下载数据,我们启动 个异步任务 ASI 去 络下载数据, 然后异步刷新界 ,我们 需等待  络数据下载完成.
246问:解释多线程、NSThread 、NSOperation、GCD
答:多线程在 iOS  的很多, 如每个 asi 请求,sdwebimage 请求,数 据请求等待等 络数据处 ,多 线程/异步就是主要是为 界 流 畅,防 界 假死。
每 个 ASI 请求就是 个 NSOperation
每 个 NSUrlConnnection 也是 个线程
Nsthread 是创建线程的 个通 的类。 如线程创建,取消,开 始等。
Nsoperation 就是 个简单的以任务为导向的多线程模型。 的是 为  懂操作系统, 懂线程的  使 的
GCD 类似于 NSOperation, 是 个 blocks 版本的线程模型。
247 C 和 OC 有哪些基本数据类型
答:C char, short, int, struct, union, enum NSString, nsarray, nsdictionary.
248问:类别和继承 么区别
答:类别/类 /Category 很  给现有类添加 法。但是 能添加成 员变 , 名类除外, 如可以给 NSString 增加 法,给 UINavigationBar 增加 法, 如 SDWebImage 给 UIImageView 增 加  setImageWithURL: 法。类别对于使 者很   继承可以给现有的类增加 法和成员变 。继承对于使 者来 说 如类别   , 如对 SDWebImage 如果 继承的话,那么就 需要写 个类似于 QFImageView 然后把所有的 UIImageView 改成
QFImageView,这  如类别  
249问:类别的作  答:类别是给已有的类添加 法,但是 能添加变 , 名类别除外 ()
250问:你如何 解复 机制
答: 般是对 uiscrollview 做复 机制,因为 uiscrollview 滚动窗 没 有复 ,所以要做,原 就是超过 屏幕的 view  能销毁, 要放 在复 队 /池  存放起来,然后以后要在scrollview显示view  先  要 alloc 创建, 要 先去复 池  找有没有可复 的 view,如果没有就 alloc 如果有就直接 。
251问:瀑布流怎么 解和实现的
答:1. 如果简单的来说,  3 个 tableview 就可以实现瀑布流,3 个 tableview 实现联动滚动。
2. 其实最好的做法在 scrollview 上使  3 个复 队 ,如果 种  个 cell 超过屏幕, 能 release,  是把它回收到复 队 中, 如果要创建 个 cell, 先从复 队 中取 个,然后使 。
252常 的点击 势有哪 种
答:UIPinchGuesture, UITapGuesture, UISwipGuesture, UILongPressGuesture, UIRotationGuesture
253问: 么是 MVC 模式
 答:Model, View, Controller, 是 iOS 开发中的典型的设计模式。 如通 过 http 把 络数据下载并 解析然后存于  的数据模型 Model 中, 然后通知 controller 去刷新界 ,通过 controller 讲 Model 和 View 进 关联,这种模式就叫 mvc。
这样的好处是,可以隔离数据模型 model 和 View 界 。遵循 低 耦合的设计思想.
254问:界 之间传值有哪 种 法 答:单 ,代 ,直接赋值,通知中 / 播, 数据库等多种
255问:解释 TCP/IP 协议
答:TCP/IP 是 络开发中常 的传输协议,他传输和 udp 相 是可靠 的。http 是基于 tcp/ip 的主要  户互联 的协议
所谓可靠是 tcp 传输对 会给 个 ACK 信号(确认信号)
tcp 传输 如 udp 快,吞吐  如 udp  
tcp 是顺序的,udp 是 序的
tcp 会保持连接,udp  会保持连接
256问:浅拷 与深拷 的区别 或者 么是深拷   么是浅拷  答:copy, mutableCopy
@interface A {
B *b; } 浅拷 只是拷 对象本身, 会对  的 对象进 步拷 
深拷 会对 对象以及 对象的 对象进 步拷 
257问:C语  的数组与OC数组的区别
答:OC 数组是 个对象,有  的 法,c 没有都需要  写 C 数组删除是需要后 往前移动,oc 数组  动处 
258问:你如何 解 iOS 内存管 
答:1. new alloc copy retain这些对象我们都要主动的release或者 autorelease
2. 如果是类 法创建的对象,那么系统 动释放池 动在适当的 时候会帮我们 release 3. ARC xcode  动会帮我们  智能的添加 release autorelease 操 作
259问:在项  么时候选择使 GCD， 么时候选择NSOperation 答:项 中使 NSOperation的优点是NSOperation是对线程的 度抽象，在项 中使 它，会使项  的程序结构 好， 类化NSOperation的设计思 ，是具有 向对象的优点(复 、封装)，使 得实现是多线程 持， 接 简单，建议在复杂项 中使 。 项 中使 GCD的优点是GCD本身 常简单、  ，对于 复杂的多线程操作，会节省代码 ，  Block参数的使 ，会是代码 为 读，建议在简单项 中使 。
260问:ASI 原 是 么 请举 来说明你平时 的那些 件
答:ASI 使 apple底层 CFNetworking 框架实现的，  是  Socket 套接字实现的。 他是 个基 于NSOperation(抽象类)的线程处  络框架 CFNetwork 是基于InputSteam / Outstream 流的  式管 数据，它内部使  多线程异步模式进 数据的通讯， 如数据上传进度，下载进度，缓 存的管 机制，  件下载，  件上传，安全机制。
 261问: 么是block block 实现原  多线程与block有 么关系 写个  。 答:1  么是block
对于闭包(block),有很多定义，其中闭包就是能够读取其它函数内部变 的函数，这个定义即接近 本质 较好 解。对于刚接触Block的同学，会觉得有些绕，因为我们习惯写这样的程序main(){ funA();} funA(){funB();} funB(){.....}; 就是函数main调 函数A，函数A调 函数B... 函数们依次顺序 执 ，但现实中 全是这样的， 如项 经 M， 下有3个程序员A、B、C，当他给程序员A安排 实现功能F1时，他并 等着A完成之后，再去安排B去实现F2， 是安排给A功能F1，B功能F2，C功 能F3，然后可能去写技术 档， 当A遇到问题时，他会来找项 经 M，当B做完时，会通知M， 这就是 个异步执 的  。在这种情形下，Block 可 显身 ，因为在项 经 M，给A安排  作时，同时会告诉A若果遇到困难，如何能找到他报告问题( 如打他 机号)，这就是项 经 M 给A的 个回调接 ，要回掉的操作， 如接到电话，百度查询后，返回  内容给A，这就是 个 Block，在M交待 作时，已经定义好，并且取得 F1的任务号(局部变 )，却是在当A遇到问题 时，才调 执 ，跨函数在项 经 M查询百度，获得结果后回调该block。
2 block 实现原 
Objective-C是对C语 的扩展，block的实现是基于指针和函数指针。 从计算语 的发展，最早的goto， 级语 的指针，到 向对象语 的block，从机 的思维， 步 步接近 的思维，以  开发 员 为 效、直接的描述出现实的逻辑(需求)。 下 是两篇很好的介绍block实现的博 
iOS中block实现的探究
谈Objective-C Block的实现
3 block的使 
使 实  cocoaTouch框架下动画效果的Block的调 
使 typed声明block
typedef void(^didFinishBlock) (NSObject *ob); 这就声明  个didFinishBlock类型的block，
然后 可 
@property (nonatomic,copy) didFinishBlock finishBlock; 声明 个blokc对象，注意对象属性设置为copy，接到block 参数时， 会 动复制 份。
__block是 种特殊类型， 使 该关键字声明的局部变 ，可以被block所改变，并且其在原函数中的值会被改变。
4 常 系  试题  试时， 试官会先问 些，是否 解block，是否使 过block，这些问题相当于开场 ，往往是 下  系 问题的开始，所以 定要如实根据  的情况回答。
1 使 block和使 delegate完成委托模式有 么优点
  先要 解 么是委托模式，委托模式在iOS中  应 ，其在设计模式中是适配 模式中的对象适 配 ，Objective-C中使 id类型指向 切对象，使委托模式 为简洁。 解委托模式的细节:
iOS设计模式----委托模式 使 block实现委托模式，其优点是回调的block代码块定义在委托对象函数内部，使代码 为紧凑 ;
适配对象 再需要实现具体某个protocol，代码 为简洁。
2 多线程与block
GCD与Block
使  dispatch_async 系  法，可以以指定的 式执 block GCD编程实 
dispatch_async的完整定义 void dispatch_async( dispatch_queue_t queue, dispatch_block_t block);
功能:在指定的队  提交 个异步执 的block， 阻塞当前线程
通过queue来控制block执 的线程。主线程执 前 定义的 finishBlock对象 dispatch_async(dispatch_get_main_queue(),^(void){finishBlock();});
262问:属性readwrite，readonly，assign，retain，copy，nonatomic 各是 么作 ，在那种情况 下 
答:readwrite 是可读可写特性;需要 成getter 法和setter 法时
readonly 是只读特性 只会 成getter 法  会 成setter 法 ; 希望属性在类外改变
assign 是赋值特性，setter 法将传 参数赋值给实 变 ;仅设置变 时;
retain 表示持有特性，setter 法将传 参数先保 ，再赋值，传 参数的retaincount会+1;
copy 表示赋值特性，setter 法将传 对象复制 份;需要完全 份新的变 时。
nonatomic  原 操作，决定编译  成的setter getter是否是原 操作，atomic表示多线程安 全， 般使 nonatomic。
263问:对于语 NSString*obj = [[NSData alloc] init]; obj在编译时和运 时分别时 么类型的对象 答:编译时是NSString的类型;运 时是NSData类型的对象。
264问:如何 解线程
答:(1)iOS 线程是为 界 流程，防   假死。 如解析 个  件， 如 个地区的详情 件， 解析时间 如是10秒，那么  线程的话，界 就会假死、卡顿10秒左右。 户体验差，那么解决  法就是很快把界 暂时 启动线程去后台解析;
 (2)再 如做图 滤镜，图像操作会耗费很多时间， 如5秒左右，界 就会假死; (3) 络下载数据， 于 络状态好坏时间 确定，那么我们需要使 线程 来处 这中 确定的 关系， 定数据接收完成，那么就可以通知主线程处 ; (4)再 如读取数据全国电话号码数据库，时间很 ，那么就需要通过线程来做; (5)特别强调的是，线程 是快，就是为 给 户 个快的假象.
265问:描述 下iOS SDK中如何实现MVC的开发模式 答:MVC是模型、试图、控制开发模式，对于iOS SDK，所有的View都是视图层的，它应该独 于模 型层，由视图控制层来控制。所有的 户数据都是模型层，它应该独 于视图。所有的 ViewController都是控制层，由它负责控制视图，访问模型数据。
266问:Objective-C如何对内存管 的,说说你的看法和解决 法 答:Objective-C的内存管 主要有三种 式ARC( 动内存计数)、 动内存计数、内存池。
267问:说说为 么要 RAC，它是如何简化代码的 答:ReactiveCocoa(其简称为RAC)是由GitHub开源的 个应 于iOS和OS X开发的新框架。RAC 具有函数式编程和响应式编程的特性，可以  简化你的代码，并且 前看来安全可靠。 RAC通过如下 点来达到简化代码的作 :
1、事件监听
RAC最基本的  使 技巧就是对事件的监听，在iOS开发中，我们所说的点击事件其实就是 target-action，接触过iOS开发的 都 会陌 UIControlEventTouchUpInside，这就是按下并松开 的动作， 仅仅是UIButton，还有UITextField也有 标-动作模式。
2、代 
 RAC写代 是有局限的，它只能实现返回值为void的代  法;
为 么要 RAC写代  还是四个字“简化代码”。为 么在这 强调这个，是因为RAC的优点在代
  法中体现得 漓尽致。
3、通知
开发中通知是 个 较常 的功能，主要的应 场景是某个  进 数据 传需要 新model但是 点击返回栈时 会刷新返回界 的数据，这时可以 通知来 新另 个  的数据，我们也可以在 另 个  的ViewDidAppear 法中刷新数据。
4、KVO RAC中的KVO 部分都是宏定义，所以代码异常简洁，简单来说就是RACObserve(TARGET,
KEYPATH)这种形式，TARGET是监听 标，KEYPATH是要观察的属性值，举个  ，如果 UIScrollView滚动则输出success。
268问:runtime如何实现weak变 的 动置nil 答:runtime对注册的类会进 布局，对于weak对象会放  个hash表中。  weak指向的对象内存 地址作为key，当此对象的引 计数为0的时候会dealloc。假如weak指向的对象内存地址是a，那么 就会以a为键，在这个 weak 表中搜索，找到所有以a为键的weak对象，从 设置为nil。 weak修饰的指针默认值是nil(在Objective-C中向nil发送消息是安全的)
 269问:objc_msgForward函数是做 么的，直接调 它将会发  么 答:_objc_msgForward是IMP类型， 于消息转发的:当向 个对象发送 条消息，但它并没有实现 的时候，_objc_msgForward会尝试做消息转发。
IMP msgForward = _objc_msgForward; 如果 动调 objcmsgForward，将跳过查找IMP的过程， 是直接触发“消息转发”，进 如下流程 :
• 第 步:+ (BOOL)resolveInstanceMethod:(SEL)sel实现 法，指定是否动态添加 法。若返 回NO，则进 下 步，若返回YES，则通过class_addMethod函数动态地添加 法，消息得到处 ， 此流程完毕。
• 第 步:在第 步返回的是NO时，就会进 - (id)forwardingTargetForSelector:(SEL)aSelector 法，这是运 时给我们的第 次机会， 于指 定哪个对象响应这个selector。 能指定为self。若返回nil，表示没有响应者，则会进 第三步。若 返回某个对象，则会调 该对象的 法。
• 第三步:若第 步返回的是nil，则我们 先要通过- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector指定 法签名，若返回nil，则表示 处 。若返回  法签名，则会进 下 步。
• •
第四步:当第三步返回 法 法签名后，就会调 - (void)forwardInvocation:(NSInvocation *)anInvocation 法，我们可以通过anInvocation对象做很多处 ， 如修改实现 法，修改响应 对象等
第五步:若没有实现- (void)forwardInvocation:(NSInvocation *)anInvocation 法，那么会进  - (void)doesNotRecognizeSelector:(SEL)aSelector 法。若我们没有实现这个 法，那么就 会crash，然后提示打 到响应的 法。到此，动态解析的流程就结束 。
270问:runtime如何通过selector找到对应的IMP地址 答:每个selector都与对应的IMP是  对应的关系，通过selector就可以直接找到对应的IMP:
271问:为 么其他语  叫函数调 ， objective c 则是给对象发消息(或者谈下对runtime的  解)
答:先来看看怎么 解发送消息的含义:
[receiver message]会被编译 转化为:
objc_msgSend(receiver, selector)
如果消息含有参数，则为:
objc_msgSend(receiver, selector, arg1, arg2, ...) 如果消息的接收者能够找到对应的selector，那么就相当于直接执  接收者这个对象的特定 法; 否则，消息要么被转发，或是临时向接收者动态添加这个selector对应的实现内容，要么就 脆玩完 崩溃掉。
现在可以看出[receiver message]真的 是 个简简单单的 法调 。因为这只是在编译阶段确定  要向接收者发送message这条消息， receive将要如何响应这条消息，那就要看运 时发 的情况 来决定 。
OC 的 Runtime 铸就 它动态语 的特性，Objc Runtime使得C具有  向对象能 ，在程序运  时创建，检查，修改类、对象和它们的 法。可以使 runtime的 系  法实现。
 顺 附上OC中 个类的数据结构 /usr/include/objc/runtime.h struct objc_class {
Class isa OBJC_ISA_AVAILABILITY; //isa指针指向Meta Class，因为Objc的类的本身也是 个 Object，为 处 这个关系，runtime就创造 Meta Class，当给类发送[NSObject alloc]这样消息 时，实际上是把这个消息发给 Class Object
#if !__OBJC2__ Class super_class
OBJC2_UNAVAILABLE; //  类 const char *name
OBJC2_UNAVAILABLE; // 类名 long version
OBJC2_UNAVAILABLE; // 类的版本信息，默认为0 long info
OBJC2_UNAVAILABLE; // 类信息，供运 期使 的 些位标识 long instance_size
OBJC2_UNAVAILABLE; // 该类的实 变    struct objc_ivar_list *ivars
OBJC2_UNAVAILABLE; // 该类的成员变 链表 struct objc_method_list **methodLists OBJC2_UNAVAILABLE; //  法定义的链表
struct objc_cache *cache
OBJC2_UNAVAILABLE; //  法缓存，对象接到 个消息会根据isa指针查找消息对象，这时会在 method Lists中遍历，如果cache ，常 的 法调 时就能够提 调 的效率。
struct objc_protocol_list *protocols OBJC2_UNAVAILABLE; // 协议链表
#endif
} OBJC2_UNAVAILABLE; OC中 个类的对象实 的数据结构(/usr/include/objc/objc.h):
typedef struct objc_class *Class; /// Represents an instance of a class. struct objc_object {
Class isa
OBJC_ISA_AVAILABILITY;
}; /// A pointer to an instance of a class. typedef struct objc_object *id;
向object发送消息时，Runtime库会根据object的isa指针找到这个实 object所属于的类，然后在 类的 法 表以及 类 法 表寻找对应的 法运 。id是 个objc_object结构类型的指针，这个 类型的对象能够转换成任何 种对象。
然后再来看看消息发送的函数:objc_msgSend函数 在引 中已经对objc_msgSend进   点介绍，看起来像是objc_msgSend返回 数据，其实 objc_msgSend从 返回数据 是你的 法被调 后返回 数据。下 详细叙述下消息发送步骤:
 检测这个 selector 是 是要忽 的。 如 Mac OS X 开发，有 垃圾回收就  会 retain,release 这些函数 。
检测这个 target 是 是 nil 对象。ObjC 的特性是允许对 个 nil 对象执 任何 个 法 会 Crash， 因为会被忽 掉。
如果上 两个都过 ，那就开始查找这个类的 IMP，先从 cache   找，完 找得到就跳到对应的 函数去执 。
如果 cache 找 到就找 下 法分发表。 如果分发表找 到就到超类的分发表去找， 直找，直到找到NSObject类为 。 如果还找 到就要开始进 动态 法解析 ，后 会提到。
后 还有: 动态 法解析resolveThisMethodDynamically 消息转发forwardingTargetForSelector
272问:能否向编译后得到的类中增加实 变  能否向运 时创建的类中添加实 变  为 么 答:-  能向编译后得到的类中增加实 变 ;
- 能向运 时创建的类中添加实 变 ;
解释如下:
因为编译后的类已经注册在 runtime 中，类结构体中的 objc_ivar_list 实 变 的链表 和 instance_size 实 变 的内存  已经确定，同时runtime 会调  class_setIvarLayout 或 class_setWeakIvarLayout 来处  strong weak 引 。所以 能向存在的类中添加实 变 ; 运 时创建的类是可以添加实 变 ，调  class_addIvar 函数。但是得在调  objc_allocateClassPair 之后，objc_registerClassPair 之前，原因同上。
273问:Toll-Free Bridging 是 么  么情况下会使 
答:Toll-Free Bridging 于在Foundation对象与Core Foundation对象之间交换数据,俗称桥接; 在ARC环境下,Foundation对象转成 Core Foundation对象; 使 __bridge桥接以后ARC会 动管 2个对象;
使 __bridge_retained桥接需要 动释放Core Foundation对象;
在ARC环境下, Core Foundation对象转成 Foundation对象;
使 __bridge桥接,如果Core Foundation对象被释放,Foundation对象也同时 能使  ,需要 动管  Core Foundation对象;
使 __bridge_transfer桥接,系统会 动管 2个对象。
274问:Runtime的相关术语有哪些 答:1.SEL
它是selector在 Objc 中的表示(Swift 中是 Selector 类)。selector 是 法选择 ，其实作 就和 名字 样， 常 活中，我们通过 名辨别谁是谁，注意 Objc 在相同的类中 会有命名相同的两 个 法。selector 对 法名进 包装，以 找到对应的 法实现。它的数据结构是:
typedef struct objc_selector *SEL;
我们可以看出它是个映射到 法的 C 字符 ，你可以通过 Objc 编译  命令@selector() 或者 Runtime 系统的 sel_registerName 函数来获取 个 SEL 类型的 法选择 。 注意: 同类中相同名字的 法所对应的 selector 是相同的，由于变 的类型 同，所以 会导致 它们调  法实现混乱。
 2.id
id 是 个参数类型，它是指向某个类的实 的指针。定义如下:
typedef struct objc_object *id;
struct objc_object { Class isa; };
以上定义，看到 objc_object 结构体包含 个 isa 指针，根据 isa 指针就可以找到对象所属的类。 注意:isa 指针在代码运 时并 总指向实 对象所属的类型，所以 能依靠它来确定类型，要想确 定类型还是需要 对象的 -class  法。PS:KVO 的实现机 就是将被观察对象的 isa 指针指向 个 中间类  是真实类型。
3.Class
typedef struct objc_class *Class;
Class 其实是指向 objc_class 结构体的指针。objc_class 的数据结构如下:
struct objc_class {
Class isa OBJC_ISA_AVAILABILITY;
#if !__OBJC2__
Class super_class
const char *name
long version
long info
long instance_size
struct objc_ivar_list *ivars
struct objc_method_list **methodLists struct objc_cache *cache
struct objc_protocol_list *protocols
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE;
#endif
} OBJC2_UNAVAILABLE;
从 objc_class 可以看到， 个运 时类中关联 它的 类指针、类名、成员变 、 法、缓存以及 附属的协议。
其中 objc_ivar_list 和 objc_method_list 分别是成员变  表和 法 表:
// 成员变  表 struct objc_ivar_list {
int ivar_count #ifdef __LP64__
int space #endif
/* variable length structure */
struct objc_ivar ivar_list[1] }
//  法 表
struct objc_method_list {
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
struct objc_method_list *obsolete
OBJC2_UNAVAILABLE;
 int method_count #ifdef __LP64__
int space #endif
/* variable length structure */ struct objc_method method_list[1]
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE;
}
由此可 ，我们可以动态修改 *methodList 的值来添加成员 法，这也是 Category 实现的原 ， 同样解释  Category  能添加属性的原因。
objc_ivar_list 结构体 来存储成员变 的 表，  objc_ivar 则是存储 单个成员变 的信息;同  ，objc_method_list 结构体存储着 法数组的 表， 单个 法的信息则由 objc_method 结构体 存储。
值得注意的时，objc_class 中也有 个 isa 指针，这说明 Objc 类本身也是 个对象。为 处 类 和对象的关系，Runtime 库创建  种叫做 Meta Class(元类) 的东 ，类对象所属的类就叫做元类。 Meta Class 表述 类对象本身所具备的元数据。
我们所熟悉的类 法，就源 于 Meta Class。我们可以 解为类 法就是类对象的实  法。每个 类仅有 个类对象， 每个类对象仅有 个与之相关的元类。
当你发出 个类似 [NSObject alloc](类 法) 的消息时，实际上，这个消息被发送给  个类对象 (Class Object)，这个类对象必须是 个元类的实 ， 这个元类同时也是 个根元类(Root Meta Class)的实 。所有元类的 isa 指针最终都指向根元类。
所以当 [NSObject alloc] 这条消息发送给类对象的时候，运 时代码 objc_msgSend() 会去它元类 中查找能够响应消息的 法实现，如果找到 ，就会对这个类对象执  法调 。
最后 objc_class 中还有 个 objc_cache ，缓存，它的作 很 要，后 会提到。
4.Method
Method 代表类中某个 法的类型
typedef struct objc_method *Method; struct objc_method {
SEL method_name char *method_types IMP method_imp
}
objc_method 存储  法名， 法类型和 法实现:
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE;
 法名类型为 SEL
 法类型 method_types 是个 char 指针，存储 法的参数类型和返回值类型
 method_imp 指向  法的实现，本质是 个函数指针 Ivar
Ivar 是表示成员变 的类型。
typedef struct objc_ivar *Ivar; struct objc_ivar {
char *ivar_name char *ivar_type int ivar_offset
#ifdef __LP64__ int space
#endif
}
其中 ivar_offset 是基地址偏移字节
5.IMP IMP在objc.h中的定义是: typedef id (*IMP)(id, SEL, ...);
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
它就是 个函数指针，这是由编译  成的。当你发起 个 ObjC 消息之后，最终它会执 的那段 代码，就是由这个函数指针指定的。  IMP 这个函数指针就指向 这个 法的实现。
如果得到 执 某个实 某个 法的  ，我们就可以绕开消息传递阶段，直接执  法，这在后   Cache 中会提到。
你会发现 IMP 指向的 法与 objc_msgSend 函数类型相同，参数都包含 id 和 SEL 类型。每个  法名都对应 个 SEL 类型的 法选择 ， 每个实 对象中的 SEL 对应的 法实现肯定是唯 的， 通过 组 id和 SEL 参数就能确定唯 的 法实现地址。
  个确定的 法也只有唯 的 组 id 和 SEL 参数。
6.Cache
Cache 定义如下:
typedef struct objc_cache *Cache struct objc_cache {
unsigned int mask /* total = mask + 1 */ unsigned int occupied
Method buckets[1]
OBJC2_UNAVAILABLE; OBJC2_UNAVAILABLE;
OBJC2_UNAVAILABLE;
};
Cache 为 法调 的性能进 优化，每当实 对象接收到 个消息时，它 会直接在 isa 指针指向 的类的 法 表中遍历查找能够响应的 法，因为每次都要查找效率太低 ， 是优先在 Cache 中 查找。
Runtime 系统会把被调 的 法存到 Cache 中，如果 个 法被调 ，那么它有可能今后还会被调  ，下次查找的时候就会效率  。就像计算机组成原 中 CPU 绕过主存先访问 Cache  样。
 7.Property
typedef struct objc_property *Property;
typedef struct objc_property *objc_property_t;//这个 常  可以通过class_copyPropertyList 和 protocol_copyPropertyList  法获取类和协议中的属性:
objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount) objc_property_t *protocol_copyPropertyList(Protocol *proto, unsigned int *outCount) 注意:
返回的是属性 表， 表中每个元素都是 个 objc_property_t 指针
#import <Foundation/Foundation.h
@interface Person : NSObject
/** 姓名 */
@property (strong, nonatomic) NSString *name;
/** age */
@property (assign, nonatomic) int age;
/** weight */
@property (assign, nonatomic) double weight;
@end
以上是 个 Person 类，有3个属性。让我们 上述 法获取类的运 时属性。
unsigned int outCount = 0;
objc_property_t *properties = class_copyPropertyList([Person class], &outCount); NSLog(@"%d", outCount);
for (NSInteger i = 0; i < outCount; i++) {
NSString *name = @(property_getName(properties[i])); NSString *attributes = @(property_getAttributes(properties[i])); NSLog(@"%@--------%@", name, attributes);
} 打印结果如下:
test[2321:451525] 3
test[2321:451525] name--------T@"NSString",&,N,V_name
test[2321:451525] age--------Ti,N,V_age
test[2321:451525] weight--------Td,N,V_weight
property_getName  来查找属性的名称，返回 c 字符 。property_getAttributes 函数挖掘属性 的真实名称和 @encode 类型，返回 c 字符 。
objc_property_t class_getProperty(Class cls, const char *name)
objc_property_t protocol_getProperty(Protocol *proto, const char *name, BOOL isRequiredProperty, BOOL isInstanceProperty)
class_getProperty 和 protocol_getProperty 通过给出属性名在类和协议中获得属性的引 。
 275问:为 么需要Runtime
答:Objective-C 是  动态语 ，它会将 些 作放在代码运 时才处  并 编译时。也就是说， 有很多类和成员变 在我们编译的时是 知道的， 在运 时，我们所编写的代码会转换成完整的 确定的代码运 。
因此，编译 是 够的，我们还需要 个运 时系统(Runtime system)来处 编译后的代码。
Runtime 基本是  C 和汇编写的，由此可 苹果为 动态系统的 效 做出的努 。苹果和 GNU 各 维护 个开源的 Runtime 版本，这两个版本之间都在努 保持 致。
276问:Runtime是 么
答:Runtime  叫运 时，是 套底层的 C 语  API，其为 iOS 内部的核 之 ，我们平时编写的 OC 代码，底层都是基于它来实现的。 如:
[receiver message];
// 底层运 时会被编译 转化为: objc_msgSend(receiver, selector)
// 如果其还有参数 如:
[receiver message:(id)arg...];
// 底层运 时会被编译 转化为: objc_msgSend(receiver, selector, arg1, arg2, ...)
277问:两个APP之间如何互调传值
答:两个APP之间的跳转是通过[[UIApplication sharedApplication] openURL:url]这种 式来实现的。
1. 先设置第 个APP的url地址
2.接着设置第 个APP的url地址
3.跳转代码:
NSString *urlString = [NSString stringWithFormat:@"AppJumpSecond://%@",textField.text]; [[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]];  如:将textField的 字也传过去，同样的，在第 个  也是如此
NSString *urlString = [NSString stringWithFormat:@"AppJumpFirst://%@",textField.text]; [[UIApplication sharedApplication] openURL:[NSURL URLWithString:urlString]]; 4.处 传过去的数据， 如传 textField的数据，接收时在AppDelegate的
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication: (NSString *)sourceApplication annotation:(id)annotation 法 。
在AppDelegate 设置属性
@property (nonatomic, strong) RootViewController *rvc; 在didFinishLaunchingWithOptions 法 添加
self.rvc = [[RootViewController alloc] init]; UINavigationController *nc = [[UINavigationController alloc] initWithRootViewController:self.rvc]; self.window.rootViewController = nc;
 添加代码块
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication: (NSString *)sourceApplication annotation:(id)annotation {
self.rvc.textField.text = [[url host] stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding]; return YES;
}
使得textField显示另 个  传过来的数据。
278问:如何 objective-c 实现常 算法(冒泡、选择、快速、插 ) 答:前置条件:
- (void)initArr
{
for (int i = 0; i < arr.count; i++) {
for (int j = 0; j < arr.count - i - 1;j++) {
if ([arr[j+1]integerValue] < [arr[j] integerValue]) { int temp = [arr[j] integerValue];
arr[j] = arr[j + 1];
arr[j + 1] = [NSNumber numberWithInt:temp];
} }
}
NSLog(@"冒泡排序后:%@",arr); }
 .冒泡排序
- (void)sort:(NSMutableArray *)arr {
for (int i = 0; i < arr.count; i++) {
for (int j = 0; j < arr.count - i - 1;j++) {
if ([arr[j+1]integerValue] < [arr[j] integerValue]) { int temp = [arr[j] integerValue];
arr[j] = arr[j + 1];
arr[j + 1] = [NSNumber numberWithInt:temp];
} }
}
NSLog(@"冒泡排序后:%@",arr); }
 .选择排序
- (void)sort:(NSMutableArray *)arr {
for (int i = 0; i < arr.count; i ++) {
for (int j = i + 1; j < arr.count; j ++) {
 if ([arr[i] integerValue] [arr[j] integerValue]) { int temp = [arr[i] integerValue];
arr[i] = arr[j];
arr[j] = [NSNumber numberWithInt:temp];
} }
}
NSLog(@"选择排序后:%@",arr); }
三.快速排序
- (void)quickSort:(NSMutableArray *)arr leftIndex:(int)left rightIndex:(int)right {
if (left < right) {
int temp = [self getMiddleIndex:arr leftIndex:left rightIndex:right]; [self quickSort:arr leftIndex:left rightIndex:temp - 1];
[self quickSort:arr leftIndex:temp + 1 rightIndex:right];
} }
- (int)getMiddleIndex:(NSMutableArray *)arr leftIndex:(int)left rightIndex:(int)right {
int tempValue = [arr[left] integerValue]; while (left < right) {
while (left < right && tempValue <= [arr[right] integerValue]) { right --;
}
if (left < right) {
arr[left] = arr[right]; }
while (left < right && [arr[left] integerValue] <= tempValue) { left ++;
}
if (left < right) {
arr[right] = arr[left]; }
}
arr[left] = [NSNumber numberWithInt:tempValue]; return left;
}
四.插 排序
- (void)sort:(NSMutableArray *)arr
 {
for (int i = 1; i < arr.count; i ++) {
int temp = [arr[i] integerValue];
for (int j = i - 1; j = 0 && temp < [arr[j] integerValue]; j --) { arr[j + 1] = arr[j];
arr[j] = [NSNumber numberWithInt:temp]; }
}
NSLog(@"插 排序后:%@",arr); }
279问:SDWebImage内部实现过程是 么
答:1.   setImageWithURL:placeholderImage:options:会先把 placeholderImage 显示，然后 SDWebImageManager 根据 URL 开始处 图 。
2.进  SDWebImageManager-downloadWithURL:delegate:options:userInfo:，交给 SDImageCache 从缓存查找图 是否已经下载 queryDiskCacheForKey:delegate:userInfo:.
3.先从内存图 缓存查找是否有图 ，如果内存中已经有图 缓存，SDImageCacheDelegate 回调 imageCache:didFindImage:forKey:userInfo: 到 SDWebImageManager。
4.SDWebImageManagerDelegate 回调 webImageManager:didFinishWithImage:到 UIImageView+WebCache 等前端展示图 。
5.如果内存缓存中没有， 成 NSInvocationOperation 添加到队 开始从硬盘查找图 是否已经缓 存。
6.根据 URLKey 在硬盘缓存 录下尝试读取图  件。这 步是在 NSOperation 进 的操作，所 以回主线程进 结果回调 notifyDelegate:。
7.如果上 操作从硬盘读取到 图 ，将图 添加到内存缓存中(如果空闲内存过 ，会先清空内 存缓存)。SDImageCacheDelegate 回调 imageCache:didFindImage:forKey:userInfo:。进 回调 展示图 。
8.如果从硬盘缓存 录读取 到图 ，说明所有缓存都 存在该图 ，需要下载图 ，回调 imageCache:didNotFindImageForKey:userInfo:。
9.共享或 新 成 个下载  SDWebImageDownloader 开始下载图 。
10.图 下载由 NSURLConnection 来做，实现相关 delegate 来判断图 下载中、下载完成和下载 失败。
11.connection:didReceiveData:中   ImageIO 做 按图 下载进度加载效果。 12.connectionDidFinishLoading:数据下载完成后交给 SDWebImageDecoder 做图 解码处 。
13.图 解码处 在 个 NSOperationQueue 完成， 会拖慢主线程 UI。如果有需要对下载的图  进  次处 ，最好也在这 完成，效率会好很多。
14.在主线程 notifyDelegateOnMainThreadWithInfo: 宣告解码完成， imageDecoder:didFinishDecodingImage:userInfo: 回调给 SDWebImageDownloader。
15.imageDownloader:didFinishWithImage: 回调给 SDWebImageManager 告知图 下载完成。 16.通知所有的 downloadDelegates 下载完成，回调给需要的地 展示图 。
17.将图 保存到 SDImageCache 中，内存缓存和硬盘缓存同时保存。写 件到硬盘也在以单独 NSInvocationOperation 完成，避免拖慢主线程。
18.SDImageCache 在初始化的时候会注册 些消息通知，在内存警告或退到后台的时候清 内存图  缓存，应 结束的时候清 过期图 。
19.SDWI 也提供  UIButton+WebCache 和 MKAnnotationView+WebCache，  使 。 20.SDWebImagePrefetcher 可以预先下载图 ，  后续使 。
280问:__block和__weak修饰符的区别是 么 答:1.__block 管是ARC还是MRC模式下都可以使 ，可以修饰对象，还可以修饰基本数据类型。 2.__weak只能在ARC模式下使 ，也只能修饰对象(NSString)， 能修饰基本数据类型(int)。 3.__block对象可以在block中被 新赋值，__weak 可以。
