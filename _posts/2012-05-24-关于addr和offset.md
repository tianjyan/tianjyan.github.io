---
layout: post
title: 关于addr和offset
date: 2012-05-24 21:15:58
tags: [Win32]
categories: [C]
---

# 相同点

1. addr和offset 操作符都是获得操作数的偏移地址；
2. addr和offset 的处理都是先检查处理的是全局还是局部变量，若是全局变量则把其地址放到目标文件中。

# 不同点

1. addr伪操作符,只能用在 invoke 伪指令语句中；
2. offset伪操作符可以用在任何可能涉及偏移地址的指令(当然包括 invoke 伪指令)并想获取操作数偏移地址的场合中；
3. addr不能处理向前引用(即addr引用的操作数必须在使用addr前就得定义或声明)，而offset则能(不管引用的操作数是其前或其后定义或声明)；
4. addr是运行阶段在堆栈中分配内存空间，offset是编译阶段由编译器解释。因此，addr可以处理局部变量而 offset则不能。
5. addr如果检查到待处理的变量是局部变量，就在执行invoke语句前产生如下指令序列：    
    >lea  eax,operand  
    >push eax  
    >因为lea指令能够在运行时决定标号的有效地址，所以有了上述指令序列，就可以保证invoke的正确执行了。

# 总结
为了避免出现错误，建议除在局部变量中引用addr操作符外，其它场合使用offset。