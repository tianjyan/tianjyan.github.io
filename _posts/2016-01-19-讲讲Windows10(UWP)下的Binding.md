---
layout: post
title: 讲讲Windows10(UWP)下的Binding
date: 2016-01-19 21:15:58
tags: [UWP]
categories: [csharp]
---
## 前言
貌似最近来问我XAML这块的东西的人挺多的。有时候看他们写XAML这块觉着也挺吃力的，所谓基础不牢，地动山摇。XAML这块虽说和HTML一样属于标记语言，但是世界观相对更加庞大一点。

今天讲讲XAML中的Binding。没啥技术含量，全当是快速阅读。

Binding作为MVVM模式的一个相对核心的功能，一直是有争议的。使用数据绑定可以将我们的View和Model解耦，但是如果一旦出现Bug，我们将很难调试，还有一个问题就是数据绑定会带来过大的内存开销。
跟多数的技术一样，都有自己的两面性，具体运用场景如何抉择，应该充分考虑。

作为XAML的一部分，Binding的功能也随着XAML版本的变更着。目前为止，XAML一共有以下几个版本：
>* WPF Version
>* Silverlight 3 Version
>* Silverlight 4 Version
>* Windows 8 XAML/Jupiter(Windows Runtime XAML Framework) Version

其中WPF版本的Binding功能最强大，但也开销最大。本文中，我们主要讲述最新版本，即__Windows 8 XAML/Jupiter__这个版本的XAML中的Binding。

## 开始之前
要想讲明白Binding这个东西，我们先要从Binding类的继承层次开始讲。
```csharp
public class Binding : BindingBase, IBinding, IBinding2
{
    public Binding();

    public IValueConverter Converter { get; set; }
    public System.String ConverterLanguage { get; set; }
    public System.Object ConverterParameter { get; set; }
    public System.String ElementName { get; set; }
    public BindingMode Mode { get; set; }
    public PropertyPath Path { get; set; }
    public RelativeSource RelativeSource { get; set; }
    public System.Object Source { get; set; }
    
    public System.Object FallbackValue { get; set; }   
    public System.Object TargetNullValue { get; set; }
    public UpdateSourceTrigger UpdateSourceTrigger { get; set; }
}
```
可以看到Binding继承了BindingBase类，还继承了IBinding，IBinding2的接口。我们再分别看下这三个类和接口。首先看下我们的BindingBase。
```csharp
public class BindingBase : DependencyObject, IBindingBase
{
    public BindingBase();
}
```
BindingBase又继承了DependencyObject和IBindingBase。DependencyObject这个我们就不多讲了，IBindingBase只是一个空接口。看来BindingBase没有看到太多信息，我们再来看下IBinding和IBinding2。
```csharp
internal interface IBinding
{
    IValueConverter Converter { get; set; }
    System.String ConverterLanguage { get; set; }
    System.Object ConverterParameter { get; set; }
    System.String ElementName { get; set; }
    BindingMode Mode { get; set; }
    PropertyPath Path { get; set; }
    RelativeSource RelativeSource { get; set; }
    System.Object Source { get; set; }
}

internal interface IBinding2
{
    System.Object FallbackValue { get; set; }
    System.Object TargetNullValue { get; set; }
    UpdateSourceTrigger UpdateSourceTrigger { get; set; }
}
```

微软设计了一个Binding的基础模型，蕴含了接口分离原则(ISP)的思想，又提供了一个IBindingBase的空接口，如果你想实现自己的Binding模型，可以继承这个接口，这样可以和.NET类库风格统一。

## 讲讲IBinding
既然我们已经了解了Binding的大概的层次结构，那我们开始一个个讲讲这些都是怎么用的。

### IBinding中的Path
Path是我们相对用的比较多的，多数情况下，我们可以这样写
```csharp
Text="{Binding}"
```

XAML会取绑定源的ToString的值，所以我们可以重写Override方法来实现我们的需要的绑定。
我们也可以绑定具体的属性，比如：
```csharp
Text="{Binding Name}"
```
如果我们绑定了一个集合，那我们也可以尝试这样写：
```csharp
Text="{Binding MyList[1].Name}"
```
除了上述比较常用的，我们还有一个叫做ICustomPropertyProvider的接口，当你的类实现了这个接口中的
```csharp
string GetStringRepresentation() 
```
XAML就会去取这个函数返回的值。

__所以总结下我们的Path的绑定方式：__

```csharp
默认情况：
target Text="{Binding}"
source ToString()
//你可以重写ToString方法来改变值

属性绑定:
target Text="{Binding Name}"
source public String Name { get;}

索引器绑定：
target Text="{Binding MyList[1].Name}"

实现ICustomPropertyProvider的绑定：
target Text="{Binding}"
source String GetStringRepresentation() 
//实现方法获取值
```

###  IBinding中的Mode
Mode一共有三种，OneTime，OneWay，TwoWay。看字面的意思就很容易理解。
```csharp
//OneTime
Text="{Binding, Mode=OneTime}"
//OneWay
Text="{Binding, Mode=OneWay}"
//TwoWay
Text="{Binding, Mode=TwoWay}"
```
在OneWay和TwoWay中，如果想要对象的值变更时让绑定目标也变化，需要注意一下两点
>* 对于普通的属性，需要类实现INotifyPropertyChanged，并且对象值变化时手动通知变更。
>* 对于依赖属性，当触发SetValue方法后，PropertyChangedCallBack会通知变更，所以无需我们手动操作。
在UWP系统中，Mode的默认值为__OneWay__。

