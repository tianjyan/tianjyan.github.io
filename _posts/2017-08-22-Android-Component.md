---
title: 由客户端内部通讯引发的插件化开发的随想和实践
date: 2017-08-22 20:15:58
tags: [Android]
categories: [Java]
---
# 背景
最近在写一个基于Android的IPC实现的一个小工具，主要实现的就是能够在手机查看被监视程序的值的变化和日志等。因为用了入侵的方式，所以需要被监视APK集成一个SDK。程序界面一览:  

<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-01.png" width="240">
<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-02.png" width="240">
<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-03.png" width="240">
<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-04.png" width="240">  
<!--
工程结构以及SDK简单示例:  
![06](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-06.png)
![05](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-05.png)
!-->
大概还是一个半成品的样子，后续会写一些Root以后才有的功能。

# 遇到的问题
在实际的开发中，因为主程序中会包含各个集成SDK的Client端的数据，所以主程序的数据接受到最后UI的呈现，就面临了一个传输的选择。在客户端的开发中，我们解决内部的通信问题一般有三种方式：
* 基于事件总线（EventBus, PubSubEvent等）;
* 协议化（內建Server，接收方和发送方约定好数据格式）;
* 接口。  

第一种方式极大的提高了我们的开发效率，但是后续带来整体代码的恶化简直是我们维护代码调试代码的灾难。第二种方式內建Server的话，确实很好的解决了我们耦合的问题，但是也带来了一些问题，其一是性能，数据模型的转换在频繁的通信中会带来性能的损耗，其二是开发效率，因为是协议传输，所以一方有变更，另一方也要做相应的变更。那第三种方式接口，因为本身是强引用，所以易于调试和维护，其次也没有数据模型的转换。所以我倾向于用接口去解决数据传输的问题。

# 过往的经验
在过往开发桌面端的经验中，我们大量运用依赖注入的方式来解决模块与模块的耦合问题，同时也可以用来解决模块与仓储之间传输数据的问题。这也是传统的桌面端开发中，插件式开发的经典实现。用之前写过的一个程序举个例子: 

![07](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-07.png)

整个工程的结构是这样的：`MailAccount`作为主程序，`MailAccount.Interface`是`MailAccount`唯一的引用项，`MailAccount.Extra.Trial`和`MailAccount.Extra.Standard`是完全独立的dll的项目。

整个程序实现的效果是这样的，当MailAccount的exe文件运行时，如果目录下没有任何其他的dll，则不运行任何内容，只是一个空白页面，但是当目录下有Trial或者Standard任意一个dll时，则运行相应dll中的内容。

首先看下Trail和Standard的实现: 

```CSharp
namespace MailAccount.Extra.Trial
{
    [Export("Trial", typeof(IUserAction))]
    public class UserTrial : IUserAction
    {
        public bool DoWork<T>(IEnumerable<T> source)
        {
            if(source.Count() > 5)
            {
                return false;
            }
            return true;
        }
    }
}
```

```CSharp
namespace MailAccount.Extra.Standard
{
    [Export("Standard", typeof(IUserAction))]
    public class UserStandard : IUserAction
    {
        public bool DoWork<T>(IEnumerable<T> source)
        {
            return true;
        }
    }
}
```

Trail和Standard都实现了IUserAction的接口，并对其中功能做了自己的具体实现。

再看下MailAccount中启动的时候做了什么：

