---
layout: post
title: Java和C#对比(1):基本一致的地方
date: 2016-06-28 21:15:58
tags: [Java Vs C#]
categories: [学习笔记]
---

最近开始把开发的重心从C#往Java迁移，虽然之前对Java语言有所关注，但毕竟也看得不多。从语言设计者的区别上讲，C#的设计更加关注语言本身，微软更加关注代码是否简洁，不断的为C#加入各种特性，比如各种语法糖，从泛型，可空类型，隐式类型到Lamada再到动态类型等，微软一直围绕代码简洁，减少Bug等实际的开发过程中问题来进行改进。
而Java的设计不同，java更关注系统本身，强调更好的解藕，始终坚持OOP的本质。所以从应用结构上讲，同时代的Java要比C#更美观，易于维护，代码腐烂的速度也慢。
所以虽然语法相似，但是关注点不同，所以很难说出这两种语言谁优谁劣。

本篇系列准备从语法的角度，对Java和C#对比，希望读者能在已有C#或者Java一门语言的基础上，对另一门语言有个快速而又全面的概念。需要注意的是，本文所对比的语言版本是[C#6.0](https://msdn.microsoft.com/zh-cn/library/618ayhy6.aspx)和[Java 8](http://docs.oracle.com/javase/specs/index.html)。  

##  1.对Java和C#而言，一切都是对象  
C#和Jav中的所有对象都从Object类中派生，C#中是System.Object，Java中是java.lang.Object。其中需要注意的是，在C#中，Object和小写的object并无实质区别。两个Object中的自带的方法一览：  
<table border="1"><tr><td>System.Object</td><td>java.lang.Object</td></tr><tr><td>Equals</td><td>equals</td></tr><tr><td>GetHashCode</td><td>hashCode</td></tr><tr><td>GetType</td><td>getClass</td></tr><tr><td>ReferenceEquals</td><td></td></tr><tr><td>Finalize</td><td>finalize</td></tr><tr><td>MemberwiseClone</td><td>Clone</td></tr><tr><td>ToString</td><td>toString</td></tr><tr><td></td><td>wait</td></tr><tr><td></td><td>notify</td></tr><tr><td></td><td>notifyAll</td></tr></table>

参考信息：  
[C#中的基元类型](http://www.cnblogs.com/ASPNET2008/archive/2009/06/14/1503041.html)

## 2.关键字一览  
对于多数的关键字，C#和Java是一样的。
<table border="1"><tbody><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">C# keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">Java keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">C# keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">Java keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">C# keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">Java keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">C# keyword</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">Java keyword</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">abstract</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">abstract</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">as</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">base</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">super</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">bool</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">boolean</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">break</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">break</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">byte</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">case</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">case</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">catch</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">catch</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">char</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">char</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">checked</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">class</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">class</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">const</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">const</span> <sup><a>1</a></sup></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">continue</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">continue</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">decimal</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">default</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">default</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">delegate</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">do</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">do</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">double</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">double</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">else</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">else</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">enum</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">enum</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">event</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">explicit</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">extern</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">native</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">false</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">false</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">finally</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">finally</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">fixed</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">float</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">float</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">for</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">for</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">foreach</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">for</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">goto</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">goto</span> <sup><a>1</a></sup></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">if</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">if</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">implicit</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">in</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">int</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">int</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">interface</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">interface</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">internal</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">protected</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">is</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">instanceof</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">lock</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">synchronized</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">long</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">long</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">namespace</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">package</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">new</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">new</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">null</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">null</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">object</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">operator</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">out</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">override</span> <sup><a>2</a></sup></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">params</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">...</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">private</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">private</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">protected</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">public</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">public</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">readonly</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">ref</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">return</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">return</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">sbyte</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">byte</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">sealed</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">final</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">short</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">short</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">sizeof</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">stackalloc</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">static</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">static</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">string</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">struct</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">switch</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">switch</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">this</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">this</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">throw</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">throw</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">true</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">true</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">try</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">try</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">typeof</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">uint</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">ulong</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">unchecked</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">unsafe</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">ushort</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">using</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">import</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">virtual</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">void</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">void</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">volatile</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">volatile</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">while</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">while</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">:</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">extends</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">:</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">implements</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">strictfp</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">throws</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:black">N/A</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:red">transient</span> <sup><a></a>3</sup></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"></td></tr></tbody></table>

C#除了上述关键字外，还有一类上下文关键字。上下文关键字用于提供代码中的特定含义，但它不是 C# 中的保留字。某些上下文关键字（如 partial 和 where）在两个或更多个上下文中具有特殊含义。

<table border="1"><tbody><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">add</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">Alias</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">ascending</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">async</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">await</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">descending</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">dynamic</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">from</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">get</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">global</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">group</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">into</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">join</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">let</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">orderby</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">partial(类型)</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">partial(方法)</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">remove</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">select</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">set</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">value</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">var</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">where(泛型类型约束)</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">where(查询子句)</span></p></td></tr><tr><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue">yield</span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue"></span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue"></span></p></td><td style="PADDING-BOTTOM:3.75pt;PADDING-LEFT:3.75pt;PADDING-RIGHT:3.75pt;PADDING-TOP:3.75pt"><p><span style="COLOR:blue"></span></p></td></tr></tbody></table>

参考信息：  
[C# Reference #Keywords ](https://msdn.microsoft.com/en-us/library/x53a06bb.aspx)  
[Java Language Specification #Keywords](http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-3.9)  

_注释1:const和goto仅作为保留字，并未在Java中使用。_   
_注释2:Java中的*@override*注解标记和*Override*是等效的。_  
_注释3:C#中的*[NonSerialized]*属性(attribute)标记和*transient*是等效的。_

## 3.虚拟机与语言运行时
Java需要将代码编译成字节码(Java byte code)，并由在JVM(Java Virtual Machine)运行。而C#需要将代码编译成中间语言(Intermediate Language)，然后由CLR(Common Language Runtime)运行。两个平台都支持通过即时编译器(Just In Time Compiler)进行原生代码的编译。
需要注意的是，Java可以在运行前通过JIT直接编译成原生代码，而.Net只能在运行时处理IL语言。

参考信息：  
[just-in-time compiler (JIT)](http://whatis.techtarget.com/definition/just-in-time-compiler-JIT)

## 4.基于堆(heap)的类型和垃圾回收
在Java中所有的对象都通过*new*关键字在堆上创建。而C#中绝大多数对象通过*new*关键字创建。JVM和CLR都会负责将创建的对象销毁。

_注释:C#也支持在栈(stack)上创建对象(值类型)_

参考信息：  
[你真的了解C# 值类型和引用吗](http://www.cnblogs.com/Scissors/archive/2012/08/18/2645056.html)  
Jon Skeet, 姚琪琳. 深入理解C#[M]. 北京:人民邮电出版社, 2014. 39-43   
[Mark-compact algorithm](https://en.wikipedia.org/wiki/Mark-compact_algorithm)

## 5.数组可以是交错的
不同于C和C++中的，二维或者多维的数组大小应该是一样的。C#和Java运行多维数组的大小不一样。举个例子：

    int[][] myArray = new int[2][];
    myArray[0] = new int[3];
    myArray[1] = new int[9];
    
## 6.没有全局方法
不同于C++，Java和C#中的方法必须在一个类之中。

## 7.单一继承
Java和C#都仅支持从一个类继承，但允许实现多个接口。

## 8.String是不可变的
C#中有个System.String类型和Java中的Java.lang.String对应。两个类都是不可以修改的，意味着字符串的字一旦生成，就不能被修改。两个类都暴露了一些方法来返回修改后的结果，但其实字符串本身并未被修改。
举个例子：

    C#：
    string csStr = “Young Ytj";
    csStr.ToLower();//字符串本身并为被更改，只是新生成了一个小写的字符串。

    Java：
    String jStr ＝ “Young Ytj”;
    jStr.toLowerCase();//字符串本身并为被更改，只是新生成了一个小写的字符串。

## 9.不可扩展的类
C#和Java中都可以控制一个类不被继承。C#中用*sealed*，Java中用*final*。

    C#：
    sealed class CSharpClass
    {
      //Field etc...
    }

    Java：
    final clas JavaClass{
      //Field etc...
    }

## 10.异常
异常在C#和Java中是相似的。两种语言都支持使用try catch的方式抓住异常，然后在finally中处理未释放的资源。两个语言的异常都继承与一个Exception类。
异常能够被抓住并重新抛出。

    C#：
    class MyException : Exception
    {
        public MyException(string message) : base(message){}
        public MyException(string message, Exception innerException) : base(message, innerException){}
    }

    public class ExceptionTest
    {
      static void DoWork()
      {
        throw new Exception();
      }

      public static void Main(string[] args)
      {
        try
        {
          try
          {
            DoWork();
            return; //不会到达的代码
          } 
          catch(Exception ex)
          {
            throw new MyException("MyException occured", ex);//重新抛出异常
          }
        }
        finally
        {
          Console.WriteLine("Finally block executes even though MyException not caught");
        }
      }
    }

    Java：
    class MyException extends Exception{
      public MyException(String message){ super(message);}
      public MyException(String message, Exception innerException){ super(message, innerException);}
    }

    public class ExceptionTest{
      static void DoWork(){
        throw new Exception();
      }

      public static void main(String[] args) throws Exception{
        try{
          try{
            DoWork();
            return; //不会到达的代码
          }catch(Exception ex){
            throw new MyException("MyException occured", ex);//重新抛出异常
          }
        }finally{
          System.out.println("Finally block executes even though MyException not caught");
        }
      }
    }

## 11.成员变量和构造函数的初始化顺序
Java和C#中的类而言，它们的静态构造函数总是最先初始化的，其次是它们的成员变量，然后再初始化构造函数，对于类的静态成员，它们只有在调用的时候才会被初始化。

    C#：
    class StaticInitTest
    {
        string instMember = InitInstance(); 
        string staMember = InitStatic(); 
        
        StaticInitTest()
        {
          Console.WriteLine("In instance constructor");
        }

        static StaticInitTest()
        {
          Console.WriteLine("In static constructor");
        }

        static String InitInstance()
        {
          Console.WriteLine("Initializing instance variable");
          return "instance";
        }

        static String InitStatic()
        {
          Console.WriteLine("Initializing static variable");
          return "static";
        }

        static void DoWork()
        {
          Console.WriteLine("Invoking static DoWork() method");
        }
    
        public static void Main(string[] args)
        {
          Console.WriteLine("Beginning main()");
          StaticInitTest.DoWork(); 
          StaticInitTest sti = new StaticInitTest(); 
          Console.WriteLine("Completed main()");
        }
    }

    Java：
    class StaticInitTest{
      String instMember = initInstance(); 
      String staMember = initStatic(); 

      StaticInitTest(){
        System.out.println("In instance constructor");
      }

      static{
        System.out.println("In static constructor");
      }

      static String initInstance(){
        System.out.println("Initializing instance variable");
        return "instance";
      }

      static String initStatic(){
        System.out.println("Initializing static variable");
        return "static";
      } 

      static void doWorK(){
        System.out.println("Invoking static DoWork() method");
      }

      public static void main(String[] args){
        System.out.println("Beginning main()");
        StaticInitTest.doWorK(); 
        StaticInitTest sti = new StaticInitTest(); 
        System.out.println("Completed main()");
      }
    }


输出结果：  
In static constructor  
Beginning main()  
Invoking static DoWork() method  
Initializing instance variable  
Initializing static variable  
In instance constructor  
Completed main()  

## 12.装箱和拆箱
有时候我们需要将一个值类型装换为一个Object的类型，Java和C#中将这个过程叫做装箱。那么反过来，把一个Object类型转成一个值类型就叫做拆箱。

    C#：
    struct Point
    {
        private int x; 
        private int y; 

        public Point (int x, int y)
        {
          this.x = x;
          this.y = y; 
        } 
        
        public override string ToString()
        {
          return String.Format("({0}, {1})", x, y); 
        }
    }

    class Test
    {
        public static void PrintString(object o)
        {
            Console.WriteLine(o);
        }

        public static void Main(string[] args)
        {
          Point p = new Point(10, 15); 
          ArrayList list = new ArrayList(); 
          int z = 100;

          PrintString(p); //p被装箱成object对象并传给PrintString函数
          PrintString(z); //z被装箱成object对象并传给PrintString函数g

          // int和float被装箱并放入集合中 
          list.Add(1); 
          list.Add(13.12); 
          list.Add(z); 
          
          for(int i =0; i < list.Count; i++)
          PrintString(list[i]);    
        }
    }

    Java：
    class Test{
      public static void PrintString(Object o){
        System.out.println(o);
      }

      public static void PrintInt(int i){
        System.out.println(i); 
      }

      public static void main(String[] args){
        Vector list = new Vector(); 
        int z = 100;
        Integer x = new Integer(300);
        PrintString(z); //z装箱成Object并传给PrintString
        PrintInt(x); //x拆箱成int并传给PrintInt

        //int和float被装箱并放入集合中 
        list.add(1); 
        list.add(13.12); 
        list.add(z); 
              
        for(int i =0; i < list.size(); i++)
          PrintString(list.elementAt(i));    
              
      }
    }

## 13.跨平台移植
Java技术一个很大的卖点就是Java写的应用程序一次编写到处运行。Sun官方支持Linux、Solaris 和 Windows，其它一些公司也实现了OS/2, AIX 和MacOS平台的Java。.NET也通过Mono项目和Ximian提供了移植性的支持。

## 14.静态导入
静态导入特性使我们可以访问类静态成员的时候不需要指定类名，这个特性可以让我们减少代码的冗余，特别对于某些帮助类型来说很方便。静态导入和普通的import语句很像，只不过多了static关键字，导入的是类而不是包名。

    Java:
    import static java.awt.Color.*; 

    public class Test{

        public static void main(String[] args) throws Exception{

        //constants not qualified thanks to static import
        System.out.println(RED + " plus " + YELLOW + " is " + ORANGE);
        }
    }

输出：

    java.awt.Color[r=255,g=0,b=0] plus java.awt.Color[r=255,g=255,b=0] is java.awt.Color[r=255,g=200,b=0]