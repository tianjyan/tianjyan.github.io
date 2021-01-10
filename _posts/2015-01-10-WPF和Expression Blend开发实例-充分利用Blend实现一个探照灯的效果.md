---
title: WPF和Expression Blend开发实例:充分利用Blend实现一个探照灯的效果
date: 2015-01-10 21:15:58
tags: [WPF]
categories: [CSharp]
---
本篇文章阅读的基础是在读者对于WPF有一定的了解并且有WPF相关的编码经验，对于Blend的界面布局有基础的知识。文章中对于相应的在Blend中的操作进行演示，并不会进行细致到每个属性的介绍。同时，本篇文章所用的Blend版本是5.0.40218.0，即VS2012对应的版本，对于其他版本的操作区别，请读者自行研究。Ok，我们现在开始，本篇文章最终的效果如下图所示:  
![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-01.jpg)
好，我们开始分步介绍过程，除了最后设计的一个按钮的后台代码需要使用到代码之外，其余的操作我们都使用图形操作。

1.新建项目

打开Blend，新建一个WPF应用程序，命名为WPF_SearchLight，确定完成，具体如下：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-02.jpg)

2.单击项目自动生成的MainWindow，调整属性：

> WindowStyle设置为None;  
> Background设置为纯黑色;   
> Height设置为200;  
> Width设置为600;  
> WindowsStartupLocation设置为CenterScreen.  

最终效果图如下：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-03.jpg)
![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-04.jpg)

3.添加一个按钮，用于退出程序，将它放置在TextBlock的右端，并在后台代码中添加处理事件。

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-05.jpg)   
```CSharp
private void OnClick(object sender, System.Windows.RoutedEventArgs e)
{
    Application.Current.Shutdown();
}
```
4.接下来我们需要设计一个圆和一个矩形，画一个矩形，遮盖住TextBlock，它的长度必须为TextBlock的两倍以上，并且Background设置为纯黑色，同时A的值设置为80%。如下图：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-06.jpg)   

接下来画一个圆，圆的直径与TextBlock的高度相等，效果如图：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-07.jpg)   

然后在在对象和时间线窗口中同时选中(按住Shift)，右击，选中合并→相减，会生成一个Path路径。(此处应注意先按矩形，再按圆形即矩形-圆形)

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-08.jpg)   

5.接下来就要设计动画了，在对象和时间线中点击Path，然后点击"+"，新建一个名为"SearchLight"的动画如图：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-09.jpg)   

这时候会出现时间面板，如图:

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-10.jpg)   

点击时间左边的按钮，记录此时的Path位置；
将时间轴拖动到6处，然后水平移动Path至退出按钮处，点击时间左边的按钮，记录此时的Path位置；
将时间轴拖动到7处，不移动Path，点击时间左边的按钮，记录此时的Path位置。
动画设定完成，此时可以点击播放按钮查看效果。

6还有一些其他的操作， 选定SearchLight，设置动画的一些属性，如AutoReverse勾选，RepeatBehavior选择为Forever，如图：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-11.jpg)

在触发器的窗口中，可以选择触发动画的时机，如图：

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2015-01-10-WPFLight-12.jpg)   

最终实现的效果如一开始所示的图片一样。