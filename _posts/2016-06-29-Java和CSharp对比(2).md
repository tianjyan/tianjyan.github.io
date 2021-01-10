---
title: Java和C#对比(2):功能一致但是语法不同的地方
date: 2016-06-29 21:15:58
tags: [Java Vs C#]
categories: [学习笔记]
---

## 1.Main函数
C#和Java函数的入口点都是Main函数，但有两个细微的差别：
* Java中的入口函数是小写的main
* C#中的Main函数可以不具有任何形参

## 2.继承类和实现接口的语法
C#中通过冒号*:*来继承和实现接口，而Java中分别用了*extends*和*implements*。

## 3.运行时类型标识
在运行环境下，C#通过*is*来判断对象类型，而Java通过*instanceof*来判断对象类型。

    C#：
    if(x is MyClass)
    {
        MyClass mc = (MyClass)x;
    }

    Java：
    if(x instanceof MyClass){
        MyClass mc = (MyClass)x;
    }

## 4.命名空间
C#中用namespace代表命名空间，而Java用Package来表示。

    C#：
    namespace N1
    {
        class A{}
    }

    Java：
    package N1;

    class A{}

其中C#支持嵌套的命名空间：

    C#：
    namespace N1
    {
        namespace N2
        {
            class A{}
        }
    }

## 5.构造函数，析构函数和Finalize函数
C#和Java中都有构造函数和Finalize函数，但C#还有析构函数。C#的析构函数会隐式的转换为Finalize，并调用基类的Finalize方法。
在C#和Java中，当垃圾回收器准备好释放对象占用的存储空间，讲首先调用Finalize方法，并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。

    C#：
    public class MyClass 
    {
        static int num_created = 0; 
        int i = 0;    

        MyClass()
        {  
            i = ++num_created; 
            Console.WriteLine("Created object #" + i);    
        }

        ~MyClass()
        {
            Console.WriteLine("Object #" + i + " is being finalized");  
        }

        public static void Main(string[] args)
        {
            for(int i=0; i < 10000; i++)
            new MyClass(); 
        }
    }

    Java：
    public class MyClass {
 
        static int num_created = 0; 
        int i = 0;    

        MyClass(){  
            i = ++num_created; 
            System.out.println("Created object #" + i);    
        }

        public void finalize(){
            System.out.println("Object #" + i + " is being finalized");  
        }

        public static void main(String[] args){

        for(int i=0; i < 10000; i++)
            new MyClass(); 
        }
    }

参考信息：  
Bruce Eckel, 陈昊鹏. Java编程思想[M]. 北京:机械工业出版社, 2016. 87-91  
[析构函数（C# 编程指南）](https://msdn.microsoft.com/zh-cn/library/66x5fx1b.aspx)    

## 6.同步方法和代码块
在Java中可以指定同步块来确保在同一时刻只有一个线程访问某个对象，C#提供了lock语句对应Java的synchronized语句。

    C#：
    public void WithdrawAmount(int num)
    { 
        lock(this)
        { 
            if(num < this.amount)
                this.amount -= num; 
        }
    }

    Java：
    public void withdrawAmount(int num){ 
        synchronized(this){ 
            if(num < this.amount)
                this.amount -= num; 
        }
    }

C#和Java都有同步方法的概念。在调用同步方法的时候，调用方法的线程会锁定包含方法的对象，因此其它线程调用相同对象的同步方法必须等到其它线程执行完方法释放锁之后才能执行。同步方法在Java中用synchronized关键字来标记，而在C#中使用[MethodImpl(MethodImplOptions.Synchronized)]特性来修饰。同步方法的例子如下：

    C#：
    public class BankAccount
    {
        [MethodImpl(MethodImplOptions.Synchronized)]
        public void WithdrawAmount(int num)
        { 
            if(num < this.amount)
                this.amount - num; 
        }
    }

    Java：
    public class BankAccount{
        public synchronized void withdrawAmount(int num){ 
            if(num < this.amount)
                this.amount - num; 
            }
    }

## 7.访问修饰符
如下表格对照C#和Java的访问修饰符。C++爱好者比较失望，在Java2中Sun改变了protected关键字的语义，而C#的protected关键字和C++的一样。也就是说，protected成员只有在类的成员方法或派生类的成员方法中可以访问。internal修饰符表示成员可以被类相同程序集的其它类访问。internal protected修饰符表示的是internal或protected。

<STYLE type="text/css">.javak { COLOR: red } .csharpk { COLOR: blue }</STYLE>
<TABLE border="1"><TBODY><TR><TD class="csharpk">C# access modifier</TD><TD class="javak">Java access modifier</TD></TR><TR><TD class="csharpk">private</TD><TD class="javak">private</TD></TR><TR><TD class="csharpk">public</TD><TD class="javak">public</TD></TR><TR><TD class="csharpk">internal</TD><TD class="javak">protected</TD></TR><TR><TD class="csharpk">protected</TD><TD>N/A</TD></TR><TR><TD class="csharpk">internal protected</TD><TD>N/A</TD></TR></TBODY></TABLE>

_注释：在没有指定访问修饰符的情况下，C#字段或方法的默认访问级别是private，而Java则是protected。_

## 8.反射
在C#和Java中发现类中方法和字段以及运行时调用类方法的能力一般叫做反射。Java和C#中反射的主要区别是，C#的反射是程序集级别的，而Java是类级别的。由于程序集一般保存为DLL，对于C#需要包含类的DLL，而Java需要可以加载类文件或目标类。如下例子遍历某个类的方法，以此比较C#和Java在反射上的差异：

    C#：
    class ReflectionSample 
    {
        public static void Main( string[] args)
        {
            Assembly assembly=null;
            Type type=null;
            XmlDocument doc=null; 

            try
            {
                // 加载程序集以获得类型
                assembly = Assembly.LoadFrom("C:\\WINNT\\Microsoft.NET\\Framework\\v1.0.2914\\System.XML.dll");
                type = assembly.GetType("System.Xml.XmlDocument", true);

                //Unfortunately one cannot dynamically instantiate types via the Type object in C#. 
                doc  =  Activator.CreateInstance("System.Xml","System.Xml.XmlDocument").Unwrap() as XmlDocument;

                if(doc != null)
                        Console.WriteLine(doc.GetType() + " was created at runtime");
                    else
                        Console.WriteLine("Could not dynamically create object at runtime");

            }
            catch(FileNotFoundException)
            {
                Console.WriteLine("Could not load Assembly: system.xml.dll");
                return;
            }
            catch(TypeLoadException)
            {
                Console.WriteLine("Could not load Type: System.Xml.XmlDocument from assembly: system.xml.dll");
                return;
            }
            catch(MissingMethodException)
            {
                Console.WriteLine("Cannot find default constructor of " + type);
            }
            catch(MemberAccessException)
            {
                Console.WriteLine("Could not create new XmlDocument instance");
            }
            
            // 获得方法
            MethodInfo[] methods = type.GetMethods();
                
            //打印方法签名和参数
            for(int i=0; i < methods.Length; i++)
            {
                Console.WriteLine ("{0}", methods[i]);
                
                ParameterInfo[] parameters = methods[i].GetParameters(); 
                
                for(int j=0; j < parameters.Length; j++)
                {   
                    Console.WriteLine (" Parameter: {0} {1}", parameters[j].ParameterType, parameters[j].Name);
                }   
            }
        }
    }

    Java：
    class ReflectionTest {
        public static void main(String[] args) {  
            Class c=null; 
            Document d;

            try{
                    c =  DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument().getClass();
                    d =  (Document) c.newInstance();      
                    System.out.println(d + " was created at runtime from its Class object");
                }catch(ParserConfigurationException pce){
                    System.out.println("No document builder exists that can satisfy the requested configuration");
                }catch(InstantiationException ie){
                    System.out.println("Could not create new Document instance");
                }catch(IllegalAccessException iae){
                    System.out.println("Cannot access default constructor of " + c);
                }

                // Get the methods from the class
                Method[] methods = c.getMethods();

                //print the method signatures and parameters
                for (int i = 0; i < methods.length; i++) {

                    System.out.println( methods[i]);
                    
                    Class[] parameters = methods[i].getParameterTypes();
                    
                    for (int j = 0; j < parameters.length; j++) {         
                        System.out.println("Parameters: " +  parameters[j].getName());
                    }
                }
            }
        }
 

上面的代码可以看到C#的反射API略微优雅一点，C#提供了ParameterInfo包含方法的参数元数据，而Java提供的只是Class对象丢失了诸如参数名等信息。

有的时候需要获取指定类元数据对象，那么可以使用Java的java.lang.Class或C#的System.Type对象。要从类的实例获取元数据，在Java中可以使用getClass()方法而在C#中可以使用GetType()方法。如果要根据名字而不是创建一个类的实例来获取元数据可以这么做：

    C#： 
    Type t = typeof(ArrayList);

    Java：
    Class c = java.util.Arraylist.class; /* 必须在类的完整名字后跟 .class */

## 9.声明常量
在Java中要声明常量可以使用final关键字。final的变量可以在编译时或运行时进行设置。在Java中，如果在基元上使用final的话基元的值不可变，如果在对象引用上使用final的话，则引用只可以指向一个对象。final的成员可以在声明的时候不初始化，但是必须要构造方法中初始化。

在C#中要声明常量使用const关键字来表示编译时常量，使用readonly关键字来表示运行时常量。基元常量和引用常量的语义对于C#和Java来说是一样的。

和C++不同的是，C#和Java不能通过语言结构来指定不可变的类。

    C#：
    public class ConstantTest
    {
        /* 编译时常量*/ 
        const int i1 = 10;   //隐含表示这是static的变量
        
        // 下面的代码不能通过编译
        // public static const int i2 = 20; 

        /* 运行时常量 */
        public static readonly uint l1 =  (uint) DateTime.Now.Ticks;
    
        /* 对象引用作为常量 */ 
        readonly Object o = new Object();
    
        /* 未初始化的只读变量 */ 
        readonly float f; 

        ConstantTest() 
        {
            // 未初始化的只读变量必须在构造方法中初始化
            f = 17.21f; 
        }
    }

    Java：
    public class ConstantTest{
        /* Compile time constants */ 
        final int i1 = 10;   //instance variable 
        static final int i2 = 20; //class variable 

        /* run time constants */
        public static final long l1 = new Date().getTime();
    
        /* object reference as constant */ 
        final Vector v = new Vector();
    
        /* uninitialized final */ 
        final float f; 

    
        ConstantTest() {
            // unitialized final variable must be initialized in constructor 
            f = 17.21f; 
        }
    }

_注释：Java语言还支持方法上的final参数。在C#中没有这个功能。final参数只要用于允许传入方法的参数可以让方法内的内部类进行访问。_

## 10.基元类型
对于Java中的每一个基元类型在C#中都有同名的对应（除了byte之外）。Java中的byte是有符号的，等价于C#中的sbyte，不同于C#的byte。C#还提供了一些基元类型的无符号版本，比如ulong、uint、ushort和byte。C#中最不同的基元类型是decimal类型，不会有舍入错误，当然也就需要更多空间也更慢。

    C#:
    decimal dec = 100.44m; //m is the suffix used to specify decimal numbers
    double  dbl = 1.44e2d; //e is used to specify exponential notation while d is the suffix used for doubles

## 11.数组声明
Java有两种方式声明数组。一种方式为了兼容C/C++的写法，另外一种更具有可读性，C#只能使用后者。

    C#：
    int[] iArray = new int[100]; //valid, iArray is an object of type int[] 
    float fArray[] = new float[100]; //ERROR: Won't compile  

    Java：
    int[] iArray = new int[100]; //valid, iArray is an object of type int[] 
    float fArray[] = new float[100]; //valid, but isn't clear that fArray is an object of type float[] 

## 12.调用基类构造方法和构造方法链
C#和Java自动调用基类构造方法，并且提供了一种方式可以调用基类的有参构造方法。两种语言都要求派生类的构造方法在任何初始化之前先调用基类的构造方法防止使用没有初始化的成员。两种语言还提供了在一个构造方法中调用另一个构造方法的方式以减少构造方法中代码的重复。这种方式叫做构造方法链：

    C#：
    class MyException: Exception
    {
        private int Id; 

        public MyException(string message): this(message, null, 100){ }

        public MyException(string message, Exception innerException): 
            this(message, innerException, 100){ }

        public MyException(string message, Exception innerException, int id) : base(message, innerException)
        {
            this.Id = id;   
        }
    }
  
    Java：
    class MyException extends Exception{

        private int Id; 
        
        public MyException(String message){
            this(message, null, 100); 
        }

        public MyException(String message, Exception innerException){
            this(message, innerException, 100); 
        }

        public MyException( String message,Exception innerException, int id){
            super(message, innerException); 
            this.Id = id;     
        }
    }
 
## 13.可变长度参数列表
在C和C++中可以指定函数接收一组参数。在printf和scanf类似的函数中大量使用这种特性。C#和Java允许我们定义一个参数接收可变数量的参数。在C#中可以在方法的最后一个参数上使用params关键字以及一个数组参数来实现，在Java中可以为类型名通过增加三个.来实现。

    C#：
    class ParamsTest
    {
        public static void PrintInts(string title, params int[] args)
        {
            Console.WriteLine(title + ":");

            foreach(int num in args)
            {
                Console.WriteLine(num); 
            }
        }

        public static void Main(string[] args)
        {
            PrintInts("First Ten Numbers in Fibonacci Sequence", 0, 1, 1, 2, 3, 5, 8, 13, 21, 34);
        }
    }

    Java：
    class Test{

        public static void PrintInts(String title, Integer... args){

        System.out.println(title + ":");

        for(int num : args)
            System.out.println(num); 

        }

        public static void main(String[] args){

        PrintInts("First Ten Numbers in Fibonacci Sequence", 0, 1, 1, 2, 3, 5, 8, 13, 21, 34);
        }
    }
 
## 14.泛型
C#和Java都提供了创建强类型数据结构且不需要在编译时知道具体类型的机制。在泛型机制出现以前，这个特性通过在数据结构中指定object类型并且在运行时转换成具体类型来实现。但是这种技术有许多缺点，包括缺乏类型安全、性能不佳以及代码膨胀。

如下代码演示了两种方式：

    C#：
    class Test
    {
        public static Stack GetStackB4Generics()
        {
            Stack s = new Stack(); 
            s.Push(2);
            s.Push(4); 
            s.Push(5);

            return s;
        }

        public static Stack<int> GetStackAfterGenerics()
        {
            Stack<int> s = new Stack<int>(); 
            s.Push(12);
            s.Push(14); 
            s.Push(50);

            return s;
        }

        public static void Main(String[] args)
        {
            Stack s1 = GetStackB4Generics(); 
            int sum1 = 0; 
            
            while(s1.Count != 0)
            {
                sum1 += (int) s1.Pop(); //cast 
            }
        
            Console.WriteLine("Sum of stack 1 is " + sum1); 

            Stack<int> s2 = GetStackAfterGenerics(); 
            int sum2 = 0; 
        
            while(s2.Count != 0)
            {
                sum2 += s2.Pop(); //no cast
            }
        
            Console.WriteLine("Sum of stack 2 is " + sum2); 
        }
    }

    Java：
    class Test{
        public static Stack GetStackB4Generics(){
            Stack s = new Stack(); 
            s.push(2);
            s.push(4); 
            s.push(5);

            return s;
        }

        public static Stack<Integer> GetStackAfterGenerics(){
            Stack<Integer> s = new Stack<Integer>(); 
            s.push(12);
            s.push(14); 
            s.push(50);

            return s;
        }

        public static void main(String[] args){

            Stack s1 = GetStackB4Generics(); 
            int sum1 = 0; 
            
            while(!s1.empty()){
                sum1 += (Integer) s1.pop(); //cast 
            }
        
            System.out.println("Sum of stack 1 is " + sum1); 

            Stack<Integer> s2 = GetStackAfterGenerics(); 
            int sum2 = 0; 
            
            while(!s2.empty()){
                sum2 += s2.pop(); //no cast
            }
            
            System.out.println("Sum of stack 2 is " + sum2); 
        }
    }

尽管和C++中的模板概念相似，C#和Java中的泛型特性实现上不一样。在Java中，泛型功能使用类型擦除来实现。泛型信息只是在编译的时候出现，编译后被编译器擦除所有类型声明替换为Object。编译器自动在核实的地方插入转换语句。这么做的原因是，泛型代码和不支持泛型的遗留代码可以互操作。类型擦除的泛型类型的主要问题是，泛型类型信息在运行时通过反射或运行时类型标识不可用。并且对于这种方式，泛型类型数据结构必须使用对象和非基元类型进行生命。所以只能有Stack<Integer>而不是Stack<int>。

在C#中，.NET运行时中间语言IL直接支持泛型。泛型在编译的时候，生成的IL包含具体类型的占位符。在运行的时候，如果初始化一个泛型类型的引用，系统会看是否有人已经用过这个类型了，如果类型之前请求过则返回之前生成的具体类型，如果没有JIT编译器把泛型参数替换为IL中的具体类型然后再初始化新的类型。如果请求的类型是引用类型而不是值类型的话，泛型类型参数会替换为Object，但是不需要进行转换，因为.NET运行时会在访问的时候内部进行转换。

在某些时候，我们的方法需要操作包含任意类型的数据结构而不是某个具体类型，但是又希望利用强类型泛型的优势。这个时候我们可以通过C#的泛型类型推断或Java通配类型实现。如下代码所示：

    C#：
    class Test
    {
        //Prints the contents of any generic Stack by 
        //using generic type inference 
        public static void PrintStackContents<T>(Stack<T> s)
        {
            while(s.Count != 0)
            {
                Console.WriteLine(s.Pop()); 
            }    
        }

        public static void Main(String[] args)
        {
            Stack<int> s2 = new Stack<int>(); 
            s2.Push(4); 
            s2.Push(5); 
            s2.Push(6); 

            PrintStackContents(s2);     
            
            Stack<string> s1 = new Stack<string>(); 
            s1.Push("One"); 
            s1.Push("Two"); 
            s1.Push("Three"); 

            PrintStackContents(s1); 
        }
    }

    Java：
    class Test{

        //Prints the contents of any generic Stack by 
        //specifying wildcard type 
        public static void PrintStackContents(Stack<?> s){
            while(!s.empty()){
                System.out.println(s.pop()); 
            }    
        }

        public static void main(String[] args){

            Stack <Integer> s2 = new Stack <Integer>(); 
            s2.push(4); 
            s2.push(5); 
            s2.push(6); 

            PrintStackContents(s2);     
            
            Stack<String> s1 = new Stack<String>(); 
            s1.push("One"); 
            s1.push("Two"); 
            s1.push("Three"); 

            PrintStackContents(s1); 
        }
    }

C#和Java都提供了泛型约束。  

对于C#有三种类型的约束：
* 1.派生约束，告诉编译器泛型类型参数需要从某个基类型继承，比如接口或基类
* 2.默认构造方法约束，告诉编译器泛型类型参数需要提供公共的默认构造方法
* 3.引用、值类型约束，泛型类型参数需要是引用类型或值类型

对于Java支持派生约束：

    C#：
    public class Mammal 
    {
        public Mammal(){}
        public virtual void Speak(){} 
    }

    public class Cat : Mammal
    {
        public Cat(){}
        public override void Speak()
        {
            Console.WriteLine("Meow");
        }
    }

    public class Dog : Mammal
    {
        public Dog(){}
        public override void Speak()
        {
            Console.WriteLine("Woof");
        } 
    }

    public class MammalHelper<T> where T: Mammal /* derivation constraint */,
                                    new() /* default constructor constraint */{ 
        public static T CreatePet()
        {
            return new T(); 
        }

        public static void AnnoyNeighbors(Stack<T> pets)
        {
            while(pets.Count != 0)
            {
                Mammal m = pets.Pop(); 
                m.Speak();  
            }    
        }  
    }

    public class Test
    {
        public static void Main(String[] args)
        {
            Stack<Mammal> s2 = new Stack<Mammal>(); 
            s2.Push(MammalHelper<Dog>.CreatePet()); 
            s2.Push(MammalHelper<Cat>.CreatePet()); 

            MammalHelper<Mammal>.AnnoyNeighbors(s2);         
        }
    }

    Java：
    abstract class Mammal {
        public abstract void speak(); 
    }

    class Cat extends Mammal{
        public void speak(){
            System.out.println("Meow");
        }
    }

    class Dog extends Mammal{
        public void speak(){
            System.out.println("Woof");
        } 
    }

    public class Test{
        
        //derivation constraint applied to pets parameter
        public static void AnnoyNeighbors(Stack<? extends Mammal> pets){
            while(!pets.empty()){
                Mammal m = pets.pop(); 
                m.speak();  
            }    
        }

        public static void main(String[] args){

            Stack<Mammal> s2 = new Stack<Mammal>(); 
            s2.push(new Dog()); 
            s2.push(new Cat()); 

            AnnoyNeighbors(s2);         
        }
    }

C#还提供了default操作符可以返回类型的默认值。引用类型的默认值是null，值类型（比如int、枚举和结构）的默认值是0（0填充结构）。对于泛型default很有用，如下代码演示了这个操作符的作用：

    C# ：
    public class Test
    {
        public static T GetDefaultForType<T>()
        {
            return default(T); //return default value of type T
        }

        public static void Main(String[] args)
        {    
            Console.WriteLine(GetDefaultForType<int>());
            Console.WriteLine(GetDefaultForType<string>());    
            Console.WriteLine(GetDefaultForType<float>());
        }
    }
输出：  
0  
  
0
 
## 15.for-each循环
for-each循环是很多脚本语言、编译工具、方法类库中非常常见的一种迭代结构。for-each循环简化了C#中实现System.Collections.IEnumerable接口或Java中java.lang.Iterable接口数组或类的迭代。

在C#中通过foreach关键字来创建for-each循环，而在Java中通过操作符:来实现。

    C#：
    string[] greek_alphabet = {"alpha", "beta", "gamma", "delta", "epsilon"};
    foreach(string str in greek_alphabet)
    Console.WriteLine(str + " is a letter of the greek alphabet");

    Java：
    String[] greek_alphabet = {"alpha", "beta", "gamma", "delta", "epsilon"};
    for(String str : greek_alphabet)
    System.out.println(str + " is a letter of the greek alphabet");
 

## 16.元数据注解
元数据注解提供了一种强大的方式来扩展编程语言和语言运行时的能力。注解是可以要求运行时进行一些额外任务、提供类额外信息扩展功能的一些指令。元数据注解在许多编程环境中很常见，比如微软的COM和linux内核。

C#特性提供了为模块、类型、方法或成员变量增加注解的方式。如下描述了一些.NET自带的特性以及如何使用它们来扩展C#的能力：
* 1.[MethodImpl(MethodImplOptions.Synchronized)] 用于指定多线程访问方法的时候使用锁进行保护，和Java的sychronized一样。
* 2.[Serializable]用于把类标记为可序列化的，和Java的实现Serializable接口相似。
* 3.[FlagsAttribute]用于指定枚举支持位操作。这样枚举就可以有多个值。

        C#：
        //declaration of bit field enumeration 
        [Flags]
        enum ProgrammingLanguages{ 
            C     = 1, 
            Lisp  = 2, 
            Basic = 4, 
            All  = C | Lisp | Basic
        }

        aProgrammer.KnownLanguages  =  ProgrammingLanguages.Lisp; //set known languages  ="Lisp"
        aProgrammer.KnownLanguages |=  ProgrammingLanguages.C; //set known languages  ="Lisp C"
        aProgrammer.KnownLanguages &= ~ProgrammingLanguages.Lisp; //set known languages  ="C"

        if((aProgrammer.KnownLanguages & ProgrammingLanguages.C) > 0)
        {
            //if programmer knows C
            //.. do something 
        }

* 4.[WebMethod]在ASP.NET中用于指定方法可以通过Web服务访问。

可以通过反射来访问模块、类、方法或字段的特性（详见[MSDN](http://msdn.microsoft.com/zh-cn/library/system.attributetargets.aspx)）。对于运行时获取类是否支持某个行为特别有用，开发者可以通过继承System.Attribute类来创建他们自己的特性。如下是使用特性来提供类作者信息的例子，然后我们通过反射来读取这个信息。

    C#：
    [AttributeUsage(AttributeTargets.Class)]
    public class AuthorInfoAttribute: System.Attribute
    {
        string author; 
        string email; 
        string version;

        public AuthorInfoAttribute(string author, string email)
        {
            this.author = author; 
            this.email  = email;
        }

        public string Version
        {
            get
            {
                return version;
            }
            set
            {
                version = value; 
            }
        }

        public string Email
        {
            get
            {
                return email;
            }
        }

        public string Author
        {
            get
            {
                return author;
            }
        }
    }

    [AuthorInfo("Dare Obasanjo", "kpako@yahoo.com", Version="1.0")]
    class HelloWorld
    {

    }

    class AttributeTest
    {
        public static void Main(string[] args)
        {
            /* Get Type object of HelloWorld class */      
            Type t = typeof(HelloWorld); 

            Console.WriteLine("Author Information for " + t);
            Console.WriteLine("=================================");

            foreach(AuthorInfoAttribute att in t.GetCustomAttributes(typeof(AuthorInfoAttribute), false))
            {
                Console.WriteLine("Author: " + att.Author);
                Console.WriteLine("Email: " + att.Email);
                Console.WriteLine("Version: " + att.Version);   
            }
        }
    }
 

Java的提供了为包、类型、方法、参数、成员或局部变量增加注解的能力。Java语言只内置了三种注解，如下：
* 1.@Override用于指定方法覆盖基类的方法。如果注解的方法并没有覆盖基类的方法，在编译的时候会出错。
* 2.@Deprecated用于指示某个方法已经废弃。使用废弃的方法会在编译的时候产生警告。
* 3.@SuppressWarnings用于防止编译器发出某个警告。这个注解可选接收参数来表明屏蔽哪种警告。

和C#一样，可以通过反射来读取模块、类、方法或字段的注解。但是不同的是Java注解可以是元注解。开发者可以创建自定义的注解，创建方法和接口相似，只不过需要定义@interface关键字。如下是使用注解和通过反射读取信息的例子：

    Java：
    @Documented //we want the annotation to show up in the Javadocs 
    @Retention(RetentionPolicy.RUNTIME) //we want annotation metadata to be exposed at runtime
    @interface AuthorInfo{
        String author(); 
        String email(); 
        String version() default "1.0";
    }

    @AuthorInfo(author="Dare Obasanjo", email="kpako@yahoo.com")
    class HelloWorld{

    }

    public class Test{

        public static void main(String[] args) throws Exception{

            /* Get Class object of HelloWorld class */      
            Class c = Class.forName("HelloWorld");
            AuthorInfo a = (AuthorInfo) c.getAnnotation(AuthorInfo.class); 
            
            System.out.println("Author Information for " + c);
            System.out.println("=======================================");
            System.out.println("Author: " + a.author());
            System.out.println("Email: " + a.email());
            System.out.println("Version: " + a.version());      

        }
    }    

## 17.枚举
枚举用于创建一组用于自定义的命名常量。尽管从表面上看C#和Java的枚举非常相同，但是在实现上它们完全不同。Java的枚举类型是纯种的类，也就是它们是类型安全并可以增加方法、字段或甚至实现接口进行扩展，而在C#中枚举纯粹是一个包装了数字类型（一般是int）的语法糖，并且不是类型安全的。如下代码演示了两者的区别：

    C#：
    public enum DaysOfWeek
    {
        SUNDAY, 
        MONDAY, 
        TUESDAY, 
        WEDNESDAY,
        THURSDAY, 
        FRIDAY,
        SATURDAY
    }

    public class Test
    {

        public static bool isWeekDay(DaysOfWeek day)
        {
            return !isWeekEnd(day);   
        }

        public static bool isWeekEnd(DaysOfWeek day)
        {
            return (day == DaysOfWeek.SUNDAY || day == DaysOfWeek.SATURDAY); 
        }

        public static void Main(String[] args)
        {
            DaysOfWeek sun = DaysOfWeek.SUNDAY; 
            Console.WriteLine("Is " + sun + " a weekend? " + isWeekEnd(sun));
            Console.WriteLine("Is " + sun + " a week day? " + isWeekDay(sun));

            /* Example of how C# enums are not type safe */
            sun = (DaysOfWeek) 1999;
            Console.WriteLine(sun);
        }
    }
 
    Java：
    enum DaysOfWeek{
        SUNDAY, 
        MONDAY, 
        TUESDAY, 
        WEDNESDAY,
        THURSDAY, 
        FRIDAY,
        SATURDAY;

        public boolean isWeekDay(){
            return !isWeekEnd();   
        }

        public boolean isWeekEnd(){
            return (this == SUNDAY || this == SATURDAY); 
        }

    }

    public class Test{

        public static void main(String[] args) throws Exception{

            DaysOfWeek sun = DaysOfWeek.SUNDAY; 
            System.out.println("Is " + sun + " a weekend? " + sun.isWeekEnd());
            System.out.println("Is " + sun + " a week day? " + sun.isWeekDay());
        }
    }

运行结果：  
Is SUNDAY a weekend? true   
Is SUNDAY a week day? false  


## 18.函数式接口与委托
在Java中，包装一个方法的调用，需要创建一个接口类型和相应的实现类型，在实现中调用需要包装的方法，如果需要调用的是实例方法，还需要将实例的引用传递进接口实现的实例中（后面再比较闭包）。这种实现方式的好处是不需要引入更多的语法概念，可以保持语言的精简，学习曲线平缓。缺点是代码量多，不能清晰的区分和表达“这是一个包装了方法的对象”这种概念，同时接口的定义多样，实例的使用者需要了解接口的细节才能很好地使用。
在C#中，有一种特殊的引用类型叫做委托(Delegate)，专门用于表达方法的引用。所有的委托类型都继承于System.Delegate，同时，拥有一组特殊的语法，使得委托的定义、实例化、调用十分简单和极具语义。委托的调用可以像调用普通方法一样，使用者不需要了解委托的内在实现。

    C#：
    class Test
    {
        delegate TResult Func<T, TResult>(T arg);

        public static void Main(string[] args)
        {
            Func<String, String> doWork = DoWork;
            String result = doWork("C#");
            Console.WriteLine(result);
        }

        private static string DoWork(String arg)
        {
            return "Done " + arg;
        }
    }

    Java：
    class Main {
        @FunctionalInterface
        public interface Func<T, TResult>{
            TResult invoke(T arg);
        }

        public static void main(String[] args) {
            Func<String, String> doWork = Main::DoWork;
            String result = doWork.invoke("Java");
            System.out.printf(result);
        }

        private static String DoWork(String arg){
            return "Done " + arg;
        }
    }


## 19.Lambda表达式
Java和C#都支持Lambda表达式，其中Java用"->"表示Lambda表达式，而C#中用"=>"表示。

    C#：
    class Test
    {
        public static void Main(string[] args)
        {
          List<int> nums = new List<int> { 1, 2, 3, 4, 5 };
          Parallel.ForEach(nums, (value) =>
          {
            Console.WriteLine(value);
          });
        }
    }

    Java：
    class Test{
      public static void main(String[] args){
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
        numbers.forEach((Integer value) -> System.out.println(value));  
      }
    }


## 20.Streams和Linq
事实上.NET4内置提供了多种类似的集合操作方法（这些方法都是通过扩展方法（C#3新特性）添加的，可以在不修改类定义的情况下为类添加类似于实例方法的效果）。利用这些方法和lambda表达式，可以为集合进行多种操作。

    C#：
    var result = Enumerable.Range(0, 100)//遍历1到100
        .Where(n => n % 2 == 0)//筛选能被2整除的数
        .Where(n => n % 3 == 0)//筛选能被2整除的数
        .Select(n => n.ToString())//转换成字符串
        .Reverse();//反转顺序

通过这样的链式编程，可以表达各种数据集合操作，而这些都只是操作定义，真正的数据操作在使用时才进行，达到了延时操作效果。
更进一步，C#实现了linq，一种语言级查询语言，将查询简化到了极致，达到了类似于SQL的效果。
上面的查询可以写成：

    C#：
    var reuslt = from n in Enumerable.Range(0, 100) where n % 2 == 0 && n % 3 == 0 select n.ToString();

用一行语句表达了纯粹的查询，不存在任何方法、委托的痕迹。Java8中添加java.util.Stream包，用于实现类似于C#链式查询的效果。

    Java：
    List<String> stringCollection = null;
    String[] result = stringCollection.stream()
        .filter(s -> s.startsWith("a"))
        .map(s -> s.toString())
        .toArray();

参考信息：  
[Java8的新特性以及与C#的比较](http://www.cnblogs.com/wisdomqq/p/3618544.html)