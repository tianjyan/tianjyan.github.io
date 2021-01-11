---
layout: post
title: Java和C#对比(3):C#中有，Java中也有但完全不同的地方
date: 2016-06-30 21:15:58
tags: [Java Vs C#]
categories: [学习笔记]
---
## 1.嵌套类
在Java和C#中可以在一个类中嵌套另一个。在Java中有两种类型的嵌套类，非静态的嵌套类也就是内部类，以及静态的嵌套类。Java的内部类可以认为是内部类和其包含类一对一的关系，每一个包含类的实例都保存了一个相应内部类的实例，内部类可以额访问包含类的成员变量以及包含非静态的方法。Java的静态内部类可以访问包含类的静态成员和方法。C#也有Java的静态嵌套类，但是没有Java的内部类。如下嵌套类声明是等价的：

    C#：
    public class Car
    {
        private Engine engine;

        private class Engine
        {
            string make; 
        }
    }

    Java：
    public class Car{

        private Engine engine;

        private static class Engine{
            String make; 
        }
    }

_注释：在Java中，嵌套类可以在任何代码块中声明，包括方法，在C#中则不能。在方法中创建嵌套类看上去不必要，但是结合匿名内部类可以提供强大的设计模式。_

## 2.线程和易失成员
线程是程序内的顺序控制流程。程序或进程可以有多个线程并行执行，在执行任务的时候可以共享数据也可以独立运行。这样开发人员就可以在一个程序或进程中一次执行多个任务。线程的优点包括充分利用多处理器架构的资源、通过一边处理任务一边等待阻塞的系统调用（比如打印机或其它IO）减少执行时间，避免GUI应用程序失去响应。

Java线程可以通过继承java.lang.Thread并重写run()方法或者实现java.lang.Runable接口并实现run()方法来实现。在C#中，可以创建System.Threading.Thread对象并且传入System.Threading.Thread委托来表示需要线程运行的方法。在Java中，每一个类都从java.lang.Object继承wait()、notify()以及notify()。在C#中的等价是Thread.Threading.Montir类的Wait()、Pulse()以及PulseAll()。

    C#：
    public class WorkerThread
    {

        private int idNumber;
        private static int num_threads_made = 1;
        private ThreadSample owner;

        public WorkerThread(ThreadSample owner)
        {
            idNumber = num_threads_made;
            num_threads_made++;
            this.owner = owner;
        }/* WorkerThread() */

        //sleeps for a random amount of time to simulate working on a task
        public void PerformTask()
        {
            Random r = new Random((int)DateTime.Now.Ticks);
            int timeout = (int)r.Next() % 1000;

            if (timeout < 0)
                timeout *= -1;

            //Console.WriteLine(idNumber + ":A");

            try
            {
                Thread.Sleep(timeout);
            }
            catch (ThreadInterruptedException e)
            {
                Console.WriteLine("Thread #" + idNumber + " interrupted");
            }

            //Console.WriteLine(idNumber + ":B");

            owner.workCompleted(this);

        }/* performTask() */
        public int getIDNumber() { return idNumber; }
    } // WorkerThread

    public class ThreadSample
    {
        private static Mutex m = new Mutex();
        private ArrayList threadOrderList = new ArrayList();

        private int NextInLine()
        {
            return (int)threadOrderList[0];
        }

        private void RemoveNextInLine()
        {
            threadOrderList.RemoveAt(0);

            //all threads have shown up 
            if (threadOrderList.Count == 0)
                Environment.Exit(0);
        }

        public void workCompleted(WorkerThread worker)
        {
            try
            {
                lock (this)
                {
                    while (worker.getIDNumber() != NextInLine())
                    {
                        try
                        {
                            //wait for some other thread to finish working
                            Console.WriteLine("Thread #" + worker.getIDNumber() + " is waiting for Thread #" +
                                    NextInLine() + " to show up.");

                            Monitor.Wait(this, Timeout.Infinite);
                        }
                        catch (ThreadInterruptedException e) { }
                    }//while

                    Console.WriteLine("Thread #" + worker.getIDNumber() + " is home free");

                    //remove this ID number from the list of threads yet to be seen
                    RemoveNextInLine();

                    //tell the other threads to resume
                    Monitor.PulseAll(this);
                }
            }
            catch (SynchronizationLockException) { Console.WriteLine("SynchronizationLockException occurred"); }
        }

        public static void Main(String[] args)
        {
            ThreadSample ts = new ThreadSample();

            /* Launch 25 threads */
            for (int i = 1; i <= 25; i++)
            {
                WorkerThread wt = new WorkerThread(ts);
                ts.threadOrderList.Add(i);
                Thread t = new Thread(new ThreadStart(wt.PerformTask));
                t.Start();
            }

            Thread.Sleep(3600000); //wait for it all to end

        }/* main(String[]) */
    }

    Java：
    class WorkerThread extends Thread{
        
        private Integer idNumber;
        private static int num_threads_made = 1; 
        private ThreadSample owner; 

        public WorkerThread(ThreadSample owner){
            
            super("Thread #" + num_threads_made);   
        
            idNumber = new Integer(num_threads_made);   
        num_threads_made++;        
        this.owner = owner;
        
        start(); //calls run and starts the thread.
        }/* WorkerThread() */
        
        //sleeps for a random amount of time to simulate working on a task
        public void run(){

        Random r = new Random(System.currentTimeMillis()); 
        int timeout = r.nextInt()  % 1000; 

        if(timeout < 0)
            timeout *= -1 ;

        try{        
            Thread.sleep(timeout);      
            } catch (InterruptedException e){
                System.out.println("Thread #" + idNumber + " interrupted");
            }
        
        owner.workCompleted(this); 

        }/* run() */
        
            
        public Integer getIDNumber() {return idNumber;}
            
        
    } // WorkerThread

    public class ThreadSample{

        private Vector threadOrderList = new Vector(); 
        
        private Integer nextInLine(){

        return (Integer) threadOrderList.firstElement(); 
        }

        private void removeNextInLine(){

        threadOrderList.removeElementAt(0);

        //all threads have shown up 
        if(threadOrderList.isEmpty())
            System.exit(0); 
        }

        public synchronized void workCompleted(WorkerThread worker){

        
        while(worker.getIDNumber().equals(nextInLine())==false){
        
            try {
            //wait for some other thread to finish working
            System.out.println (Thread.currentThread().getName() + " is waiting for Thread #" + 
                        nextInLine() + " to show up.");
            wait();
            } catch (InterruptedException e) {}
            
        }//while
        
        System.out.println("Thread #" + worker.getIDNumber() + " is home free");

        //remove this ID number from the list of threads yet to be seen
        removeNextInLine(); 

        //tell the other threads to resume
        notifyAll(); 
        }

        public static void main(String[] args) throws InterruptedException{
        

        ThreadSample ts = new ThreadSample(); 

        /* Launch 25 threads */ 
        for(int i=1; i <= 25; i++){
            new WorkerThread(ts); 
            ts.threadOrderList.add(new Integer(i)); 
        }

        Thread.sleep(3600000); //wait for it all to end
        
        }/* main(String[]) */ 


    }//ThreadSample
 
在许多情况下，我们不能确保代码执行的顺序和源代码一致。产生这种情况的原因包括编译器优化的时候重排语句顺序、或多处理器系统不能在全局内存中保存变量。要避免这个问题，C#和Javay引入了volatile关键字来告诉语言运行时不要重新调整字段的指令顺序。

    C#：
    /* Used to lazily instantiate a singleton class */ 
    /*             WORKS AS EXPECTED                */
    class Foo {
            private volatile Helper helper = null;
            public Helper getHelper() {
                if (helper == null) {
                    lock(this) {
                        if (helper == null)
                            helper = new Helper();
                    }
                }
                return helper;
            }
        }

    Java：
    /* Used to lazily instantiate a singleton class */ 
    /* BROKEN UNDER CURRENT SEMANTICS FOR VOLATILE */ 
    class Foo {
        private volatile Helper helper = null;
        public Helper getHelper() {
            if (helper == null) {
                synchronized(this) {
                    if (helper == null)
                        helper = new Helper();
                }
            }
            return helper;
        }
    }

尽管上面的代码除了lock和synchronized关键字之外没什么不同，Java的版本不保证在所有的JVM下都工作，详见[http://www.javaworld.com/javaworld/jw-02-2001/jw-0209-double.html](http://www.javaworld.com/javaworld/jw-02-2001/jw-0209-double.html)。在C#中volatile的语义不会出现这样的问题，因为读写次序不能调整，同样被标记volatile的字段不会保存在寄存器中，对于多处理器系统确保变量保存在全局内存中。

## 3.操作符重载

操作符重载允许特定的类或类型对于标准操作符具有新的语义。操作符重载可以用于简化某个常用操作的语法，比如Java中的字符串连接。操作符重载也是开发人员争议的一个地方，它在带来灵活性的同时也带来了滥用的危险。有些开发者会乱用重载（比如用++或--来表示连接或断开网络）或是让操作符不具有本来的意义（比如[]返回一个集合中某个索引项的复制而不是原始的对象）或重载了一半操作符（比如重载<但是不重载>）。

注意，和C++不用，C#不允许重载如下的操作符：new、()、||、&&、=或各种组合赋值，比如+=、-=。但是，重载的组合赋值会调用重载的操作符，比如+=会调用重载的+。

    C#：
    class OverloadedNumber
    {
        private int value; 
        public OverloadedNumber(int value)
        {
            this.value = value; 
        }

        public override string ToString()
        {
            return value.ToString(); 
        }

        public static OverloadedNumber operator -(OverloadedNumber number)
        {
            return new OverloadedNumber(-number.value); 
        }

        public static OverloadedNumber operator +(OverloadedNumber number1, OverloadedNumber number2)
        {
            return new OverloadedNumber(number1.value + number2.value); 
        }

        public static OverloadedNumber operator ++(OverloadedNumber number)
        {
            return new OverloadedNumber(number.value + 1); 
        }
    }

    public class OperatorOverloadingTest 
    {
        public static void Main(string[] args)
        {
            OverloadedNumber number1 = new OverloadedNumber(12); 
            OverloadedNumber number2 = new OverloadedNumber(125); 

            Console.WriteLine("Increment: {0}", ++number1);
            Console.WriteLine("Addition: {0}", number1 + number2);
        }
    } // OperatorOverloadingTest

## 4.switch语句
C#的switch和Java的switch有个主要的区别。在C#中，除非标签不包含任何语句否则不允许贯穿。贯穿是显式禁止的，因为可能导致难以找到的bug。

    C#:
    switch(foo)
    {    
        case "A": 
            Console.WriteLine("A seen");
            break;
        case "B": 
        case "C": 
            Console.WriteLine("B or C seen");
            break; 

            /* ERROR: Won't compile due to fall-through at case "D" */
        case "D": 
            Console.WriteLine("D seen");
        case "E":
            Console.WriteLine("E seen");
            break; 
    }

## 5.程序集
C#程序集和Java的JAR文件有很多共性。程序集是.NET环境最基本的代码打包单元。程序集包含了中间语言代码、类的元数据以及其它执行任务所需要的数据。由于程序集是最基本的打包单元，和类型相关的一些行为必须在程序集级别进行。例如，安全授权、代码部署、程序集级别的版本控制。Java JAR文件有着相似的作用，但是实现不一样。程序集一般是EXE或DLL而JAR文件是ZIP文件格式的。

## 6.集合
许多有名的编程语言都会包含一个集合框架，框架一般由各种用于保存数据的数据结构和配套的操作对象的算法构成。集合框架的优势是让开发者可以不用写数据结构和排序算法，把精力放在真正的业务逻辑上。还有就是可以让不同的项目保持一致性，新的开发者也少了很多学习曲线。

C#集合框架大多位于System.Collections和System.Collections.Generic命名空间。Systems.Collections命名空间包含了表示抽象数据类型的接口和抽象类，比如IList, IEnumerable, IDictionary, ICollection, 和 CollectionBase，只要数据结构从抽象数据类型派生，开发者无需关心其内部如何实现。System.Collections命名空间还包含了很多数据结构的具体实现，包括ArrayList, Stack, Queue, HashTable 和SortedList。这四种结构都提供了同步包装，可以在多线程程序中线程安全。System.Collections.Generic命名空间实现了System.Collections空间中主要数据结构的泛型版本，包括泛型的List<T>, Stack<T>,Queue<T>, Dictionary<K,T> 和SortedDictionary<K,T>类。

Java集合框架则在java.util包中包含许多类和接口。java.util包也同样支持泛型，并没有使用新的命名空间来放置泛型类型。Java集合框架和C#相似，不过可以认为是C#集合框架的超集，因为它实现了一些其它特性。

## 7.goto
和Java不同，C#包含的goto可以用来在代码中进行跳转，尽管goto被嘲笑，但是有的时候还是可以使用goto来减少代码重复并增加可读性。goto语句第二个用处是可以重用异常，因为异常抛出是无法跨越方法边界的。

_注释：在C#中goto无法跳转到语句块。_

    C#：
    class GotoSample
    {
        public static void Main(string[] args)
        {
            int num_tries = 0;
            retry: 

            try
            {             
                num_tries++;    
                Console.WriteLine("Attempting to connect to network. Number of tries =" + num_tries);

                //Attempt to connect to a network times out 
                //or some some other network connection error that 
                //can be recovered from
            
                throw new SocketException(); 
            }
            catch(SocketException)
            {
                if(num_tries < 5)
                goto retry;                 
            }       
        }/* Main(string[]) */ 

    }//GotoSample

## 8.虚方法
面向对象的一个主要特点就是多态。多态可以让我们和继承体系中的泛化类型而不是实际类型打交道。也就是一般在基类中实现的方法在派生类中重写，我们即使持有基类类型的引用，但是其指向派生类型，在运行时而不是编译时动态绑定的方法叫做虚方法。在Java中所有的方法都是虚方法，而在C#中，必须通过virtual关键字显式指定方法为虚方法，默认不是虚方法。同样可以用override关键字在子类中重写虚方法或使用new关键字隐藏基类方法。在Java中可以通过标记方法为final关键字让方法不能被派生类重写，在C#中可以不标记virtual来实现。主要区别是，如果派生类也实现了相同方法，C#的可以通过把引用指向基类调用到基类的方法，而在Java中如果基类实现了final方法，派生类不允许再有同名的方法。

    C#：
    public class Parent
    {
        public void DoStuff(string str)
        {
            Console.WriteLine("In Parent.DoStuff: " + str); 
        }
    }

    public class Child: Parent
    {
        public void DoStuff(int n)
        {
            Console.WriteLine("In Child.DoStuff: " + n);    
        }

        public void DoStuff(string str)
        {
            Console.WriteLine("In Child.DoStuff: " + str);  
        }
    }

    public class VirtualTest
    {

        public static void Main(string[] args)
        {
            Child ch = new Child(); 
            ch.DoStuff(100); 
            ch.DoStuff("Test"); 
            ((Parent) ch).DoStuff("Second Test"); 
        }
    }//VirtualTest

OUTPUT:  
In Child.DoStuff: 100  
In Child.DoStuff: Test  
In Parent.DoStuff: Second Test  

    Java：
    class Parent{

        public void DoStuff(String str){
        System.out.println("In Parent.DoStuff: " + str);    
        }

    }

    class Child extends Parent{

        public void DoStuff(int n){
        System.out.println("In Child.DoStuff: " + n);   
        }

        public void DoStuff(String str){
        System.out.println("In Child.DoStuff: " + str); 
        }
    }


    public class VirtualTest{

        public static void main(String[] args){

        Child ch = new Child(); 

        ch.DoStuff(100); 
        ch.DoStuff("Test"); 

        ((Parent) ch).DoStuff("Second Test"); 
        }

    }//VirtualTest

OUTPUT:  
In Child.DoStuff: 100  
In Child.DoStuff: Test  
In Child.DoStuff: Second Test  
C#的例子可以通过把基类DoStuff(string) 方法标记为virtual子类方法标记为override关键字来实现和Java相同输出：

    C#：
    public class Parent
    {
        public virtual void DoStuff(string str)
        {
            Console.WriteLine("In Parent.DoStuff: " + str); 
        }
    }

    public class Child: Parent
    {
        public void DoStuff(int n)
        {
            Console.WriteLine("In Child.DoStuff: " + n);    
        }

        public override void DoStuff(string str)
        {
            Console.WriteLine("In Child.DoStuff: " + str);  
        }
    }

    public class VirtualTest
    {
        public static void Main(string[] args)
        {

            Child ch = new Child(); 
            ch.DoStuff(100); 
            ch.DoStuff("Test"); 
            ((Parent) ch).DoStuff("Second Test"); 
        }
    }

如上例子可以修改子类的DoStuff(string)方法为如下以得到之前的结果：  

    public new void DoStuff(string str)

## 9.文件IO
两种语言都通过Stream类支持IO操作，如下例子把input.txt的内容复制到output.txt中。

    C#：
    public class FileIOTest 
    {
        public static void Main(string[] args)
        {
            FileStream inputFile  = new FileStream("input.txt", FileMode.Open);
            FileStream outputFile = new FileStream("output.txt", FileMode.Open);
            StreamReader sr     = new StreamReader(inputFile);
            StreamWriter sw     = new StreamWriter(outputFile);

            String str;

            while((str = sr.ReadLine())!= null)
                sw.Write(str);
                sr.Close();
                sw.Close();
        }
    }//FileIOTest

    Java：
    public class FileIO{

        public static void main(String[] args) throws IOException {

        File inputFile  = new File("input.txt");
        File outputFile = new File("output.txt");

            FileReader in     = new FileReader(inputFile);
        BufferedReader br = new BufferedReader(in);

            FileWriter out    = new FileWriter(outputFile);
        BufferedWriter bw = new BufferedWriter(out);

        String str;

        while((str = br.readLine())!= null)
            bw.write(str);

            br.close();
            bw.close();
        }

    }//FileIOTest

## 10.对象序列化

对象持久化或叫序列化是通过诸如文件或网络读写对象的能力。如果在使用程序的时候对象的状态必须保存下来，那么对象持久化就很有用。有的时候以简单文本形式保存数据不够，以DBMS保存数据又劳师动众了，那么可以使用序列化直接保存，还有的时候可以使用序列化来传输类型。C#中可序列化类型标记[Serializable]特性。如果C#的类的一些成员不需要在运行时序列化，可以标记[NonSerialized]特性。这些字段通常用于计算或是临时的值，不需要保存下来。C#提供了两种格式来序列化类，XML或二进制格式，前者对于人来说更可读，后者更高效。当然我们也可以通过实现ISerializable接口实现自定义的序列化方式。

在Java中，对象序列化需要实现Serializable接口，而transient关键字用于标记不需要序列化的成员。默认情况下，Java支持序列化对象到二进制格式，但是提供了重写标准序列化过程的方式。需要重写默认序列化的对象需要实现如下方法签名：

    private void readObject(java.io.ObjectInputStream stream) throws IOException, ClassNotFoundException;

    private void writeObject(java.io.ObjectOutputStream stream) throws IOException

由于上面的方法是private的，使用readObject和writeObject来实现自定义序列化的话没有要实现的接口，对于需要公开访问的方法的类实现自定义序列化可以使用java.io.Externalizable接口，指定readExternal() 和writeExternal()。

    C#：
    [Serializable]
    class SerializeTest
    {
        [NonSerialized]
        private int x; 
        private int y; 

        public SerializeTest(int a, int b)
        {
            x = a; 
            y = b; 
        }

        public override String ToString()
        {
            return "{x=" + x + ", y=" + y + "}"; 
        }

        public static void Main(String[] args)
        {
            SerializeTest st = new SerializeTest(66, 61); 
            Console.WriteLine("Before Binary Write := " + st);

            Console.WriteLine("\n Writing SerializeTest object to disk");
            Stream output  = File.Create("serialized.bin");
            BinaryFormatter bwrite = new BinaryFormatter(); 
            bwrite.Serialize(output, st); 
            output.Close(); 

            Console.WriteLine("\n Reading SerializeTest object from disk\n");
            Stream input  = File.OpenRead("serialized.bin");
            BinaryFormatter bread = new BinaryFormatter(); 
            SerializeTest fromdisk = (SerializeTest)bread.Deserialize(input); 
            input.Close(); 


            /* x will be 0 because it won't be read from disk since non-serialized */ 
            Console.WriteLine("After Binary Read := " + fromdisk);

            
            st = new SerializeTest(19, 99);  
            Console.WriteLine("\n\nBefore SOAP(XML) Serialization := " + st);

            Console.WriteLine("\n Writing SerializeTest object to disk");
            output  = File.Create("serialized.xml");
            SoapFormatter swrite = new SoapFormatter(); 
            swrite.Serialize(output, st); 
            output.Close(); 

            Console.WriteLine("\n Reading SerializeTest object from disk\n");
            input  = File.OpenRead("serialized.xml");
            SoapFormatter sread = new SoapFormatter(); 
            fromdisk = (SerializeTest)sread.Deserialize(input); 
            input.Close(); 


            /* x will be 0 because it won't be read from disk since non-serialized */ 
            Console.WriteLine("After SOAP(XML) Serialization := " + fromdisk);

            
            Console.WriteLine("\n\nPrinting XML Representation of Object");

            XmlDocument doc = new XmlDocument(); 
            doc.Load("serialized.xml"); 
            Console.WriteLine(doc.OuterXml);
        }
    }

    Java：
    class SerializeTest implements Serializable{
        transient int x; 
        private int y; 

        public SerializeTest(int a, int b){
            x = a; 
            y = b; 
        }

        public String toString(){

        return "{x=" + x + ", y=" + y + "}"; 

        }

        public static void main(String[] args) throws Exception{

        SerializeTest st = new SerializeTest(66, 61); 
        System.out.println("Before Write := " + st);

        System.out.println("\n Writing SerializeTest object to disk");
        FileOutputStream out  = new FileOutputStream("serialized.txt");
        ObjectOutputStream so = new ObjectOutputStream(out);    
        so.writeObject(st);
        so.flush();

        System.out.println("\n Reading SerializeTest object from disk\n");
            FileInputStream in     = new FileInputStream("serialized.txt");
            ObjectInputStream si   = new ObjectInputStream(in); 
            SerializeTest fromdisk = (SerializeTest)si.readObject();

            /* x will be 0 because it won't be read from disk since transient */ 
            System.out.println("After Read := " + fromdisk);
            }
    }

输出结果：  
Before Write := {x=66, y=61}  
Writing SerializeTest object to disk  
Reading SerializeTest object from disk  
After Read := {x=0, y=61}  

 

## 11.文档生成
C#和Java都提供了从源文件提取特殊格式的注释然后集中到一个文档中。这些注释一般是API规范，这是一种非常有用的方式来生成类库文档。生成的文档也可以在设计者、开发者和QA之间分发。Javadoc是一种非常有用的工具用于从源代码提取API文档。Javadoc从源代码注释中提取内容生成HTML文档。可以生成的描述信息包括包、类、成员级别。可以在类或成员变量的描述中提供对其它类或类成员的引用。

Javadoc允许方法有如下信息：
* 1.描述方法
* 2.方法抛出的异常
* 3.方法接收的参数
* 4.方法的返回值
* 5.关联的方法和成员
* 6.API是否被弃用
* 7.方法首次提供的时间

废弃信息对于编译器也有用，如果在编译的时候编译器发现调用了废弃的方法可以给予警告。

Javadoc自动提供如下信息：
* 1.继承的API
* 2.派生类列表
* 3.实现的类或借口
* 4.类序列化格式
* 5.包继承层次

由于Java生成HTML文档，可以在Javadoc注释中使用HTML。如下是一个例子：

    Java：
    /**
    * Calculates the square of a number. 
    * @param num the number to calculate. 
    * @return the square of the number. 
    * @exception NumberTooBigException this occurs if the square of the number 
    * is too big to be stored in an int. 
    */
    public static int square(int num) throws NumberTooBigException{}

C#使用XML作为文档格式。生成的文档是XML文件，包含了用于提供的元数据以及少量自动生成的信息。C#的XML文档在生成的时候不会包含有关继承API列表、派生类或实现接口等元数据。

XML格式的主要优势是可以以各种方式来用。可以用XSLT样式来吧生成ASCII文本、HTML等。也可以作为一些工具的数据源来生成特殊的文档。

如下是C# XML文档的例子：

    C#：
    ///<summary>Calculates the square of a number.</summary>
    ///<param name="num">The number to calculate.</param>
    ///<return>The square of the number. </return>
    ///<exception>NumberTooBigException - this occurs if the square of the number 
    ///is too big to be stored in an int. </exception>
    public static int square(int num){}
 

## 12.单个文件中的多个类
两种语言都可以在单个文件中定义多个类，但是有区别。在Java中，一个原文件中只可以有一个public访问的类并且类名需要和不带扩展名的源文件名保持一致。C#则对一个文件有多少个public类以及文件名是否和类名一致都没有限制。

## 13.导入类库
在应用程序中使用类库有两个步骤，首先需要在源文件中使用using或import关键字来引用空间或包，其次需要告诉编译器哪里有需要的类库。对于Java来说指定类库位置可以使用CLASSPATH环境变量或使用-classpath编译器选项，对于C#则在编译的时候指定/r开关。

## 14.事件
所谓事件驱动编程就是一个对象可以进行注册使得自己在别的独享状态修改或发生某个事件的时候被通知。事件驱动编程也被称作发布订阅模型或观察者设计模式，并且在图形用户接口GUI编程上特别常见。Java和C#都有自己的机制实现事件。典型的发布订阅模型是一个一对多的关系，也就是一个发布者对应多个订阅者。订阅者在发布者这里注册要调用的方法，订阅者通过内部集合保存订阅者对象。如果订阅者感兴趣的状态改变，发布者会调用一个方法遍历订阅者集合调用回调方法。

在Java中没有通用的机制来实现事件，而是采用了GUI类中使用的设计模式。事件一般是java.util.EventObject类的子类，这个类具有设置或获取事件来源的方法。在Java中订阅者一般实现接口，并且以Listener结尾，比如MouseListener, ActionListener, KeyListener，包含一个回调方法用于在事件发生的时候被发布者调用。发布者一般包含add和Listerner名字组合而成的方法用于添加注册的订阅者，比如addMouseListener, addActionListener, addKeyListener。发布者还包含用于移除订阅者的方法。这些结构构成了Java程序中的事件驱动模型。

C#使用委托来提供发布订阅模型的显式支持，事件一般是System.EventArgs类的子类。发布者具有protected的方法并以On为前缀，比如OnClick, OnClose, OnInit，在某个事件发生的时候调用这个方法，这个方法然后会调用委托并且传入EventArgs对象的实例作为参数。这个方法作为protected的话派生类就可以直接调用，无需注册委托。订阅者的方法接收和事件委托相同的返回类型和参数。事件委托一般接收两个参数，一个Object表示事件的源，一个EventArgs类表示发生的事件，并且委托是void返回值。在C#中event用于自动指定事件驱动中回调的订阅者委托。在编译的时候，编译器会增加+=和-=，等同于Java的注册和移除订阅者的方法。

如下例子演示了一个类生成20个随机数，然后在遇到偶数的时候触发事件。

    C#：
    class EvenNumberEvent: EventArgs
    {
        /* HACK: fields are typically private, but making this internal so it
        * can be accessed from other classes. In practice should use properties.
        */ 
        internal int number; 

        public EvenNumberEvent(int number):base()
        {
            this.number = number;
        }
    }

    class Publisher
    {
        public delegate void EvenNumberSeenHandler(object sender, EventArgs e); 
        public event EvenNumberSeenHandler EvenNumHandler; 
        protected void OnEvenNumberSeen(int num)
        {
            if(EvenNumHandler!= null)
                EvenNumHandler(this, new EvenNumberEvent(num));
        }

        //generates 20 random numbers between 1 and 20 then causes and 
        //event to occur if the current number is even. 
        public void RunNumbers()
        {
            Random r = new Random((int) DateTime.Now.Ticks); 

            for(int i=0; i < 20; i++)
            {     
                int current = (int) r.Next(20); 

                Console.WriteLine("Current number is:" + current);

                //check if number is even and if so initiate callback call
                if((current % 2) == 0)
                OnEvenNumberSeen(current);

            }//for
        }
    }//Publisher


    public class EventTest
    {
        //callback function that will be called when even number is seen
        public static void EventHandler(object sender, EventArgs e)
        {
            Console.WriteLine("\t\tEven Number Seen:" + ((EvenNumberEvent)e).number);
        }

        public static void Main(string[] args)
        {
            Publisher pub = new Publisher(); 
            
            //register the callback/subscriber 
            pub.EvenNumHandler += new Publisher.EvenNumberSeenHandler(EventHandler); 
            
            pub.RunNumbers(); 

            //unregister the callback/subscriber 
            pub.EvenNumHandler -= new Publisher.EvenNumberSeenHandler(EventHandler); 
        }
    }

    Java：
    class EvenNumberEvent extends EventObject{

        public int number; 

        public EvenNumberEvent(Object source, int number){
        
        super(source); 
        this.number = number;
        }

    }

    interface EvenNumberSeenListener{

        void evenNumberSeen(EvenNumberEvent ene); 

    }

    class Publisher{

        Vector subscribers = new Vector(); 
        

        private void OnEvenNumberSeen(int num){

        for(int i=0, size = subscribers.size(); i < size; i++)
            ((EvenNumberSeenListener)subscribers.get(i)).evenNumberSeen(new EvenNumberEvent(this, num));
        }
        

        public void addEvenNumberEventListener(EvenNumberSeenListener ensl){

        subscribers.add(ensl); 

        }

        public void removeEvenNumberEventListener(EvenNumberSeenListener ensl){

        subscribers.remove(ensl); 
        }

        //generates 20 random numbers between 1 and 20 then causes and 
        //event to occur if the current number is even. 
        public void RunNumbers(){
        
        Random r = new Random(System.currentTimeMillis()); 

        for(int i=0; i < 20; i++){     
            int current = (int) r.nextInt() % 20; 

            System.out.println("Current number is:" + current);

            //check if number is even and if so initiate callback call
            if((current % 2) == 0)
            OnEvenNumberSeen(current);

        }//for
        
        }

    }//Publisher


    public class EventTest implements EvenNumberSeenListener{

        //callback function that will be called when even number is seen
        public void evenNumberSeen(EvenNumberEvent e){

        System.out.println("\t\tEven Number Seen:" + ((EvenNumberEvent)e).number);
        
        }


        public static void main(String[] args){

        EventTest et = new EventTest();

        Publisher pub = new Publisher(); 
        
        //register the callback/subscriber 
        pub.addEvenNumberEventListener(et); 
        
        pub.RunNumbers(); 

        //unregister the callback/subscriber 
        pub.removeEvenNumberEventListener(et); 
        
        }
    }
 
运行结果  

    Current number is:19   
    Current number is:15   
    Current number is:1  
    Current number is:1  
    Current number is:-9  
    Current number is:-17  
    Current number is:-1  
    Current number is:-18  
            Even Number Seen:-18   
    Current number is:0  
            Even Number Seen:0   
    Current number is:1   
    Current number is:-2    
            Even Number Seen:-2   
    Current number is:-9   
    Current number is:-4   
            Even Number Seen:-4   
    Current number is:17   
    Current number is:-7   
    Current number is:1   
    Current number is:0    
            Even Number Seen:0   
    Current number is:15   
    Current number is:-10   
            Even Number Seen:-10   
    Current number is:-9  

 

## 15.跨语言互操作
跨语言互操作是在一个语言中访问另一个语言构造的能力。在Java中有很多跨语言互操作的方式。首先JNI机制允许Java程序调用C或C++甚至汇编语言写的本机方法。本机方法可以使用JNI来访问Java的特性，比如调用Java语言方法，初始化和修改Java类，抛出和捕获异常，进行运行时类型检查，动态加载Java类。要创建JNI程序可以进行如下步骤：
* 1.创建Java程序，把包含本机方法的声明标记为native方法
* 2.写一个main方法加载步骤6的类库，然后使用本机方法
* 3.使用javac编译器编译包含native方法和main的类
* 4.使用javah编译器和-jni开关来生产头文件和本地方法
* 5.使用你选择的语言写本机方法
* 6.把头文件和本机源文件编译到共享类库中，比如Windows的dll或UNIX的.so

Java还可以通过Java IDL来和CORBA的分布式对象交互。CORBA应用程序一般由对象请求代理ORB、客户端和服务端构成。ORB负责匹配请求客户端到服务端，使用对象引用来定位目标对象。ORB检查对象引用的时候会检查目标对象是否是远程的。如果对象时本地的ORB进行进程内调用IPC，否则ORB封送参数并且把调用通过网络路由到远程ORB。远程ORB然后在本地调用方法，通过网络把结果发送回客户端。CORBA有语言无关的接口定义语言IDL，各种语言都可以支持CORBA映射。Java IDL支持从Java对象到CORBA IDL对象的映射，各种ORB提供各种语言的CORBA语言绑定，包括C, C++, Java, Python, Lisp, Perl, 和Scheme。

在Java中最无缝方式进行跨语言交互的方式是直接把Java编译成字节码。Jython脚本语言是Python编程语言整合到Java平台的一个版本。如下例子演示了Jython如何创建一个Java的随机数类型（java.util.Random）并且和这个类型的实例进行交互。

    C:\jython>jython  
    Jython 2.0 on java1.2.1  
    Type "copyright", "credits" or "license" for more information.  
    >>> from java.util import Random  
    >>> r = Random()  
    >>> r.nextInt()  
    -790940041  
    >>> for i in range(5):  
    ...     print r.nextDouble()  
    ...  
    0.23347681506123852  
    0.8526595592189546  
    0.3647833839988137  
    0.3384865260567278  
    0.5514469740469587  
    >>>  

C#和.NET运行时本来的一个设计目标就是无缝的跨语言交互。任何.NET公共语言运行时的语言都可以基于公共类型系统CTS互相交互。公共类型系统定义了类型如何声明，确保各种语言可以共享类型信息。元数据是描述程序集、类型和应用程序定义的特性的二进制信息，它保存在CLR PE中，或者在程序集加载后保存在内存中。当前.NET运行时支持的语言包括APL, C#, C++, COBOL, Component Pascal, Eiffel, Haskel#/Mondrian, Java, Mercury, Oberon, Perl, Python, Scheme, Smalltalk, ML, 和Visual Basic。由于一种语言中具有的特性很可能在另外一种语言中没有，.NET框架提供了CLS描述一组基本的语言特性和定义如何使用这些特性的规则。CLS规则是公共类型系统的子集，并且通过定义一组编程语言最常见的特性集合来确保跨语言互操作。C#编译器是CLS兼容的编译器，也就是说可以用于编译符合CLS的代码。C#编译器可以检查CLS规范并且在代码使用了不符合CLS功能的时候给出错误。要让C#编译器检查CLS规范可以使用[CLSCompliantAttribute(true)]特性。C#支持的另一种跨语言交互是基于COM的对象，这个机制允许开发者在C#中使用COM，反之亦然。在创建了包装类后，C#对象可以使用COM对象，包装类可以当做普通的C#对象来使用，.NET运行时会处理复杂的参数封送操作。可以使用tlbimp工具来自动创建包装类。对于COM对象使用C#对象，必须创建描述C#对象的类型库，可以使用tlbexp创建类型库以COM的形式来描述C#对象。还可以使用regasm工具来注册程序集。COM对象和C#对象交互的时候，运行时会负责COM和.NET之间数据的封送。C#程序还可以使用extern关键字和DllImport特性来使用任何DLL的功能，这么做的优势是不需要针对C#的调用为方法作特殊处理，并且也不需要有包装来调用既有的代码。