---
layout: post
title: WPF和Expression Blend开发实例:Loading动画
date: 2015-02-08 21:15:58
tags: [WPF]
categories: [cSharp]
---
今天来点实际的，项目中可以真实使用的，一个Loading的动画，最后封装成一个控件，可以直接使用在项目中，先上图：

![Image](/images/2015-02-08-WPFLoading-01.png)
整个设计比较简单，就是在界面上画18个Path，然后通过动画改变OpacityMask的值来实现一种动态的效果。

因为整个过程比较简单，所以其实没有用到Blend，唯一一个需要注意的是Path的路径值是请美工从PS里生成的，路径如下：
```xml
<Geometry x:Key="Block">
            M291.15499,85.897525 
            C291.15499,85.897525 
            301.88917,85.87921 
            301.88917,85.87921 
            301.88917,85.87921 
            300.38339,94.355061 
            300.38339,94.355061 
            300.38339,94.355061 
            292.85366,94.355042 
            292.85366,94.355042 
            292.85366,94.355042 
            291.15499,85.897525 
            291.15499,85.897525Z
</Geometry>
```
Path的代码如下，每个Path一次旋转特定的角度围成一个圆形
```xml
<Path x:Name="block0" Style="{StaticResource PathStyle}" Data="{StaticResource Block}"  OpacityMask="#00000000" >
    <Path.RenderTransform>
        <RotateTransform Angle="180"/>
    </Path.RenderTransform>
</Path>

<Style x:Key="PathStyle" TargetType="Path">
            <Setter Property="Fill" Value="#FF0092FF"></Setter>
            <Setter Property="Stretch" Value="Fill"></Setter>
            <Setter Property="RenderTransformOrigin" Value="0.5,5"></Setter>
            <Setter Property="VerticalAlignment" Value="Top"></Setter>
            <Setter Property="Height" Value="30"></Setter>
</Style>
```
单个Path的动画：
```xml
<ColorAnimationUsingKeyFrames Duration="00:00:03.6" Storyboard.TargetName="block0" Storyboard.TargetProperty="(UIElement.OpacityMask).(SolidColorBrush.Color)">
    <LinearColorKeyFrame KeyTime="00:00:00.0" Value="#FF000000"/>
    <LinearColorKeyFrame KeyTime="00:00:00.2" Value="#EF000000"/>
    <LinearColorKeyFrame KeyTime="00:00:00.4" Value="#E2000000"/>
    <LinearColorKeyFrame KeyTime="00:00:00.6" Value="#D3000000"/>
    <LinearColorKeyFrame KeyTime="00:00:00.8" Value="#C6000000"/>
    <LinearColorKeyFrame KeyTime="00:00:01.0" Value="#B7000000"/>
    <LinearColorKeyFrame KeyTime="00:00:01.2" Value="#AA000000"/>
    <LinearColorKeyFrame KeyTime="00:00:01.4" Value="#9B000000"/>
    <LinearColorKeyFrame KeyTime="00:00:01.6" Value="#8E000000"/>
    <LinearColorKeyFrame KeyTime="00:00:01.8" Value="#7F000000"/>
    <LinearColorKeyFrame KeyTime="00:00:02.0" Value="#72000000"/>
    <LinearColorKeyFrame KeyTime="00:00:02.2" Value="#63000000"/>
    <LinearColorKeyFrame KeyTime="00:00:02.4" Value="#56000000"/>
    <LinearColorKeyFrame KeyTime="00:00:02.6" Value="#3D000000"/>
    <LinearColorKeyFrame KeyTime="00:00:02.8" Value="#26000000"/>
    <LinearColorKeyFrame KeyTime="00:00:03.0" Value="#19000000"/>
    <LinearColorKeyFrame KeyTime="00:00:03.2" Value="#0C000000"/>
    <LinearColorKeyFrame KeyTime="00:00:03.4" Value="#00000000"/>
    <LinearColorKeyFrame KeyTime="00:00:03.6" Value="#FF000000"/>
    </ColorAnimationUsingKeyFrames>
```     
源代码下载：
http://files.cnblogs.com/youngytj/LoadingAnimations.rar