```CSharp
public class Bootstrapper
    {
        private const string SEARCH_PATTERN = "MailAccount.Extra.*.dll";

        protected CompositionContainer MainContainer { get; private set; }

        public void Run()
        {
            Container container = this.CreateContainer();

            InfrastructureCatalog baseCatalog = this.CreateBaseCatalog();

            VarifyAndLoadBaseCatalog(baseCatalog, container);

            container.Configure();

            this.MainContainer = container.MainContainer;

            CreateMainWindow();
        }

        private Container CreateContainer()
        {
            return new Container();
        }

        private InfrastructureCatalog CreateBaseCatalog()
        {
            InfrastructureCatalog baseCatalog = new InfrastructureCatalog();
            baseCatalog.Add(this.GetType().Assembly);
            DirectoryInfo dirInfo = new DirectoryInfo(@".\");
            foreach (FileInfo fileInfo in dirInfo.EnumerateFiles(SEARCH_PATTERN))
            {
                try
                {
                    baseCatalog.Add(fileInfo.FullName);
                }
                catch(Exception ex)
                {
                    LogHelper.Error(ex.Message);
                }
            }


            return baseCatalog;
        }

        private void VarifyAndLoadBaseCatalog(InfrastructureCatalog baseCatalog, Container container)
        {
            if (baseCatalog != null && baseCatalog.Items != null)
            {
                foreach (AssemblyCatalog catalog in baseCatalog.Items.Distinct())
                {
                    if (container.AggregateCatalog.Catalogs.FirstOrDefault(c => c.ToString() == catalog.ToString()) == null)
                    {
                        container.AggregateCatalog.Catalogs.Add(catalog);
                    }
                }
            }
        }

        public void CreateMainWindow()
        {
            MainWindow mainWindow = MainContainer.GetExportedValue<MainWindow>("MainWindow");

            IUserAction trial = null;
            IUserAction standard = null;

            try
            {
                trial = MainContainer.GetExportedValue<IUserAction>("Trial");
            }
            catch(Exception ex)
            {
                LogHelper.Error(ex.Message);
            }

            try
            {
                standard = MainContainer.GetExportedValue<IUserAction>("Standard");
            }
            catch (Exception ex)
            {
                LogHelper.Error(ex.Message);
            }

            if (standard != null)
            {
                mainWindow.userAction = standard;
            }
            else if(trial != null)
            {
                mainWindow.userAction = trial;
            }
            else
            {
                MessageBox.Show("程序验证失败");
                Environment.Exit(0);
            }

            Application.Current.MainWindow = mainWindow;
            Application.Current.MainWindow.Show();
        }
    }
}
```
在MailAccount启动时，我们初始化一个容器，然后遍历当前目录下的与`MailAccount.Extra.*.dll`能匹配的dll，在放入到容器中。因为我们在dll中显式的`Export`并声明了key为`Standard`，所以容器能够在`MainContainer.GetExportedValue<IUserAction>("Standard")`的时候找到这个类并初始化。当然我们也可以生成新的符合要求的dll，实现热拔插，当然这是后话。

# Android中实践的前期准备
因为过往的经验，所以我在检索Android这边信息的时候，是尝试用插件化或者组件化这种字眼来搜索的。但是我却发现了一个有趣的现象，在Android这个圈子里，组件化或者插件化，大家都默认这个技术是用来实现热更新的，并且当我看了各个开源repo的开始文档后，发现总有各种限制或者缺陷，比如在特定手机上无法进行资源转换，不支持Activity(process、configChanges)的部分属性等等。总之真正的工程实践中会面临很多缺陷。  

![08](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-08.png)

