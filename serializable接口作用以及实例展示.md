转载自：http://bbs.itheima.com/forum.php?mod=viewthread&tid=42338&highlight=

一个对象序列化的接口，一个类只有实现了Serializable接口，它的对象才是可序列化的。从而可以实现对象流的转换，也就是将不同状态下的对象从内存以二进制流的形式进行网络传输或者文件保存，
因此如果要序列化某些类的对象，这些类就必须实现Serializable接口。而实际上，Serializable是一个空接口，没有什么具体内容，它的目的只是简单的标识一个类的对象可以被序列化。
什么情况下需要序列化 
a）当你想把的内存中的对象写入到硬盘的时候；
b）当你想用套接字在网络上传送对象的时候；
c）当你想通过RMI传输对象的时候；
再稍微解释一下:a)比如说你的内存不够用了，那计算机就要将内存里面的一部分对象暂时的保存到硬盘中，等到要用的时候再读入到内存中，硬盘的那部分存储空间就是所谓的虚拟内存。在比如过你要将某个特定的对象保存到文件中，我隔几天在把它拿出来用，那么这时候就要实现Serializable接口；
b)在进行java的Socket编程的时候，你有时候可能要传输某一类的对象，那么也就要实现Serializable接口；最常见的你传输一个字符串，它是JDK里面的类，也实现了Serializable接口，所以可以在网络上传输。
c)如果要通过远程的方法调用（RMI）去调用一个远程对象的方法，如在计算机A中调用另一台计算机B的对象的方法，那么你需要通过JNDI服务获取计算机B目标对象的引用，将对象从B传送到A，就需要实现序列化接口。

Serializable是一个接口，凡是一个接口中没有任何定义，就被称为标记接口，像这种接口java中有很多，
标记接口就是用来做标记的，例如：房产证上面不是你的名字，法律上你就没有这个房子的所有权。
而类也是一样，你这个类没有实现Serializable接口，在java中你就无法对此类进行序列化。
现在就来回答一下，楼主提出的这个问题。
问：这个接口中没有任何方法，那么是如何让他序列化的呢？什么又是反序列化呢？
一个类实现了Serializable只能代表这个类的对象可以被序列化，但是如何序列化这个类的对象？
于是java就给我们提供一个可以对象进行序列化的类：ObjectOutputStream
序列化就是把一个对象持久性存储
               （序列化案例）
ObjectOutputStream oos=new ObjectOutputStream(new BufferedOutputStream(new FileOutputStream("E:\\person.object")));//要把对象序列化（持久性存储）到硬盘的指定位置
                Person P=new Person("小王",229);//被序列化的对象
                oos.writeObject(P);//把此对象序列化
                oos.close();
复制代码
java同时也提供了对应的反序列化类：ObjectInputStream
通俗的理解反序列化就是把存在硬盘上的这个对象读取到内存中来
（反序列化案例）
ObjectInputStream ois=new ObjectInputStream(new BufferedInputStream(new FileInputStream("E:\\person.object")));//从硬盘的指定位置读取存储对象的文件
                Person p=(Person)ois.readObject();//调用readObject进行反列化
                System.out.println(p);
                ois.close();



