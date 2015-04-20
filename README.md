# Objective-C-Coding-Guidelines-In-Chinese
Objective-C编码规范，内容来自苹果、谷歌的文档翻译，自己的编码经验和对其它资料的总结。

##概要

Objective-C是一门面向对象的动态编程语言，主要用于编写iOS和Mac应用程序。关于Objective-C的编码规范，苹果和谷歌都已经有很好的总结：

*	[Apple Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
*	[Google Objective-C Style Guide](https://google-styleguide.googlecode.com/svn/trunk/objcguide.xml?showone=Line_Length#Line_Length)

本文主要整合了对上述文档的翻译、作者自己的编程经验和其他的相关资料，为公司总结出一份通用的编码规范，也供中国的iOS开发者参考学习。



##一些基本原则

在详细介绍各种规范前，先总结一下Objective-C编程的基本原则。

###使用ARC

除非想要兼容一些古董级的机器和操作系统，我们没有理由放弃使用ARC。在最新版的Xcode(6.2)中，ARC是自动打开的，所以直接使用就好了。

##代码格式

###使用空格而不是制表符Tab

不要在工程里使用Tab键，使用空格来进行缩进。在`Xcode > Preferences > Text Editing`将Tab和自动缩进都设置为**4**个空格。（_Google的标准是使用两个空格来缩进，但这里还是推荐使用Xcode默认的设置。_）

###每一行的最大长度

同样的，在`Xcode > Preferences > Text Editing > Page guide at column:`中将最大行长设置为**80**，过长的一行代码将会导致可读性问题。

###函数的书写

一个典型的Objective-C函数应该是这样的：

```objective-c
- (void)writeVideoFrameWithData:(NSData *)frameData timeStamp:(int)timeStamp {
    ...
}
```

在`-`和`(void)`之间应该有一个空格，第一个大括号`{`的位置在函数所在行的末尾，同样应该有一个空格。（_我司的C语言规范要求是第一个大括号单独占一行，但考虑到OC较长的函数名和苹果SDK代码的风格，还是将大括号放在行末。_）

如果一个函数有特别多的参数或者名称很长，应该将其按照`:`来对齐分行显示：

```objective-c
-(id)initWithModel:(IPCModle)model
       ConnectType:(IPCConnectType)connectType
        Resolution:(IPCResolution)resolution
          AuthName:(NSString *)authName
          Password:(NSString *)password
               MAC:(NSString *)mac
              AzIp:(NSString *)az_ip
             AzDns:(NSString *)az_dns
             Token:(NSString *)token
             Email:(NSString *)email
          Delegate:(id<IPCConnectHandlerDelegate>)delegate;
```

在分行时，如果第一段名称过短，后续名称可以以Tab的长度（4个空格）为单位进行缩进：

```objective-c
- (void)short:(GTMFoo *)theFoo
        longKeyword:(NSRect)theRect
  evenLongerKeyword:(float)theInterval
              error:(NSError **)theError {
    ...
}
```
###函数调用

函数调用的格式和书写差不多，可以按照函数的长短来选择写在一行或者分成多行：

```objective-c
//写在一行
[myObject doFooWith:arg1 name:arg2 error:arg3];

//分行写，按照':'对齐
[myObject doFooWith:arg1
               name:arg2
              error:arg3];

//第一段名称过短的话后续可以进行缩进
[myObj short:arg1
          longKeyword:arg2
    evenLongerKeyword:arg3
                error:arg4];
```

以下写法是错误的：

```objective-c
//错误，要么写在一行，要么全部分行
[myObject doFooWith:arg1 name:arg2
              error:arg3];
[myObject doFooWith:arg1
               name:arg2 error:arg3];

//错误，按照':'来对齐，而不是关键字
[myObject doFooWith:arg1
          name:arg2
          error:arg3];
```

###@public和@private标记符

@public和@private标记符应该以**一个空格**来进行缩进：

```objective-c
@interface MyClass : NSObject {
 @public
  ...
 @private
  ...
}
@end
```

###协议（Protocols）

在书写协议的时候注意用`<>`括起来的协议和类型名之间是没有空格的，比如`IPCConnectHandler()<IPCPreconnectorDelegate>`,这个规则适用所有书写协议的地方，包括函数声明、类声明、实例变量等等：

```objective-c
@interface MyProtocoledClass : NSObject<NSWindowDelegate> {
 @private
    id<MyFancyDelegate> _delegate;
}

- (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
@end
```

###闭包（Blocks）

根据block的长度，有不同的书写规则：

- 较短的block可以写在一行内。
- 如果分行显示的话，block的右括号`}`应该和调用block那行代码的第一个非空字符对齐。
- block内的代码采用**4个空格**的缩进。
- 如果block过于庞大，应该单独声明成一个变量来使用。
- `^`和`(`之间，`^`和`{`之间都没有空格，参数列表的右括号`)`和`{`之间有一个空格。

```objective-c
//较短的block写在一行内
[operation setCompletionBlock:^{ [self onOperationDone]; }];

//分行书写的block，内部使用4空格缩进
[operation setCompletionBlock:^{
    [self.delegate newDataAvailable];
}];

//使用C语言API调用的block遵循同样的书写规则
dispatch_async(_fileIOQueue, ^{
    NSString* path = [self sessionFilePath];
    if (path) {
      // ...
    }
});

//较长的block关键字可以缩进后在新行书写，注意block的右括号'}'和调用block那行代码的第一个非空字符对齐
[[SessionService sharedService]
    loadWindowWithCompletionBlock:^(SessionWindow *window) {
        if (window) {
          [self windowDidLoad:window];
        } else {
          [self errorLoadingWindow];
        }
    }];

//较长的block参数列表同样可以缩进后在新行书写
[[SessionService sharedService]
    loadWindowWithCompletionBlock:
        ^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];

//庞大的block应该单独定义成变量使用
void (^largeBlock)(void) = ^{
    // ...
};
[_operationQueue addOperationWithBlock:largeBlock];

//在一个调用中使用多个block，注意到他们不是像函数那样通过':'对齐的，而是同时进行了4个空格的缩进
[myObject doSomethingWith:arg1
    firstBlock:^(Foo *a) {
        // ...
    }
    secondBlock:^(Bar *b) {
        // ...
    }];
```

###数据结构的语法糖

鼓励使用可读性更好的语法糖来构造`NSArray`，`NSDictionary`等数据结构。

如果构造代码写在一行，需要在括号两端留有一个空格，使得被构造的元素于与构造语法区分开来：

```objective-c
//正确，在语法糖的"[]"或者"{}"两端留有空格
NSArray *array = @[ [foo description], @"Another String", [bar description] ];
NSDictionary *dict = @{ NSForegroundColorAttributeName : [NSColor redColor] };

//不正确，不留有空格降低了可读性
NSArray* array = @[[foo description], [bar description]];
NSDictionary* dict = @{NSForegroundColorAttributeName: [NSColor redColor]};
```

如果构造代码不写在一行内，构造元素需要使用**两个空格**来进行缩进，右括号`]`或者`}`写在新的一行，并且与调用语法糖那行代码的第一个非空字符对齐：

```objective-c
NSArray *array = @[
  @"This",
  @"is",
  @"an",
  @"array"
];

NSDictionary *dictionary = @{
  NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
  NSForegroundColorAttributeName : fontColor
};
```

构造字典时，字典的Key和Value与中间的冒号`:`都要留有一个空格，多行书写时，也可以将Value对齐：

```objective-c
//正确，冒号':'前后留有一个空格
NSDictionary *option1 = @{
  NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
  NSForegroundColorAttributeName : fontColor
};

//正确，按照Value来对齐
NSDictionary *option2 = @{
  NSFontAttributeName :            [NSFont fontWithName:@"Arial" size:12],
  NSForegroundColorAttributeName : fontColor
};

//错误，冒号前应该有一个空格
NSDictionary *wrong = @{
  AKey:       @"b",
  BLongerKey: @"c",
};

//错误，每一个元素要么单独成为一行，要么全部写在一行内
NSDictionary *alsoWrong= @{ AKey : @"a",
                            BLongerKey : @"b" };

//错误，在冒号前只能有一个空格，冒号后才可以考虑按照Value对齐
NSDictionary *stillWrong = @{
  AKey       : @"b",
  BLongerKey : @"c",
};
```

##命名规范

###基本原则

####清晰

命名应该尽可能的清晰和简洁，但在Objective-C中，清晰比简洁更重要。由于Xcode强大的自动补全功能，我们不必担心名称过长的问题。

```objective-c
//清晰
insertObject:atIndex:

//不清晰，insert的对象类型和at的位置属性没有说明
insert:at:

//清晰
removeObjectAtIndex:

//不清晰，remove的对象类型没有说明，参数的作用没有说明
remove:
```

不要使用单词的简写，拼写出完整的单词：
```objective-c
//清晰
destinationSelection
setBackgroundColor:

//不清晰，不要使用简写
destSel
setBkgdColor:
```
然而，有部分单词简写在Objective-C编码过程中是非常常用的，以至于成为了一种规范，这些简写可以在代码中直接使用，下面列举了部分：

```objective-c
alloc   == Allocate					max    == Maximum
alt     == Alternate				min    == Minimum
app     == Application				msg    == Message
calc    == Calculate				nib    == Interface Builder archive
dealloc == Deallocate				pboard == Pasteboard
func    == Function					rect   == Rectangle
horiz   == Horizontal				Rep    == Representation (used in class name such as NSBitmapImageRep).
info    == Information				temp   == Temporary
init    == Initialize				vert   == Vertical
int     == Integer
```

命名方法或者函数时要避免歧义

```objective-c
//有歧义，是返回sendPort还是send一个Port？
sendPort

//有歧义，是返回一个名字属性的值还是display一个name的动作？
displayName
```

####一致性

整个工程的命名风格要保持一致性，最好和苹果SDK的代码保持统一。不同类中完成相似功能的方法应该叫一样的名字，比如我们总是用`count`来返回集合的个数，不能在A类中使用`count`而在B类中使用`getNumber`。

###使用前缀

如果代码需要打包成Framework给别的工程使用，或者工程项目非常庞大，需要拆分成不同的模块，使用命名前缀是非常有用的。

- 前缀由大写的字母缩写组成，比如Cocoa中`NS`前缀代表Founation框架中的类，`IB`则代表Interface Builder框架。

- 可以在为类、协议、函数、常量以及typedef宏命名的时候使用前缀，但注意**不要**为成员变量或者方法使用前缀，因为他们本身就包含在类的命名空间内。

- 命名前缀的时候不要和苹果SDK框架冲突。

###命名类和协议（Class&Protocol）

类名以大写字母开头，应该包含一个*名词*来表示它代表的对象类型，同时可以加上必要的前缀，比如`NSString`, `NSDate`, `NSScanner`, `NSApplication`等等。

而协议名称应该清晰地表示它所执行的行为，而且要和类名区别开来，所以通常使用`ing`词尾来命名一个协议，比如`NSCopying`,`NSLocking`。

有些协议本身包含了很多不相关的功能，主要用来为某一特定类服务，这时候可以直接用类名来命名这个协议，比如`NSObject`协议，它包含了id对象在生存周期内的一系列方法。

###命名头文件（Headers）

源码的头文件名应该清晰地暗示它的功能和包含的内容：

- 如果头文件内只定义了单个类或者协议，直接用类名或者协议名来命名头文件，比如`NSLocale.h`定义了`NSLocale`类。

- 如果头文件内定义了一系列的类、协议、类别，使用其中最主要的类名来命名头文件，比如`NSString.h`定义了`NSString`和`NSMutableString`。

- 每一个Framework都应该有一个和框架同名的头文件，包含了框架中所有公共类头文件的引用，比如`Foundation.h`

- Framework中有时候会实现在别的框架中类的类别扩展，这样的文件通常使用`被扩展的框架名`+`Additions`的方式来命名，比如`NSBundleAdditions.h`。

###命名方法（Methods）

Objective-C的方法名通常都比较长，这是为了让程序有更好地可读性，按苹果的说法*“好的方法名应当可以以一个句子的形式朗读出来”*。

方法一般以小写字母打头，每一个后续的单词首字母大写，方法名中不应该有标点符号（*包括下划线*），有两个例外：

- 可以用一些通用的大写字母缩写打头方法，比如`PDF`,`TIFF`等。
- 可以用带下划线的前缀来命名私有方法或者类别中的方法。

如果方法表示让对象执行一个动作，使用动词打头来命名，注意不要使用`do`，`does`这种多余的关键字，动词本身的暗示就足够了：

```objective-c
//动词打头的方法表示让对象执行一个动作
- (void)invokeWithTarget:(id)target;
- (void)selectTabViewItem:(NSTabViewItem *)tabViewItem;
```

如果方法是为了获取对象的一个属性值，直接用属性名称来命名这个方法，注意不要添加`get`或者其他的动词前缀：

```objective-c
//正确，使用属性名来命名方法
- (NSSize)cellSize;

//错误，添加了多余的动词前缀
- (NSSize)calcCellSize;
- (NSSize)getCellSize;
```

对于有多个参数的方法，务必在每一个参数前都添加关键词，关键词应当清晰说明参数的作用：

```objective-c
//正确，保证每个参数都有关键词修饰
- (void)sendAction:(SEL)aSelector toObject:(id)anObject forAllCells:(BOOL)flag;

//错误，遗漏关键词
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;

//正确
- (id)viewWithTag:(NSInteger)aTag;

//错误，关键词的作用不清晰
- (id)taggedView:(int)aTag;
```

不要用`and`来连接两个参数，通常`and`用来表示方法执行了两个相对独立的操作（*从设计上来说，这时候应该拆分成两个独立的方法*）：

```objective-c
//错误，不要使用"and"来连接参数
- (int)runModalForDirectory:(NSString *)path andFile:(NSString *)name andTypes:(NSArray *)fileTypes;

//正确，使用"and"来表示两个相对独立的操作
- (BOOL)openFile:(NSString *)fullPath withApplication:(NSString *)appName andDeactivate:(BOOL)flag;
```
方法的参数命名也有一些需要注意的地方:
- 和方法名类似，参数的第一个字母小写，后面的每一个单词首字母大写
- 不要再方法名中使用类似`pointer`,`ptr`这样的字眼去表示指针，参数本身的类型足以说明
- 不要使用只有一两个字母的参数名
- 不要使用简写，拼出完整的单词

下面列举了一些常用参数名：

```objective-c
...action:(SEL)aSelector
...alignment:(int)mode
...atIndex:(int)index
...content:(NSRect)aRect
...doubleValue:(double)aDouble
...floatValue:(float)aFloat
...font:(NSFont *)fontObj
...frame:(NSRect)frameRect
...intValue:(int)anInt
...keyEquivalent:(NSString *)charCode
...length:(int)numBytes
...point:(NSPoint)aPoint
...stringValue:(NSString *)aString
...tag:(int)anInt
...target:(id)anObject
...title:(NSString *)aString
```

###存取方法（Accessor Methods）

存取方法是指用来获取和设置类属性值的方法，属性的不同类型，对应着不同的存取方法规范：

```objective-c
//属性是一个名词时的存取方法范式
- (type)noun;
- (void)setNoun:(type)aNoun;
//栗子
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;

//属性是一个形容词时存取方法的范式
- (NSString *)title;
- (void)setTitle:(NSString *)aTitle;
//栗子
- (BOOL)isAdjective;
- (void)setAdjective:(BOOL)flag;

//属性是一个动词时存取方法的范式
- (BOOL)verbObject;
- (void)setVerbObject:(BOOL)flag;
//栗子
- (BOOL)showsAlpha;
- (void)setShowsAlpha:(BOOL)flag;
```

命名存取方法时不要将动词转化为被动形式来使用：
```objective-c
//正确
- (void)setAcceptsGlyphInfo:(BOOL)flag;
- (BOOL)acceptsGlyphInfo;

//错误，不要使用动词的被动形式
- (void)setGlyphInfoAccepted:(BOOL)flag;
- (BOOL)glyphInfoAccepted;
```

可以使用`can`,`should`,`will`等词来协助表达存取方法的意思，但不要使用`do`,和`does`：
```objective-c
//正确
- (void)setCanHide:(BOOL)flag;
- (BOOL)canHide;
- (void)setShouldCloseDocument:(BOOL)flag;
- (BOOL)shouldCloseDocument;

//错误，不要使用"do"或者"does"
- (void)setDoesAcceptGlyphInfo:(BOOL)flag;
- (BOOL)doesAcceptGlyphInfo;
```

为什么Objective-C中不适用`get`前缀来表示属性获取方法？因为`get`在Objective-C中通常只用来表示从函数指针返回值的函数：
```objective-c
//三个参数都是作为函数的返回值来使用的，这样的函数名可以使用"get"前缀
- (void)getLineDash:(float *)pattern count:(int *)count phase:(float *)phase;
```

###命名委托（Delegate）

当特定的事件发生时，对象会触发它注册的委托方法。委托是Objective-C中常用的传递消息的方式。委托有它固定的命名范式。

一个委托方法的第一个参数是触发它的对象，第一个关键词是触发对象的类名，除非委托方法只有一个名为`sender`的参数：
```objective-c
//第一个关键词为触发委托的类名
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row;
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;

//当只有一个"sender"参数时可以省略类名
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
```

根据委托方法触发的时机和目的，使用`should`,`will`,`did`等关键词
```objective-c
- (void)browserDidScroll:(NSBrowser *)sender;

- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window;、

- (BOOL)windowShouldClose:(id)sender;
```

###集合操作类方法（Collection Methods）

有些对象管理着一系列其它对象或者元素的集合，需要使用类似“增删查改”的方法来对集合进行操作，这些方法的命名范式一般为：

```objective-c
//集合操作范式
- (void)addElement:(elementType)anObj;
- (void)removeElement:(elementType)anObj;
- (NSArray *)elements;

//栗子
- (void)addLayoutManager:(NSLayoutManager *)obj;
- (void)removeLayoutManager:(NSLayoutManager *)obj;
- (NSArray *)layoutManagers;
```

注意，如果返回的集合是无序的，使用`NSSet`来代替`NSArray`。如果需要将元素插入到特定的位置，使用类似于这样的命名：

```objective-c
- (void)insertLayoutManager:(NSLayoutManager *)obj atIndex:(int)index;
- (void)removeLayoutManagerAtIndex:(int)index;
```

如果管理的集合元素中有指向管理对象的指针，要设置成`weak`类型以防止引用循环。

下面是SDK中`NSWindow`类的集合操作方法：

```objective-c
- (void)addChildWindow:(NSWindow *)childWin ordered:(NSWindowOrderingMode)place;
- (void)removeChildWindow:(NSWindow *)childWin;
- (NSArray *)childWindows;
- (NSWindow *)parentWindow;
- (void)setParentWindow:(NSWindow *)window;
```
