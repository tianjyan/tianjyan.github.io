---
title: WPF和Expression Blend开发实例:一个样式实现的数字输入框
date: 2015-03-15 21:15:58
tags: [WPF]
categories: [CSharp]
---
今天来一个比较奇淫技巧的手法，很少人用，同时也不推荐太过频繁的使用。

先上样式：
```xml
<Style x:Key="NumberTextBox" TargetType="{x:Type FrameworkElement}">
    <EventSetter Event="PreviewTextInput" Handler="TextBox_TextInput"/>
    <Setter Value="False" Property="InputMethod.IsInputMethodEnabled"/>
</Style>
<x:Code>
    <![CDATA[
        private void TextBox_TextInput(object sender, TextCompositionEventArgs e)
        {
            bool flag = true;
            foreach (char c in e.Text)
            {
                if (c < '0' || c > '9')
                {
                    flag = false;
                }
            }
            e.Handled = !flag;
        }
    ]]>
</x:Code>
```        
其实核心只有一个，就是xaml里写代码。

> [x:Code Msdn介绍](http://msdn.microsoft.com/zh-cn/library/ms750494.aspx)

引用样式：
```xml
<TextBox Height="20" Width="200" Margin="10,0" Style="{StaticResource NumberTextBox}"/>
```
源代码下载：
http://files.cnblogs.com/youngytj/TextBoxStyle.rar