

      Collection 

　　├List 

　　│├LinkedList 

　　│├ArrayList 

　　│└Vector 

　　│　└Stack 

　　└Set 

　　Map 

　　├Hashtable 

　　├HashMap 

　　└WeakHashMap 

Collection是最基本的集合接口，一个Collection代表一组Object，即Collection的元素。一些Collection允许相同的元素而另一些不行。一些能排序而另一些不行。Java JDK不能提供直接继承自Collection的类，Java JDK提供的类都是继承自Collection的"子接口"，如:List和Set。 

注意：Map没有继承Collection接口，Map提供key到value的映射。一个Map中不能包含相同key，每个key只能映射一个value。Map接口提供3种集合的视图，Map的内容可以被当做一组key集合，一组value集合，或者一组key-value映射。 


详细介绍： 
List特点：元素有放入顺序，元素可重复 存储单列数据
Map特点：元素按键值对存储，无放入顺序 键不可重复 值可以重复 存储双列数据 
Set特点：元素无放入顺序，元素不可重复 存储单列数据（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的） 
List接口有三个实现类：LinkedList，ArrayList，Vector 
LinkedList：底层基于链表实现，链表内存是散乱的，每一个元素存储本身内存地址的同时还存储下一个元素的地址。链表增删快，查找慢 
ArrayList和Vector的联系及区别：
联系：都实现了list接口 有序 可重复 方便查询 增删需要造成大量元素移动
区别：
1.ArrayList是非线程安全的，效率高；Vector是基于线程安全的，效率低 
2.两者都是基于动态数组 数组容量不够会扩容 两者扩容大小不一样 
Set接口有两个实现类：HashSet(底层由HashMap实现)，LinkedHashSet 
SortedSet接口有一个实现类：TreeSet（底层由平衡二叉树实现） 
Query接口有一个实现类：LinkList 
Map接口有三个实现类：HashMap，HashTable，LinkeHashMap 

hashmap和hashtable区别：

  HashMap非线程安全，高效，支持null；HashTable线程安全，低效，不支持null 
就 HashMap 与 HashTable 主要从三方面来说。
共同点：都map功能 
一.历史原因:Hashtable 是基于陈旧的 Dictionary 类的， HashMap 是 Java 1.2引进的 Map
接口的一个实现
二.同步性:Hashtable 是线程安全的，也就是说是同步的，而 HashMap 是线程序不安全的，
不是同步的
三.值：只有 HashMap 可以让你将空值作为一个表的条目的 key 或 value






