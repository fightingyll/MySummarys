static表示“全局”或者“静态”的意思，用来修饰成员变量和成员方法，也可以形成静态static代码块，但是Java语言中没有全局变量的概念。 

被static修饰的成员变量和成员方法独立于该类的任何对象。也就是说，它不依赖类特定的实例，被类的所有实例共享。

只要这个类被加载，Java虚拟机就能根据类名在运行时数据区的方法区内定找到他们。因此，static对象可以在它的任何对象创建之前访问，无需引用任何对象。 

用public修饰的static成员变量和成员方法本质是全局变量和全局方法，当声明它类的对象市，不生成static变量的副本，而是类的所有实例共享同一个static变量。 

static变量前可以有private修饰，表示这个变量可以在类的静态代码块中，或者类的其他<a target="_blank" rel="nofollow" href="https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E6%88%90%E5%91%98&amp;tn=44039180_cpr&amp;fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4m1IWmWcvnWbLuyn3nyN-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWbYPHDvn163" target="_blank" class="baidu-highlight">静态成员</a>方法中使用（当然也可以在非<a target="_blank" rel="nofollow" href="https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E6%88%90%E5%91%98&amp;tn=44039180_cpr&amp;fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4m1IWmWcvnWbLuyn3nyN-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWbYPHDvn163" target="_blank" class="baidu-highlight">静态成员</a>方法中使用--废话），但是不能在其他类中通过类名来直接引用，这一点很重要。实际上你需要搞明白，private是访问权限限定，static表示不要实例化就可以使用，这样就容易理解多了。static前面加上其它访问权限关键字的效果也以此类推。 

static修饰的成员变量和成员方法习惯上称为<a target="_blank" rel="nofollow" href="https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E5%8F%98%E9%87%8F&amp;tn=44039180_cpr&amp;fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4m1IWmWcvnWbLuyn3nyN-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWbYPHDvn163" target="_blank" class="baidu-highlight">静态变量</a>和静态方法，可以直接通过类名来访问，访问语法为： 
类名.静态方法名(参数列表...) 
类名.<a target="_blank" rel="nofollow" href="https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E5%8F%98%E9%87%8F&amp;tn=44039180_cpr&amp;fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4m1IWmWcvnWbLuyn3nyN-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWbYPHDvn163" target="_blank" class="baidu-highlight">静态变量</a>名 

用static修饰的代码块表示静态代码块，当Java虚拟机（JVM）加载类时，就会执行该代码块（用处非常大，呵呵）。 

1、static变量 
　按照是否静态的对类成员变量进行分类可分两种：一种是被static修饰的变量，叫<a target="_blank" rel="nofollow" href="https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E5%8F%98%E9%87%8F&amp;tn=44039180_cpr&amp;fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4m1IWmWcvnWbLuyn3nyN-0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnWbYPHDvn163" target="_blank" class="baidu-highlight">静态变量</a>或类变量；另一种是没有被static修饰的变量，叫实例变量。

两者的区别是： 
　对于静态变量在内存中只有一个拷贝（节省内存），JVM只为静态分配一次内存，在加载类的过程中完成静态变量的内存分配，可用类名直接访问（方便），当然也可以通过对象来访问（但是这是不推荐的）。 
　对于实例变量，没创建一个实例，就会为实例变量分配一次内存，实例变量可以在内存中有多个拷贝，互不影响（灵活）。 

所以一般在需要实现以下两个功能时使用静态变量：
&#61548;  在对象之间共享&#20540;时
&#61548;  方便访问变量时

2、静态方法 
静态方法