### IBinding中的RelativeSource
RelativeSource是一种相对关系找数据源的绑定。目前有两种：Self和TemplatedParent
```xml
//Self
<TextBlock Text="{Binding Foreground.Color.R, RelativeSource={RelativeSource Mode=Self}}" Foreground="Red"/>

//TemplatedParent
<DataTemplate> 
    <Rectangle Fill="Red" Height="30" Width="{Binding Width, RelativeSource={RelativeSource Mode=TemplatedParent}}">
</DataTemplate>  
```
RelativeSource绑定的方式我们常用于控件模板。默认值一般为null。

### IBinding中的ElementName
ElementName也是我们最常用的一种绑定方式，使用这个我们需要注意两点：
>* 指定的ElementName必须在当前XAML名称范围里。
>* 如果绑定目标位于数据模板或控件模板中，则为模板化父级的XAML名称范围。
举个例子：
```xml
<UserControl x:Name="Instance" Background="Red"> 
<Grid Background="{Binding Background, ElementName=Instance}"/> 
</UserControl> 
//or
<Page x:Name="Instance"> 
<Grid> 
    <ItemsControl ItemsSource="{Binding Users}"> 
    <ItemsControl.ItemTemplate> 
        <DataTemplate> 
        <Button Command=" {Binding DataContext.TestCommand, ElementName=Instance}"/> 
        </DataTemplate> 
    </ItemsControl.ItemTemplate> 
    </ItemsControl> 
</Grid> 
</Page>
```
### IBinding中的Source
Source也是我们常用的一种方式。
```xml
<Page> 
    <Page.Resources> 
        <SolidColorBrush x:Key="MainBrush" Color="Orange"/> 
    </Page.Resources> 
    <Grid Background="{Binding Source={StaticResource MainBrush}}"/> 
</Page>
//当然我们也可以简写为
<Grid Background="{StaticResource MainBrush}"/>
```
__一般情况下，Source，ElementName和RelativeSource三者是互斥的，指定多余一种的绑定方式会引发异常。__

### IBinding中的Converter
我们很难保证我们的对象值和我们绑定目标的类型一直，所以转换器可以将类型就行转换。
使用转换器我们要实现IValueConverter接口：
```csharp
public class BoolVisibilityConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, string language)
    {
        bool? result = value as Nullable<bool>;
        if(result == true)
        {
            return Visibility.Visible;
        }
        return Visibility.Collapsed;

    }

    public object ConvertBack(object value, Type targetType, object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
```
如果你的值需要转换回去，你也可以继续实现ConvertBack方法。
```xml
<Page> 
    <Page.Resources> 
        <local:BoolVisibilityConverter x:Key="BoolVisibilityConverter"/> 
    </Page.Resources> 
    <Grid> 
        <Border Visibility="{Binding Busy, Converter={StaticResource BoolVisibilityConverter}}"/> 
    </Grid> 
</Page>
```
### IBinding中的ConverterParameter和ConverterLanguage
这两个参数不能绑定，只能指定常量值。
```xml
<Border Visibility="{Binding Busy, Converter={StaticResource BoolVisibilityConverter}, 
ConverterParameter=One, ConverterLanguage=en-US}"/> 
```
IBinding中的参数基本上覆盖了我们多数的需求。尽管相对于WPF缺少了多值绑定等等，但我们也能够通过自定义一些附加属性来实现这些功能。

## IBinding2

IBinding2中的参数就相对使用的比较少了。

### IBinding2中的FallbackValue 
FallbackValue的用途是:当绑定对象不存在时，我们就使用FallbackValue的值：
```xml
<Page> 
    <Page.Resources> 
        <x:String x:Key="ErrorString">Not Found</x:String> 
    </Page.Resources> 
    <Grid> 
        <TextBlock Text="{Binding Busy, FallbackValue={StaticResource ErrorString}}"/> 
    </Grid> 
</Page>
```
### IBinding2中的TargetNullValue
TargetNullValue的用途是：当绑定对象为空时，我们就使用TargetNullValue的值：
```xml
<Page> 
    <Page.Resources> 
        <x:String x:Key="ErrorString">Not Found</x:String> 
    </Page.Resources> 
    <Grid> 
        <TextBlock Text="{Binding Busy, TargetNullValue={StaticResource ErrorString}}"/> 
    </Grid> 
</Page>
```

### IBinding2中的UpdateSourceTrigger
UpdateSourceTrigger的值有三种：Default，PropertyChanged，Explicit。
多数情况下大多数依赖项属性的默认值都为 PropertyChanged。但是Text属性不是。
PropertyChanged的意思是当绑定目标属性更改时，立即更新绑定源。而Explicit是只有UpdateSource方法时才更新绑定源。
举个例子：
```xml
<Grid> 
    <TextBox x:Name="TitleTextBox" Text="{Binding Title, ElementName=Instance, UpdateSourceTrigger=Explicit, Mode=TwoWay}" /> 
    <Button Click="Button_Click"/> 
</Grid>
```
```csharp
private void Button_Click(object sender, RoutedEventArgs e) 
{ 
    var current = this.Title; 
    TitleTextBox.GetBindingExpression(TextBox.TextProperty).UpdateSource(); 
    current = this.Title; 
}
```
有关uwp的Binding就说到这里。谢谢~

## 参考资料
[被误解的MVC和被神化的MVVM](https://www.infoq.com/cn/articles/rethinking-mvc-mvvm)  
[Extensible Application Markup Language](https://en.wikipedia.org/wiki/Extensible_Application_Markup_Language#Differences_between_versions_of_XAML)  
[RelativeSource 标记扩展](https://msdn.microsoft.com/zh-cn/library/windows/apps/hh758284.aspx)
[UpdateSourceTrigger enumeration](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.data.updatesourcetrigger)
  
## 个人推荐
[我的博客园](http://www.cnblogs.com/youngytj/)  
[我的简书](http://www.jianshu.com/users/35fb1e295d20/latest_articles)  
[我的Github](https://github.com/youngytj)  