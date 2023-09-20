---
layout: post
title: Windows10(UWP)下的MEF
date: 2016-01-09 21:15:58
tags: [UWP]
categories: [CSharp]
---
# 前言
最近在帮一家知名外企开发Universal Windows Platform的相关应用，开发过程中不由感慨：项目分为两种，一种叫做前人栽树后人乘凉，一种叫做前人挖坑后人遭殃。不多说了，多说又要变成月经贴了。

讲讲MEF。

MEF全称Managed Extensibility Framework。我们做.Net的碰到依赖注入(DI:Dependency Injection)这一块的内容，一般会选择使用Unity或者MEF，这也是Prism主要使用的两种方式。在.Net 4.0之前，MEF一直作为扩展的形式存在，但是.Net 4.0的时候，已经作为Framework的一部分了。但是.Net 4.0的MEF只是原始的版本，后面MEF 2又加入了泛型类导入导出等等特性。MEF 2不作为.Net的一部分，又变成了以扩展包的形式存在，支持了包括.Net 4.5以及之后的平台，我们可以通过Nuget获取这个扩展，源码也被托管在了codeplex平台。

>* [Microsoft Composition (MEF 2)](https://www.nuget.org/packages/microsoft.composition)
>* [Source Code](http://mef.codeplex.com/)

MEF2支持的平台

>* NET Framework 4.5
>* Windows 8
>* Windows Phone 8.1
>* Windows Phone Silverlight 8
>* Portable Class Libraries

通常意义上，当我们讲到MEF的时候，一般都会去描述这是一个用来实行插件式开发的一套东西。当插件式开发成为了一种可能，那就意味着我们的项目可以被完整的解耦，这就保证了我们程序的健壮性，同时在开发的过程中我们也避免了各种开发人员的冲突。

# 开始
怎样开始写一个基于MEF的程序？

假设我们现在写的是一个UWP的项目，并且我们采用C#+XAML的方式。因为MVVM是XAML的主打的方式，可以很好的应用绑定数据的这个模型，所以我们采用MVVM。

所以我们决定设计一个基于C#+XAML的通过MVVM模式来进行View和ViewModel的解耦，中间我们也可以实现一个观察者模式的消息传递方式来进行View之间的相互传参等等。看是确实很完美，可以很轻易的下载一个Mvvmlight来直接用。

我们看样子已经决定了View层和ViewModel层的问题，那么对于一个完整的系统，我们还缺少一些什么组件呢？我们可以还缺少数据，所以我们需要数据层，一般我们命名为Service层，来进行跟数据库或者服务器的通信；还缺什么呢？日志系统，我们需要进行运行过程中的一些数据统计，或者异常捕获后的消息记录，所以我们需要Log层；还需要缓存层，缓存我们的数据到内存或者磁盘，这样看上去我们的程序能够运行的稍微好一点。

当我们决定了以后，我们现在需要写的东西如下：

>* View
>* ViewModel
>* Service
>* Log
>* Cache

想想我们怎么处理这个问题呢？
```CSharp
public sealed class Hub
{
        
    public static Log Log { get; private set; }
    public static Service Service { get; private set; }
    public static Cache Cache { get; private set; }

    private static Hub instance = null;
    private static readonly object padlock = new object();

    Hub()
    {
        Log = new Log();
        Service = new Service();
        Cache = new Cache();
    }

    public static Hub Instance
    {
        get
        {
            if (instance == null)
            {
                lock (padlock)
                {
                    if (instance == null)
                    {
                        instance = new Hub();
                    }
                }
            }
            return instance;
        }
    }
}
```
上面的解决方案，引入一个单例的Hub类，然后各层作为只读静态属性来提供各类功能，看上去不错，我们也能很好的调用。

但是有个问题，当这个类出现的时候，我们不希望Service等等类再被外部实例化。很不幸，当Service等作为一个可以Public的属性时，这个类本身为了访问的一致性就也要不可以避免的被标记为Public，这破坏了我们设立这个类的初衷。

那我们怎么继续解决这个问题？把Service等类都设计为单例。这也是一个解决方案。但无论是这种解决方案还是代码里的解决方案，都强引用的意味都太强了，稍加不慎，系统就会崩溃。

我们不希望引入Hub，也不想Service等类被设计为单例，同时具体的ViewModel中也不希望出现具体的Service的实例，那我们应该怎么办？

**答案：依赖注入。**

# 重新设计
保留我们之前所设想的所有的组件，引入接口来进入注入：

>* IService
>* ILog
>* ICache

看一下我们的ViewModel现在应该是怎么样的?
```CSharp
public class ViewModel
{
    ILog Log;
    IService Service;
    ICache Cache;

    public ViewModel(ILog log, IService service, ICache cache)
    {
            Log = log;
            Service = service;
            Cache = cache;
    }
}
```
又进了一步，我们只需要调用的时候给我们需要的实例就行了。如果我们需要View，我们还能声明一个IView的接口。

至此，我们设计还没有引入MEF，看上去已经相对比较好的解耦了，我们只有在调用ViewModel的时候，引入具体的是实例，耦合发生在了此处。

# 引入MEF
试想一下，既然我们需要生成的实例的对象都已经在我们的DLL之中，为什么我们还要手动的去生成一个实例，然后再传到具体的构造函数里面，它就不能自己寻找吗?

假设我们的类都有一个别名，然后我们在需要引用的地方告诉告诉程序，我们需要一个实现Ixxx接口的类，它的名字叫做xxx，这样我们是不是更进了一部。如下：
```CSharp
public class ViewModel
{
    [Import("LogSample")]
    ILog Log;
    [Import("ServiceSample")]
    IService Service;
    [Import("CacheSample")]
    ICache Cache;

    public ViewModel()
    {
    }
}
```
当我们构造函数完成后，Log等对象就已经自动在程序集中找到名为LogSample的的实现ILog的类，Service也是，Cache也是。

看下Log
```CSharp
[Export("LogSample", typeof(ILog))]
public class Log : ILog
{
}
```
到现在为止，我们主要关注具体的功能实现就好了。

# MEF正式引入
为了简化我们的程序，更加关注MEF的本质，我们把程序设计为仅包括下列的组件

>* View
>* ViewModel
>* Service

建立我们的项目如下

![Image](/images/2016-01-09-UWPMEF-01.png)

代码已经完整的托管到[GitHub](https://github.com/LibertyOrganization/CommonRepo/tree/master/01.Projects/02.UWP%20Untils/uwp_MEF)上，可以方便的查阅。

我们在Service中写了一个演示的功能：
```CSharp
[Export(Constant.Confing.SampleService,typeof(IService))]
public class SampleService : IService
{
    public void QueryData(int numuber, Action<int> action)
    {
        action(numuber);
    }
}
```
我们看一下我们的程序的主界面：
```CSharp
public sealed partial class MainPage : Page
{
    [Import(Constant.Confing.View1)]
    public IView View1 { get; set; }

    [Import(Constant.Confing.View2)]
    public IView View2 { get; set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

    private CompositionHost host;

    private void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        List<Assembly> assemblies = new List<Assembly>()
        {
            Assembly.Load(new AssemblyName("MEF.Service")),
            Assembly.Load(new AssemblyName("MEF.View")),
            Assembly.Load(new AssemblyName("MEF.ViewModel")),
            Assembly.Load(new AssemblyName("MEF.Abstract"))
            };
        ContainerConfiguration configuration = new ContainerConfiguration().WithAssemblies(assemblies);
        host = configuration.CreateContainer();
        host.SatisfyImports(this);
    }

    private void View1_Click(object sender, RoutedEventArgs e)
    {
        IView view = host.GetExport(typeof(IView), Constant.Confing.View1) as IView;
        frame.Content = view;
    }

    private void View2_Click(object sender, RoutedEventArgs e)
    {
        IView view = host.GetExport(typeof(IView), Constant.Confing.View2) as IView;
        frame.Content = view;
    }
}
```
将所有的程序集加入容器之中，然后通过容器去创建对象。

View的代码：
```CSharp
[Export(Constant.Confing.View1,typeof(IView))]
public sealed partial class View1 : UserControl, IView
{
    IService Service;
    IViewModel ViewModel;
    [ImportingConstructor]
    public View1(
        [Import(Constant.Confing.SampleService)]IService service, 
        [Import(Constant.Confing.ViewModel1)]IViewModel viewModel)
    {
        this.InitializeComponent();
        this.Service = service;
        this.ViewModel = viewModel;
    }

    private void Button_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
    {
        Service.QueryData(ViewModel.Number, ShowValue);
    }

    private void ShowValue(int i)
    {
        Btn.Content = i;
    }
}
```
# 演示
初始的状态：

![Image](/images/2016-01-09-UWPMEF-02.png)

当我们点击Show View 1按钮时，容器去创建View1的实例，View1所需要的实例，又会根据导入导出的原则去创建。创建完成后，

![Image](/images/2016-01-09-UWPMEF-03.png)

点击View 1 Click后，会将ViewModel层的数据传给Service，Service又调用回掉函数，将数据放置到UI上。

![Image](/images/2016-01-09-UWPMEF-04.png)

也可以点击Show View 2进行相应的操作。

![Image](/images/2016-01-09-UWPMEF-05.png)

![Image](/images/2016-01-09-UWPMEF-06.png)

# 总结
本文讲述了一个简单的MEF在UWP下的引用，体现了MEF通过依赖注入的方式将程序更好的解耦。阅读本文希望对你有所帮助。

谢谢~

代码下载：http://files.cnblogs.com/files/youngytj/uwp_MEF.zip

# 参考资料
[Unity](http://unity.codeplex.com/)  
[《MEF程序设计指南》博文汇总](http://www.cnblogs.com/beniao/archive/2010/08/11/1797537.html)  
[Prism](https://compositewpf.codeplex.com/)  
[Prism与MVVM、Unity、MEF关系](http://www.cnblogs.com/Joetao/articles/2074035.html)  
[依赖注入那些事儿](http://www.cnblogs.com/leoo2sk/archive/2009/06/17/di-and-ioc.html)  
[System.Composition](https://msdn.microsoft.com/zh-CN/library/windows/apps/mt185504.aspx?f=255&MSPPError=-2147217396)  