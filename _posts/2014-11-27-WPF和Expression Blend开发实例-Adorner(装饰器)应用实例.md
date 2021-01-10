---
title: WPF和Expression Blend开发实例：Adorner(装饰器)应用实例
date: 2014-11-27 21:15:58
tags: [WPF]
categories: [CSharp]
---
装饰器:
> 表示用于修饰 UIElement 的 FrameworkElement 的抽象类

简单来说就是，在不改变一个UIElement结构的情况下，将一个Visual对象加到它上面。

应用举例:
现在我们拥有一个文本框，但是我们需要限定输入的字符串，当输入的是非法字符串的时候，要求将文本框的四周包裹一个红色的边框。通常我们可以用Border将文本框包裹在里面，然后动态地改变它的颜色来实现功能。那么现在我们可以直接在文本框上面加一个装饰器来实现。

> [Adorner类](http://msdn.microsoft.com/zh-cn/library/system.windows.documents.adorner.aspx)  
> [AdornerLayer类](http://msdn.microsoft.com/zh-cn/library/system.windows.documents.adornerlayer.aspx)

装饰器是放在装饰层(AdornerLayer)里面的，这就意味着我们可以添加多个装饰器到UIElement上。通过AdornerLayer的静态方法GetAdornerLayer，我们可以很轻松的获取一个UIElement的装饰层。
接下来我们只要继承Adorner这个抽象类，来实现一个真实可用的装饰器，然后把装饰器加到控件的装饰层上面就可以了。
```CSharp
public class AdornerForTextBox : Adorner
{
    private VisualCollection visual;
    private Border border;

    public AdornerForTextBox(UIElement uiElement)
        : base(uiElement)
    {
        visual = new VisualCollection(this);
        #region 定义装饰器的样子
        border = new Border();
        border.BorderBrush = Brushes.Red;
        border.BorderThickness = new Thickness(1);
        border.Height = 20;
        border.Width = 80;
        #endregion
        visual.Add(border);
    }

    protected override int VisualChildrenCount
    {
        get
        {
            return visual.Count;
        }
    }

    protected override Visual GetVisualChild(int index)
    {
        return visual[index];
    }

    protected override Size MeasureOverride(Size constraint)
    {
        return base.MeasureOverride(constraint);
    }

    protected override Size ArrangeOverride(Size finalSize)
    {
        border.Arrange(new Rect(finalSize));
        border.Margin = new Thickness(0， 0， 0， 0);//绘制装饰器在元素上的最终位置
        return base.ArrangeOverride(finalSize);
    }
}
```
显示装饰器
```CSharp
var adornerLayer = AdornerLayer.GetAdornerLayer(tb_Adorner);
if (adornerLayer != null)
{
    //清空原有的装饰器
    var adorners = adornerLayer.GetAdorners(tb_Adorner);
    if (adorners != null && adorners.Count() > 0)
    {
        for (int i = 0; i < adorners.Count(); i++)
        {
            adornerLayer.Remove(adorners[i]);
        }
    }

    adornerLayer.Add(new AdornerForTextBox(tb_Adorner));
}
```
效果图:

![Image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2014-11-27-WPFAdorner-01.png)
