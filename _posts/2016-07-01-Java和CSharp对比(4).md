---
layout: post
title: Java和C#对比(4):C#有但Java没有的地方
date: 2016-07-01 21:15:58
tags: [Java Vs C#]
categories: [学习笔记]
---
## 1.对象清理
为了提供完全控制类使用的资源，C#提供了System.IDisposable接口，它包含Dispose()方法可以让类的使用者在使用类之后释放必要的资源。管理诸如数据库或文件句柄的类可以从这种模式中收益。Dispose提供了一种确定的方式在类不使用的时候释放资源，这和Java或C#的终结器不同。一般会在Dispose方法的实现中调用GC类的SupressFinalize 方法，因为我们一般通过Dispose方法显式释放资源而不需要运行时的终结器。C#还提供了诸如using关键字之类的语法糖通过Dispose方法释放资源。如果类是Disposable的话，最好让Dispose()方法是幂等的（也就是可以多次调用Dispose()），可以在Dispose()方法中设置一个标志位来检查是否已经Dispose。如下例子演示了类保持打开文件，直到Dispose()方法调用后来表示文件不需要打开了。
```CSharp
public class MyClass : IDisposable 
{     
    bool disposed = false; 
    FileStream f; 
    StreamWriter sw; 
    private String name;
    private int numShowNameCalls = 0; 

    MyClass(string name)
    {       
        f = new FileStream("logfile.txt", FileMode.OpenOrCreate); 
        sw = new StreamWriter(f);
        this.name = name;
        Console.WriteLine("Created " + name);   
    }

    ~MyClass()
    {
        Dispose(false); 
    }

    public void Dispose()
    {
        if(!disposed){
            Dispose(true);
    }
}

private void Dispose(bool disposing)
{
    /* prevents multiple threads from disposing simultaneously */ 
    lock(this)
    { 
        /* disposing variable is used to indicate if this method was called from a 
        * Dispose() call or during finalization. Since finalization order is not 
        * deterministic, the StreamWriter may be finalized before this object in 
        * which case, calling Close() on it would be inappropriate so we try to 
        * avoid that. 
        */
        if(disposing)
        {     
            Console.WriteLine("Finalizing " + name);      
            sw.Close(); /* close file since object is done with */ 
            GC.SuppressFinalize(this);
            disposed = true; 
        } 
    }
}
    
public string ShowName()
{
    if(disposed)            
        throw new ObjectDisposedException("MyClass");

    numShowNameCalls++; 
    sw.Write("ShowName() Call #" + numShowNameCalls.ToString() + "\n");         
    
    return "I am " + name; 
}

public static void Main(string[] args)
{
    using (MyClass mc = new MyClass("A MyClass Object"))
    {
        for(int i = 0; i < 10; i++)
        {
            Console.WriteLine(mc.ShowName()); 
        } //for
    }/* runtime calls Dispose on MyClass object once "using" code block is exited, even if exception thrown  */ 
    
    }//Main 
}
```

如上的模式和C++方式的析构器很像，只不过不需要考虑内存分配。终结器这种不精确的特性一致被Java开发者诟病，有了Dispose后这不再是问题了。

_注释：调用Dispose()方法不等同于要求对象被垃圾回收，只不过由于不需要终结器之后可以加速被回收。_

## 2.可选参数
可选参数，是指给方法的特定参数指定默认值，在调用方法时可以省略掉这些参数。但要注意：
* 可选参数不能为参数列表的第1个参数，必须位于所有的必选参数之后（除非没有必选参数）；
* 可选参数必须指定一个默认值，且默认值必须是一个常量表达式，不能为变量；
* 所有可选参数以后的参数都必须是可选参数。
```CSharp
class Test
{
    private static void Main()
    {
        int result = Add(1);
        Console.WriteLine(result);
    }

    private static int Add(int a, int b = 1)
    {
        return a + b;
    }
}
```

## 3.值类型（结构）
在Java和C#中，堆上的东西只能等垃圾回收来收集而在栈上的对象会自动被系统回收。一般在栈上分配的内存会比在堆上略快。

在Java中，所有的类都在堆上创建而基元类型在栈上创建。如果对象很小并且很常用的话只能在堆上分配的话会造成一定的性能负担，C#提供了一种机制可以让某种类是基于栈分配的，叫做结构，其实C#内建的诸如int的基元类型就是使用结构来分配的。和类不同，值类型一般按值传递并且不会被垃圾收集。要使用基于栈的类，可以使用struct来替代class关键字。要创建C#结构可以使用和类一样的new关键字。如果结构使用默认构造方法语法创建，那么结构的字段都会使用0初始化。但是，不可以为结构定义默认构造方法。

```CSharp
struct Point 
{
    public int x; 
    public int y; 

    public Point( int x, int y)
    {  
        this.x = x; 
        this.y = y;
    }

    public override string ToString()
    {
        return String.Format("({0}, {1})", x, y); 
    }

    public static void Main(string[] args)
    {
        Point start = new Point(5, 9); 
        Console.WriteLine("Start: " + start);

        /* The line below wouldn't compile if Point was a class */ 
        Point end = new Point(); 
        Console.WriteLine("End: " + end);
    }
} // Point
```

## 4.运行时类型标识（as运算符）
C#的as运算符和C++的dynamic_cast结构一样。as运算符的作用是尝试把类型转换为某种类型，如果不成功的话返回null。

```CSharp
MyClass mc = o as MyClass; 

if(mc !=  null)      //check if cast successful 
mc.doStuff();
```
_注释：as不能用于值类型。_
 
## 5.属性
属性可以避免直接访问类的成员和Java的getters以及setters很像。可以使用属性来访问类的字段或成员属性，但又避免使用方法。可以创建只读、只写或读写属性，此外还可以创建属性让getter和setter具有不同的访问性（比如public的getter和private的setter），如下是使用属性的例子：

```CSharp
public class User 
{
    public User(string name)
    {
        this.name = name;   
    }   

    private string name; 
    //property with public getter and private setter
    public string Name
    {
        get
        {
            return name; 
        }   
        private set 
        { 
            name = value; 
        }
    }

    private static int minimum_age = 13;   

    //read-write property for class member, minimum_age
    public static int MinimumAge
    {
        get
        {
            return minimum_age; 
        }
        set
        {     
            if(value > 0 && value < 100)
                minimum_age = value; 
            else 
                Console.WriteLine("{0} is an invalid age, so minimum age remains at {1}", value, minimum_age);
        }
    }

    public static void Main(string[] args)
    {
        User newuser = new User("Bob Hope"); 

        User.MinimumAge = -5; /* prints error to screen since value invalid */ 
        User.MinimumAge = 18; 
        //newuser.Name = "Kevin Nash"; Causes compiler error since Name property is read-only 

        Console.WriteLine("Minimum Age: " + User.MinimumAge);       
        Console.WriteLine("Name: {0}", newuser.Name);
    }
} // User
```
 
## 6.多维度数组
如下代码演示了多维数组和交错数组的区别
```CSharp
public class ArrayTest 
{
    public static void Main(string[] args)
    {

        int[,] multi = { {0, 1}, {2, 3}, {4, 5}, {6, 7} };  

        for(int i=0, size = multi.GetLength(0); i < size; i++)
        {
            for(int j=0, size2 = multi.GetLength(1); j < size2; j++)
            {
                Console.WriteLine("multi[" + i + "," + j + "] = " + multi[i,j]);
            }

        }


        int[][] jagged = new int[4][];
        jagged[0] = new int[2]{0, 1};
        jagged[1] = new int[2]{2, 3};
        jagged[2] = new int[2]{4, 5};
        jagged[3] = new int[2]{6, 7};

        for(int i=0, size = jagged.Length; i < size; i++)
        {
            for(int j=0, size2 = jagged[1].Length; j < size2; j++)
            {
                Console.WriteLine("jagged[" + i + "][" + j + "] = " + jagged[i][j]);
            }
        }
    }
} // ArrayTest
```
 
## 7.索引器
索引器是重写类[]运算符的语法。如果类包含另外一种对象的话，索引器就很有用。索引器的灵活之处在于支持任何类型，比如整数或字符串、还可以创建索引器允许多维数组语法，可以在索引器中混合和匹配不同的类型，最后，索引器可以重载。

```CSharp
public class IndexerTest: IEnumerable, IEnumerator 
{
    private Hashtable list; 

    public IndexerTest ()
    {
        index = -1; 
        list = new Hashtable();     
    }

    //indexer that indexes by number
    public object this[int column]
    {
        get
        {
            return list[column];
        }
        set
        {
            list[column] = value; 
        }

    }

    /* indexer that indexes by name */ 
    public object this[string name]
    {
        get
        {
            return this[ConvertToInt(name)]; 
        }
        set
        {
            this[ConvertToInt(name)] = value; 
        }
    }

    /* Convert strings to integer equivalents */
    private int ConvertToInt(string value)
    {
        string loVal = value.ToLower(); 
        switch(loVal)
        {  
            case "zero": return 0;
            case "one": return 1;
            case "two": return 2;
            case "three":  return 3;
            case "four": return 4;
            case "five": return 5; 
            default:
                return 0; 
        }
        return 0; 
    }

    /** 
    * Needed to implement IEnumerable interface. 
    */
    public IEnumerator GetEnumerator(){ return (IEnumerator) this; }

    /** 
    * Needed for IEnumerator. 
    */ 
    private int index; 

    /** 
    * Needed for IEnumerator. 
    */ 
    public bool MoveNext()
    {
        index++;
        if(index >= list.Count)
            return false; 
        else
            return true; 
    }

    /** 
    * Needed for IEnumerator. 
    */ 
    public void Reset()
    {
        index = -1; 
    }

    /** 
    * Needed for IEnumerator. 
    */ 
    public object Current
    {
        get
        {
            return list[index];
        }
    }

    public static void Main(string[] args)
    {
        IndexerTest it = new IndexerTest(); 
        it[0] = "A"; 
        it[1] = "B";
        it[2] = "C";
        it[3] = "D"; 
        it[4] = "E";

        Console.WriteLine("Integer Indexing: it[0] = " + it[0]);    
        Console.WriteLine("String  Indexing: it[\"Three\"] = " + it["Three"]);

        Console.WriteLine("Printing entire contents of object via enumerating through indexer :");

        foreach( string str in it)
        {
            Console.WriteLine(str);
        }
    }
} // IndexerTest
```

## 8.预处理指令
C#包含预处理器，相当于C/C++预处理器的有限子集。C#预处理器没有#include文件的能力也没有使用#define进行文本替换的能力。主要的能力在于使用#define和#undef标识符以及通过#if和#elif以及#else选择编译某段代码的能力。#error和#warning指示器可以在编译的时候让指示器之后的错误或警告消息显示出来。#pragma指示器用于处理屏蔽编译器警告消息。最后，#line指示器可以用于编译器发现错误的时候指定源文件行号。

```CSharp
#define DEBUG /* #define must be first token in file */ 
using System; 

#pragma warning disable 169 /* Disable 'field never used' warning */

class PreprocessorTest
{
    int unused_field; 

    public static void Main(string[] args)
    {
        #if DEBUG
        Console.WriteLine("DEBUG Mode := On");
        #else
        Console.WriteLine("DEBUG Mode := Off");
        #endif
    }
}
```

## 9.别名
using关键字可以用于为完全限定名设置别名，和C/C++的typedef相似。如果类的完全限定名需要解决命名空间冲突的话，这就很有用了。
```
using Terminal = System.Console; 
class Test
{
    public static void Main(string[] args)
    {
        Terminal.WriteLine("Terminal.WriteLine is equivalent to System.Console.Writeline"); 
    }
}
```
 
## 10.运行时代码生成
Reflection.Emit命名空间包含了一些类可以用于生成.NET中间语言，以及在运行时在内存中构建类甚至把PE文件写到磁盘上。这类似Java的那些通过生成Java字节码写入磁盘，用于在运行时创建Java类然后被程序使用的类库。Reflection.Enmit命名空间主要的用户是编译器或脚本引擎的作者。比如，System.Text.RegularExpressions使用Reflection.Emit类库来为每一个编译后的表达式生成自定义匹配引擎。

## 11.指针和不安全的代码
尽管C#和Java一样，不能使用指针类型，但是如果C#代码在unsafe上下文中执行的话就可以使用指针类型。如果C#代码在unsafe上下文中执行，那么就会禁止许多运行时检查，程序也必须在所运行的机器上有完全信任权限。写unsafe代码的语法和语义和在C和C++中使用指针的语法和语义相似。写unsafe代码时，必须使用unsafe关键字把代码块指定为unsafe的，并且程序必须使用/unsafe编译器开关编译。由于垃圾回收期可能会在程序执行的过程中重新分配托管变量，所以在fixed代码块中使用托管变量的时候需要使用fixed关键字来固定变量地址。如果没有fixed关键字的话，标记和压缩垃圾回收期可能会在回收的过程中移动变量的地址。
```CSharp
class UnsafeTest
{
    public static unsafe void Swap(int* a, int*b)
    {
        int temp = *a; 
        *a = *b; 
        *b = temp; 
    }

    public static unsafe void Sort(int* array, int size)
    {
        for(int i= 0; i < size - 1; i++)
            for(int j = i + 1; j < size; j++)
            if(array[i] > array[j])
                Swap(&array[i], &array[j]); 
    }

    public static unsafe void Main(string[] args)
    {
        int[] array = {9, 1, 3, 6, 11, 99, 37, 17, 0, 12}; 
        Console.WriteLine("Unsorted Array:"); 
    
        foreach(int x in array)
            Console.Write(x + " "); 

        fixed( int* iptr = array )
        {  // must use fixed to get address of array
            Sort(iptr, array.Length);   
        }//fixed 
    
        Console.WriteLine("\nSorted Array:"); 
    
        foreach(int x in array)
            Console.Write(x + " "); 

    }
}
``` 

## 12.按引用传递
在Java中，传给方法的参数是按值传递的，方法操作的是复制的数据而不是原来的数据。在C#中，可以指定参数按照引用传递而不是数据拷贝。有的时候如果希望方法返回超过一个对象的时候就有用。指定参数按引用传递的关键字是ref和out。区别是使用ref的话传入参数必须初始化，而out则不需要。
```CSharp
class PassByRefTest{

    public static void changeMe(String s){

    s = "Changed"; 

    }

    public static void swap(int x, int y){

    int z = x;
    x = y;
    y = z;

    }

    public static void main(String[] args){

    int a = 5, b = 10; 
    String s = "Unchanged"; 

    swap(a, b); 
    changeMe(s); 

    System.out.println("a := " + a + ", b := " + b + ", s = " + s);
    }
}
```
OUTPUT  
a := 5, b := 10, s = Unchanged  

```CSharp
class PassByRefTest
{
    public static void ChangeMe(out string s)
    {
        s = "Changed"; 
    }

    public static void Swap(ref int x, ref int y)
    {
        int z = x;
        x = y;
        y = z;
    }

    public static void Main(string[] args)
    {
        int a = 5, b = 10; 
        string s; 

        Swap(ref a, ref b); 
        ChangeMe(out s); 

        Console.WriteLine("a := " + a + ", b := " + b + ", s = " + s);
    }
}
```

OUTPUT  
a := 10, b := 5, s = Changed  
 
## 13.逐字字符串
C#提供了一种方式来避免在字符串常量中使用转移序列。唯一的例外是双引号需要使用两个双引号，可以在字符串声明的时候使用@来声明逐字字符串。

```CSharp
class VerbatimTest
{
    public static void Main()
    {
        //verbatim string 
        string filename  = @"C:\My Documents\My Files\File.html"; 
        
        Console.WriteLine("Filename 1: " + filename);

        //regular string 
        string filename2 =  "C:\\My Documents\\My Files\\File.html"; 

        Console.WriteLine("Filename 2: " + filename2);

        string snl_celebrity_jeopardy_skit = @"
            Darrell Hammond (Sean Connery) : I'll take ""Swords"" for $400
            Will Farrell    (Alex Trebek)  : That's S-Words, Mr Connery.
            ";

        Console.WriteLine(snl_celebrity_jeopardy_skit);
    }
}
```

## 14.溢出检查
C#提供了显式检测或忽略溢出条件的选项。溢出条件检测到之后会抛出System.OverflowException。由于溢出检查会带来性能损失，所以需要通过/checked+编译选项显式启用。可以通过在代码块声明checked来表示代码总是进行溢出检查，或是使用unchecked表示总是取消溢出检查。

```CSharp
class CheckedTest
{
    public static void Main()
    {
    int num = 5000; 

    /* OVERFLOW I */
    byte a  = (byte) num; /* overflow detected only if /checked compiler option on */

    /* OVERFLOW II */
    checked
    {       
        byte b = (byte) num; /* overflow ALWAYS detected */     
    }

    /* OVERFLOW III */
    unchecked
    {    
        byte c = (byte) num; /* overflow NEVER detected */      
    }

    }//Main
}
```

## 15.显式接口实现
有的时候在实现接口的时候可能会遇到冲突，比如FileRepresentation类可能会实现IWindow和IFileHandler接口，他妈两个接口都有Close方法，IWindow的Close方法表示关闭GUI窗口，而IFileHandler 的Close方法表示关闭文件。在Java中除了只写一个Close方法之外别无他法，而在C#中可以为每一个接口写一个实现。比如对于之前提到的例子，FileRepresentation类可以有两个不同的Close方法。注意，显式接口方法是private的，并且只有转换成相应类型之后才能进行方法调用。

```CSharp
interface IVehicle
{
    //identify vehicle by model, make, year
    void IdentifySelf();    
}

interface IRobot
{
    //identify robot by name
    void IdentifySelf();
}

class TransformingRobot : IRobot, IVehicle
{
    string model; 
    string make;
    short  year; 
    string name;
    
    TransformingRobot(String name, String model, String make, short year)
    {
        this.name  = name;
        this.model = model; 
        this.make  = make; 
        this.year  = year; 
    }

    
    void IRobot.IdentifySelf()
    {
        Console.WriteLine("My name is " + this.name);
    }

    void IVehicle.IdentifySelf()
    {
        Console.WriteLine("Model:" + this.model + " Make:" + this.make + " Year:" + this.year);
    }

    public static void Main()
    {
        TransformingRobot tr = new TransformingRobot("SedanBot", "Toyota", "Corolla", 2001); 

        // tr.IdentifySelf(); ERROR 

        IVehicle v = (IVehicle) tr; 

        IRobot r   = (IRobot) tr; 

        v.IdentifySelf(); 
    
        r.IdentifySelf(); 
}
}
```
OUTPUT  
Model:Toyota Make:Corolla Year:2001  
My name is SedanBot  
 
## 16.友元程序集
友元程序集特性允许内部类型或内部方法被其它程序集访问。可以使用[InternalsVisibleToAttribute]特性来实现友元程序集。如下代码演示了2个源文件编译成2个程序集来使用友元程序集特性。

```CSharp
// friend_assembly_test.cs
// compile with: /target:library
using System.Runtime.CompilerServices;
using System;

[assembly:InternalsVisibleTo("friend_assembly_test_2")]

// internal by default
class Friend 
{
    public void Hello() 
    {
        Console.WriteLine("Hello World!");
    }
}

// public type with internal member
public class Friend2 
{
internal string secret = "I like jelly doughnuts";  
}

// friend_assembliy_test_2.cs
// compile with: /reference:friend_assembly_test.dll /out:friend_assembly_test_2.exe
using System;

public class FriendFinder 
{
    static void Main() 
    {
        // access an internal type
        Friend f = new Friend();
        f.Hello();

        Friend2 f2 = new Friend2();
        // access an internal member of a public type
        Console.WriteLine(f2.secret);
    }
}
```

## 17.命名空间限定符
项目越大越有可能发生命名空间冲突。C#有::运算符来指定命名空间的作用域。运算符左边的操作数表示的是解决冲突的作用域，右边的操作数表示的是要解决冲突的名字。左操作数可以是关键字global指代全局作用域或是命名空间的别名。

```CSharp
namespace TestLib
{
    class Test
    {

        public class System {} 

        static DateTime Console = DateTime.Now;   

        public static void Main(){
        //Console.WriteLine("Hello world"); doesn't work due to static member variable named 'Console'
        //System.Console.WriteLine("Hello world"); doesn't work due to nested class named 'System'
            
        global::System.Console.WriteLine("The time is " + Console); 

        sys::Console.WriteLine("Hello again"); 
    }
}
```
## 18.迭代器
对于支持foreach循环的数据结构，必须实现或返回System.Collections.IEnumerable的实例。枚举器写起来还是有点麻烦的，yield关键字可以把任何方法或属性转换为枚举器。可以使用yield return语句来一个一个返回内容，可以使用yield break来表示结束了序列。方法或属性必须返回IEnumerable, IEnumerable<T>, IEnumerator 或IEnumerator<T>中的一个。

```CSharp
class Test
{
    public static string[] fruit = {"banana", "apple", "orange", "pear", "grape"};
    public static string[] vegetables = {"lettuce", "cucumber", "peas", "carrots"};

    public static IEnumerable FruitAndVeg
    {
        get
        {  
            foreach(string f in fruit)
            {
                yield return f; 
            }

            foreach(string v in vegetables)
            {
                yield return v; 
            }

            yield break; //optional
        }
    
    }

    public static void Main()
    {
    
        foreach (string produce in Test.FruitAndVeg)
        {
            Console.WriteLine(produce);
        }
    }
}
``` 

## 19.部分类
部分类特性使得我们可以在多个源文件中定义单个类、接口或接口。对于自动生成的代码特别有用。在这个特性出现之前，我们可能会修改自动生成的代码，因为手写代码和自动生成的代码位于一个文件中。而有了这个特性，就减少了出现这种情况的可能。可以通过在类声明中使用partial关键字来启动部分类，如下代码演示了定义在两个源文件中的部分类。注意，一个源文件中的方法和属性可以引用另一个源文件中定义的方法和属性。

    C#：
    partial class Test
    {
        public static string[] fruit = {"banana", "apple", "orange", "pear", "grape"};
        public static string[] vegetables = {"lettuce", "cucumber", "peas", "carrots"};

        public static void Main()
        {
            foreach (string produce in Test.FruitAndVeg)
            {
                Console.WriteLine(produce);
            }
        }
    }

    C#：
    partial class Test
    {
        public static IEnumerable FruitAndVeg
        {
            get
            {
                foreach(string f in fruit)
                {
                    yield return f; 
                }
                foreach(string v in vegetables)
                {
                    yield return v; 
                }
            }
        }
    }

需要注意的是类上的特性会进行合并，因此矛盾的特性是不允许的，比如一个文件中类声明的是private另一个文件声明的是public的。

## 20.可空类型
可空类型System.Nullable类型的实例。可空类型可以表示底层值类型的值也可以表示空值。例如，Nullable<bool>可以表示值true、false和null。可空类型的变量可以使用值的类型加上?运算符来声明。bool?等价于Nullable<bool>。每一个可空类型都有HasValue属性来表示是否具有有效的值或是null。可空类型真实的值保存在其Value属性中。GetValueOrDefault()方法可以返回可空类型的值或如果是null的话返回底层值类型的默认值。

可空类型在用于把C#对象映射到关系型数据库的时候很有用，因为在SQL数据库中可以存在null的有效值。

    C#:
    public class Test
    {
        public static void Main(string[] args)
        {
            int? x = 5; 
            if(x.HasValue)
            {
                Console.WriteLine("The value of x is " + x.Value);
            }

            x = null; 

            //prints 0 
            Console.WriteLine(x.GetValueOrDefault());
        }
    }

??运算符叫做空结合运算符，用于测试可空类型的值，并且在值是空的情况下返回另外一个值。因为x??y等价于x==(nulll ? y : x)。

    C# 
    public class Test
    {
        public static void Main(string[] args)
        {
            int? x = null; 
            int  y = x ?? 5;

            //prints 5 
            Console.WriteLine(y);
        }
    }

## 21.动态类型
C#有个关键字dynamic，用于定义变量。dynamic类型不同寻常之处是，它仅在编译期间存在，在运行期间它会被System.Object类型替代。

    C#：
    public class Coffee
    {
        public string GetName()
        {
            return "You selected Maxwell coffee.";
        }
    }

　　 public class Juice
    {
        public string GetName()
        {
            return "You selected orange juice.";
        }
    }

    class Test
    {
        static private Object GetDrink(int i)
        {
            if (i == 1)
            {
                return new Juice();
            }
            else
            {
                return new Coffee();
            }
        }

        static void Main(string[] args)
        {
            Console.WriteLine("Please Select Your Drink: 1 -- Juice; 2 -- Coffee");
            int nDrinkType = Console.Read();
            dynamic drink = GetDrink( nDrinkType );
            Console.WriteLine( drink.GetName() );
        }
    }

C#编译器允许你通过dynamic对象调用任何方法，即使这个方法根本不存在，编译器也不会在编译的时候报编译错误。只有在运行的时候，它才会检查这个对象的实际类型，并检查在它上面GetName()是什么意思。动态类型将使得C#可以以更加统一而便利的形式表示下列对象：
* 来自动态编程语言——如Python或Ruby——的对象
* 通过IDispatch访问的COM对象
* 通过反射访问的一般.NET类型
* 结构发生过变化的对象——如HTML DOM对象

当我们得到一个动态类型的对象时，不管它是来自COM还是IronPython、HTML DOM还是反射，只需要对其进行操作即可，动态语言运行时(DLR)会帮我们指出针对特定的对象以及这些操作的具体意义。这将给我们的开发带来极大的灵活性，并且能够极大程度上地精简我们的代码。

参考信息：  
[C# 4.0中的动态类型和动态编程](http://blog.csdn.net/tastelife/article/details/7344397)

## 22.异步编程
Async和Await关键字是C#异步编程的核心。通过使用这两个关键字，你可以使用.NET Framework或Windows Runtime的资源创建一个异步方法如同你创建一个同步的方法一样容易。通过使用async和await定义的异步方法，这里被称为异步方法。

    C#：
    public class MyClass
    {
        public MyClass()
        {
            DisplayValue(); //这里不会阻塞
            System.Diagnostics.Debug.WriteLine("MyClass() End.");
        }

        public Task<double> GetValueAsync(double num1, double num2)
        {
            return Task.Run(() =>
            {
                for (int i = 0; i < 1000000; i++)
                {
                    num1 = num1 / num2;
                }
                return num1;
            });
        }

        public async void DisplayValue()
        {
            double result = await GetValueAsync(1234.5, 1.01);//此处会开新线程处理GetValueAsync任务，然后方法马上返回
            //这之后的所有代码都会被封装成委托，在GetValueAsync任务完成时调用
            System.Diagnostics.Debug.WriteLine("Value is : " + result);
        }
    }

参考信息：
[说说C#的async和await](http://blog.csdn.net/tianmuxia/article/details/17675681/)