---
layout: post
title: WPF和Expression Blend开发实例:模拟QQ登陆界面打开和关闭特效
date: 2014-12-23 21:15:58
tags: [WPF]
categories: [CSharp]
---
不管在消费者的心中腾讯是一个怎么样的模仿者抄袭者的形象，但是腾讯在软件交互上的设计一直是一流的。正如某位已故的知名产品经理所说的：设计并非外观怎样，感觉如何。设计的是产品的工作原理。我觉得腾讯掌握了其精髓。在2013版的桌面版QQ中，腾讯的登陆界面在打开的时候有一个展开的过程，而关闭的时候有个收缩的过程。效果如图：  
![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2014-12-23-WPFQQ-01.jpg)  
借助WPF和Expression Blend，我们可以轻易的实现这么一个效果，最终用比较慢的速率实现这个效果如下：  
![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2014-12-23-WPFQQ-02.jpg)

这个效果一共能够分成两个部分:展开和收缩，具体的代码如下：

收缩的代码：
```xml
<Storyboard x:Key="shrink">
    <DoubleAnimation From="1" To="0" Duration="0:0:6"
                        Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[0].Offset"/>
    <DoubleAnimation Duration="0:0:4.5" BeginTime="0:0:1.5" From="1" To="0"
                        Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[1].Offset"/>
    <ColorAnimation  Duration="0" From="#FF000000" To="#00000000" Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[1].Color"/>
</Storyboard>
```    
展开的代码：
```xml
<Storyboard x:Key="spread">
    <DoubleAnimation From="0" To="1" Duration="0:0:6"
                        Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[0].Offset"/>
    <DoubleAnimation Duration="0:0:4.5" BeginTime="0:0:1.5" From="0" To="1"
                        Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[1].Offset"/>
    <ColorAnimation BeginTime="0:0:6" Duration="0" From="#00000000" To="#FF000000" Storyboard.TargetName="layoutroot"
                        Storyboard.TargetProperty="(UIElement.OpacityMask).(GradientBrush.GradientStops)[0].Color" />
</Storyboard>
``    
其实本质上就是用Storyboard控制OpacityMask的变化来实现效果，OpacityMask的的声明代码如下：
```xml
<Grid.OpacityMask>
    <LinearGradientBrush EndPoint="0.5,1" StartPoint="0.5,0">
        <GradientStop Color="#00000000" Offset="0"/>
        <GradientStop Color="#FF000000" Offset="0"/>
    </LinearGradientBrush>
</Grid.OpacityMask>
```    
然后在后台代码中控制动画：

在构造函数中添加如下代码：
```CSharp
InitializeComponent();
sb= (System.Windows.Media.Animation.Storyboard)layoutroot.Resources["spread"];
sb.Completed += (s, e) =>
{
    sb = (System.Windows.Media.Animation.Storyboard)layoutroot.Resources["shrink"];
    sb.Completed += (sender, Event) => Application.Current.Shutdown();
};
if (sb != null)
{
    sb.Begin();
}
```
关闭按钮的事件如下：
```CSharp
private void OnClick(object sender, RoutedEventArgs e)
{
    if (sb != null)
    {
        sb.Begin();
    }
}
```   
可以通过调节上面的动画中的时间来实现和qq登陆界面一样的效果。这只是一些简单的动画，所以可以直接在VS里编写，如果是一些更加复杂的动画，那就需要借助Blend来实现了，这个以后有机会再说吧。