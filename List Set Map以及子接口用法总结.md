
Collection﻿
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

list 和set 有共同的父类Collection  它们的用法也是一样的 唯一的不太就是set中不能有相同的元素 list中可以 序列 集合
list和set的用途非常广泛 list可以完全代替数组来使用
map 是独立的合集 它使用键值对的方式来储存数据 键不能有重复的 值可以用 map不像上边两种集合那个用的广泛 不过在servlet 和jsp中 map可是绝对的重中之重 页面之间传值全靠map

list有序可重复 set无序不可重复
list有arraylist(随机存取非常高效 便于查找)和linkedlist(便于删除增加 不方便查找 从第一个开始) vector 


   arraylist 和 vector区别如下：
   arraylist ：线程不安全（同时同一个）   高效（一个优点）      扩容慢 一次扩充之前空间的一半   

   vector：线程安全（允许多个线程一起）  现成安全带来的较低效率 一次扩充之前空间的一倍
                       
    
   
 List Set Map主要方法：

List
基本信息
boolean     isEmpty()

int             size()

boolean     contains(Object o)

Iterator<E>     iterator()

增删改查（序号 以及序号的数据）

void            add(E e)

boolean     remove(Object o)

Entryobject             get(int index)

int             indexOf(Object o) 默认firstindex

int             lastIndexOf(Object o)

操作

Object[]     toArray()  注意是object类型




Set

基本信息：
boolean     isEmpty()

int             size()

boolean     contains(Object o)

Iterator<E>     iterator()

增删改查 set没有任何差 除了迭代其

boolean     add(E e)

boolean     remove(Object o)

操作

Object[]     toArray()




Map
基本信息：contains键或者值
int     size()

boolean     isEmpty()

boolean     containsKey(Object key)

boolean     containsValue(Object value)

增删改查：可以修改replace
Valueobject             get(Object key)

Valueobject             put(K key, V value)

Valueobject             replace(K key, V value)

Valueobject             remove(Object key)
Removes the mapping for a key from this map if it is present (optional operation).

default boolean     remove(Object key, Object value)
Removes the entry for the specified key only if it is currently mapped to the specified value.



操作：
Set<K>     keySet()  set可以toarray
Collection<V>     values()
Set<Map.Entry<K,V>>     entrySet()

Hashtable介绍：
类实现一个哈希表，该哈希表将键映射到相应的值。任何非 null 对象都可以用作键或值。为了成功地在哈希表中存储和获取对象，用作键的对象必须实现 hashCode 方法和 equals 方法。
Hashtable 的实例有两个参数影响其性能：初始容量 和加载因子。容量 是哈希表中桶 的数量，初始容量 就是哈希表创建时的容量。注意，哈希表的状态为 open：在发生“哈希冲突”的情况下，单个桶会存储多个条目，这些条目必须按顺序搜索。加载因子是对哈希表在其容量自动增加之前可以达到多满的一个尺度。初始容量和加载因子这两个参数只是对该实现的提示。关于何时以及是否调用 rehash 方法的具体细节则依赖于该实现。

 HashTable和HashMap区别
第一，继承不同。
public class Hashtable extends Dictionary implements Map
public class HashMap  extends AbstractMap implements Map
第二
Hashtable 中的方法是同步的，而HashMap中的方法在缺省情况下是非同步的。在多线程并发的环境下，可以直接使用Hashtable，但是要使用HashMap的话就要自己增加同步处理了。因为线程安全的问题，HashMap效率比HashTable的要高。
第三
Hashtable中，key和value都不允许出现null值。
在HashMap中，null可以作为键，这样的键只有一个；可以有一个或多个键所对应的值为null。当get()方法返回null值时，即可以表示 HashMap中没有该键，也可以表示该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。
第四，两个遍历方式的内部实现上不同。
Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 。
第五
哈希值的使用不同，HashTable直接使用对象的hashCode。而HashMap重新计算hash值。
第六
Hashtable和HashMap它们两个内部实现方式的数组的初始大小和扩容的方式。HashTable中hash数组默认大小是11，增加的方式是 old*2+1。HashMap中hash数组的默认大小是16，而且一定是2的指数。

