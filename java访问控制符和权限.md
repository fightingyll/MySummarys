public ：能被所有的类(接口、成员)访问。
protected：只能被本类、同一个包中的类访问；如果在其他包中被访问，则必须是该成员所属类的子类。
private：成员变量和方法都只能在定义它的类中被访问，其他类都访问不到。对成员变量的进行获取和更改，一般用get()，set() ，public 方法。实现了Java面向对象的封装思想。
friendly（缺省）：访问权限与protected相似，但修饰类成员时不同包中的子类不能访问。
static：修饰变量,称为类变量或静态变量。静态变量是和类存在一起的,每个实例共享这个静态变量，在类加载时初始化。
final：被声明为final的变量必须在声明时给定初值，而在以后的引用中只能读取不能更改。修饰类是不能派生出子类，修饰方法时，不能被当前子类的方法覆盖。
abstract：不能创建abstract 类的实例。一般被继承，实现抽象方法。类只要有一个abstract方法，类就必须定义为abstract，但abstract类不一定非要保护abstract方法不可。
Java中访问控制与修饰符 - shally - shally
 
Java中访问控制与修饰符 - shally - shally
 
Java中访问控制与修饰符 - shally - shally
 
Java中访问控制与修饰符 - shally - shally
(上面的图表摘自CSDN博客，有助于理解）
方法重载时，private修饰的方法重写，语法不报错，可以通过编译， 但是调用时不会使用新写的方法，仍然调用父类的方法。