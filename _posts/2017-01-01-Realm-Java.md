---
layout: post
title: Android下Realm使用的2、3事
date: 2017-01-01 20:15:58
tags: [Android, SQLite, Realm]
categories: [Java]
---

![HongKongEye](/images/2017-01-01-realm-00.jpg)

# 写在开始之前
SQL自从上世纪70年代诞生以来，就一直是数据库查询的主流语言。不得不承认SQL的出现极大的方便了我们检索和更新数据。不过在我们日常的开发中，其实并未有太多的数据量，也没有太多的表结构，也不是有很多的连表查询的需求，所以对于我们来说，使用SQL来检索数据有点太过繁琐。我们需要做的事情可能仅仅是把用户生成的数据对象快速的缓存起来。NoSQL数据库就很适合我们的这些场景。以Mongodb，Redis为代表的NoSQL都引入了一些相对现代化的方式存储数据，比如支持Json，Document的概念，流式api，数据变更通知等等，极大程度的降低了我们学习的成本提高了我们的开发效率。<!-- more -->相比于SQL，NoSQL有以下的优势：
* 轻量级，易于部署；
* 读写速度更快；
* 编写过程更加连贯；
* 易于版本更新；
* 最关键的是，成本更低。

Realm作为一个移动端的NoSQL的代码，官方的定位就是取代SQLite等在移动端的关系型数据库。从官方的[性能测试](https://realm.io/news/introducing-realm/#fast)来看，我们可以看到官方对取代SQLite等数据库的信心：

> 每秒能在20万条数据中进行查询后count的次数。realm每秒可以进行30.9次查询后count。
> ![RealmFast](/images/2017-01-01-realm-01.png)
> 在20万条中进行一次遍历查询，数据和前面的count相似：realm一秒可以遍历20万条数据31次，而coredata只能进行两次查询。
> ![RealmFast](/images/2017-01-01-realm-02.png)
> 这是在一次事务每秒插入数据的对比，realm每秒可以插入9.4万条记录，在这个比较里纯SQLite的性能最好，每秒可以插入17.8万条记录。然而封装了SQLite的FMDB的成绩大概是realm的一半。
> ![RealmFast](/images/2017-01-01-realm-03.png)

我大概花了一周的时间去了解Realm这个数据库在Android上的使用，并在我的[玩具项目](https://github.com/youngytj/android_MyPassword)上进行了实践。因为这些开始时间不久的开源项目在API上的变动上比较大，所以以下的内容仅仅适用于Realm For Java版本号为2.2.1的情形。

# 开始：在Android中如何引入Realm

## 在Android Studio中使用
首先我们需要在Project的build.gradle中加上`classpath "io.realm:realm-gradle-plugin:2.2.1"`

```gradle
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.3'
        classpath "io.realm:realm-gradle-plugin:2.2.1"
        classpath 'com.antfortune.freeline:gradle:0.8.4'
    }
}
```

然后在app的build.gradle中引用`apply plugin: 'realm-android'`即可。

## 在Eclipse中使用
![Fun](/images/2017-01-01-realm-04.jpg)

# 初见：获取一个Realm的实例并创建一个实体

## 获取Realm实例

Realm在获取实例之前，至少需要获取一个上下文对象，你也可以配置一个Configuration来指定它生成的数据库文件的名字以及数据库合并的行为等等。

```Java
RealmConfiguration realmConfiguration = new RealmConfiguration.Builder()
                .name("mypasswordrealm.realm").deleteRealmIfMigrationNeeded().build();
Realm.init(context);
Realm.setDefaultConfiguration(realmConfiguration);
this.realm = Realm.getDefaultInstance();
```
最后我们使用`getDefaultInstance`就可以获取一个Realm的实例。

## 创建一个Realm可以操作的实体

```Java
public class PasswordGroup extends RealmObject {
    @PrimaryKey
    private String groupName;

    public String getGroupName() {
        return groupName;
    }

    public void setGroupName(String groupName) {
        this.groupName = groupName;
    }

    @Override
    public String toString() {
        return "PasswordGroup [groupName=" + groupName + "]";
    }
}
```

为了创建一个Realm可以操作的实体，我们需要继承RealmObject，这样Realm就能操作这样的一个实体。

> Notes:
> Realm提供的注解有以下几个：
> * PrimaryKey：使用此注解的字段必须为String、 integer、byte、short、 int、long以及它们的包装类。与此同时，使用了这个注解的字段可以使用`copyToRealmOrUpdate`或者`insertOrUpdate`来进行插入和更新的操作。值得注意的是，Realm的实体不存在这注解的字段时，如果使用这些方法，结果是恒插入数据。
> * Required：不能为空
> * Ignore：不需要保存到数据库
> * Index：为该字段添加搜索引擎。这将导致数据占用的空间变大，并且插入时间变慢，但是加快了查询时间。

# 相识：CRUD(增加，查询，更新，删除)

我们存储数据不需要创建表等等的操作，我们只需要拿到我们的实体对象以及Realm实例就行了。Realm的操作方式可以分为同步和异步两种方式。

## 同步操作

```Java
// 新建对象并存储
realm.beginTransaction();
PasswordGroup group1 = realm.createObject(PasswordGroup.class);
group1.setGroupName("Test1");
realm.commitTransaction();

// 拷贝对象并存储
realm.beginTransaction();
PasswordGroup group2 = new PasswordGroup();
group2.setGroupName("Test2");
realm.copyToRealm(group2);
realm.commitTransaction();

// 查询对象并修改
realm.beginTransaction();
RealmResults<PasswordGroup> results = realm.where(PasswordGroup.class).findAll();
Iterator<PasswordGroup> iterator = results.iterator();
while (iterator.hasNext()) {
    PasswordGroup item = iterator.next();
    if (item.getGroupName().equals("Test2")) {
        item.setGroupName("Test3");
        break;
    }
}
realm.commitTransaction();

// 删除对象
realm.beginTransaction();
RealmResults<PasswordGroup> results = realm.where(PasswordGroup.class).equalTo("groupName", "Test2").findAll();
results.deleteAllFromRealm();
realm.commitTransaction();

```

如果你不喜欢beginTransaction和commitTransaction，也可以使用事务块的方式，只要把你的代码放到`execute`中

```Java
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {

    }
});
```

## 异步操作
异步操作的方式其实也是通过事务块的方式实现，只是`executeTransaction`变成了`executeTransactionAsync`, 并且还有`OnSuccess`和`OnError`可选操作。同时生成的Task也支持取消。

```Java
RealmAsyncTask task = realm.executeTransactionAsync(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {

    }
}, new Realm.Transaction.OnSuccess() {

    @Override
    public void onSuccess() {

    }
}, new Realm.Transaction.OnError() {
    @Override
    public void onError(Throwable error) {

    }
});

if (!task.isCancelled()) {
    task.cancel();
}
```

# 熟悉：日常使用中需要避免的一些坑

## 关于Realm的线程安全问题
Realm同步获取的RealmObject对象只能在当前线程中使用，比如说你在工作线程中查询得到的对象只能在工作线程中使用，UI线程只能在UI线程中使用。所以异步的方式很重要。

## Realm获得对象在Close以后不能访问
在调用`Realm.Close()`方法后，所获取的对象不能再访问，所以当获取到一个RealmObject后，官方提供一个`copyFromRealm`来复制出一份实例以供使用。

## 主键设置问题
最好给每个RealmObject设置一个主键，因为当不设置主键的时候，insertOrUpdate的操作默认就是insert，所以会生成重复对象。

## 会出现OOM的错误
因为Realm会在原生内存堆（native memory）上而不是Java虚拟机的内存堆分配内存。如果你的应用在内存管理上的存在问题导致 Realm 无法分配内存，io.realm.internal.OutOfMemoryError 异常会被抛出。

# 总结
本文简单介绍了Realm在Android下的一些使用场景，但并不全面。比如一些自动更新，动态对象也并未有介绍。如果有兴趣可以参阅[官方文档](https://realm.io/docs/java/latest/)。

# 参考信息
[官方文档](https://realm.io/cn/docs/java/latest/)  
[Realm数据库 从入门到“放弃”](https://halfrost.com/realm_ios/)  
[Android上替代SQLite的选择：Realm](http://www.infoq.com/cn/news/2014/10/Realm-android)  
[移动端数据库新王者：realm](http://www.jianshu.com/p/2b4388cf2a2d)  
[Android Realm数据库完美解析](http://blog.csdn.net/fesdgasdgasdg/article/details/51897212)  
[Realm for Android详细教程](http://www.jianshu.com/p/28912c2f31db)  