所以我放弃了这些开源repo，继续走依赖注入框架的方式。在Android的开发中，我们常用的依赖注入的框架其实有两个[RoboGuice](https://github.com/roboguice/roboguice)和[Dagger](https://github.com/square/Dagger)。不过真正意义上讲，虽然[Dagger2](https://github.com/google/dagger)也叫Dagger，但是开发商变了（square->google），实现方式也变了（运行时反射->编译时生成），所以可以理解为我们其实有三个依赖注入的框架可选。在框架的选择上，我最终还是选择了Dagger2，原因有两个，第一个是大厂质量能保证，第二个是我不存在热拔插的需求，运行时编译生成能提高程序的运行效率。

# 实践

<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-09.png">

选择完依赖注入的框架后，我定义了整个程序的层次结构。每一个框都作为一个独立的Module存在，方便单独的Module的管理。那么我在Plugin这块是怎么实践的呢？假设我们要新增一个plugin，我需要做些什么？正如开始所说的，因为通信方式中，我选择了使用接口，所以我首先要定义一个接口。以出参监视功能为例，我需要定义一个`IOutParaPlugin`接口：

```Java
public interface IOutParaPlugin extends IPlugin {
    boolean isGathering();
    void setIsGathering(boolean isGathering);
    void registerOutPara(OutPara outPara);
    void setOutPara(OutPara outPara, String value);
    void clientDisconnect(String pkgName);
}
```

`IPlugin`是我们所有插件的接口类，其主要功能是提供功能的名称和功能入口UI：

```Java
public interface IPlugin {
    String getPluginName();
    Fragment getPluginFragment();
    ILBApp getApp();
}
```

这个定义的接口放置在我们的`Abs`的Module中，我们可以新建一个`plugin.op`的module来实现我们的功能。

<img src="https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2017-08-22-Android-Component-10.png">

除了实现了基本的Plugin的功能外，我们还要声明一个module的类来供Dagger生成编译时的信息。

```Java
@Module
public abstract class OutParaModule {
    @Provides
    @Named(AliasName.OUT_PARA_PLUGIN)
    @Singleton
    public static IOutParaPlugin provideOutParaPlugin(ILBApp app,
                                                      @Named(AliasName.OUT_PARA_BRIDGE) Lazy<UIOutParaBridge> outParaBridgeLazy) {
        return new OutParaPlugin(app, outParaBridgeLazy);
    }

    @Provides
    @Named(AliasName.OUT_PARA_BRIDGE)
    @Singleton
    public static UIOutParaBridge provideOutParaBridge(ILBApp app,
                                                       @Named(AliasName.CLIENT_MANAGER) IClientManager clientManager,
                                                       @Named(AliasName.OUT_PARA_PLUGIN) Lazy<IOutParaPlugin> outParaPluginLazy) {
        return new UIOutParaBridge(app, clientManager, outParaPluginLazy);
    }

    @PreActivity
    @ContributesAndroidInjector
    abstract OutParaDetailActivity outParaDetailActivityInjector();

    @PreFragment
    @ContributesAndroidInjector
    abstract OutParaFragment outParaFragmentInjector();
}
```
在Module中，我们定义了我们的`OutParaPlugin`在外部可以被注入，当然我们还给了它一个别名，当有多处注入不同实现`IOutParaPlugin`类时，我们可以用别名来区分。

定义完Module以后，我们要在主app中引用：

```Java
@Singleton
@Component(modules = {
        LBAppModule.class,
        ClientModule.class,
        OutParaModule.class,
        InParaModule.class,
        LogModule.class,
        FloatingModule.class
})
interface LBComponent extends AndroidInjector<LBApp> {
    @Component.Builder
    abstract class Builder extends AndroidInjector.Builder<LBApp> {

    }
}
```

这样，在主app的`MainActiviy`中我们可以这样引用：

```Java
@Inject @Named(AliasName.OUT_PARA_PLUGIN) IOutParaPlugin outPlugin;
```

在Activity被`onCreate`的时候注入并获取`OutParaPlugin`的实例：

```Java
@Override
protected void onCreate(Bundle savedInstanceState) {
    AndroidInjection.inject(this);
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    ButterKnife.bind(this);
    active = true;

    initPlugin(outPlugin, inPlugin, logPlugin);
    initDrawer();
    initFragment();

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        requestDrawOverLays();
    }
}
```
此时，我们获取plugin后，可以用`getPluginName()`初始化侧滑栏的菜单，当用户点击时，再通过`getPluginFragment()`进入Plugin内部相关的UI。我们也可以实现新的`IOutParaPlugin`的module来快速替换现有的plugin。

# 总结
将工程插件化，是约束代码边界的一个很好的实践。将庞大的工程拆分成一个个子工程，从编译上做到隔离，将不同的工程交由不同的人负责，避免了相互之间代码更改，同时提高了代码的可维护性。

# 参考信息
[微信Android模块化架构重构实践](https://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=2649286672&idx=1&sn=4d9db00c496fcafd1d3e01d69af083f9&chksm=8334cc92b4434584e8bdb117274f41145fb49ba467ec0cd9ba5e3551a8abf92f1996bd6b147a&scene=38#wechat_redirect)  
[Prism6下的MEF：第一个Hello World](http://www.cnblogs.com/youngytj/p/5670465.html)  
[Android Dagger (2.10/2.11) Butterknife MVP](https://proandroiddev.com/how-to-android-dagger-2-10-2-11-butterknife-mvp-part-1-eb0f6b970fd)  
[Prism PubSub Event](https://github.com/PrismLibrary/Prism/tree/master/Source/Prism/Events)
