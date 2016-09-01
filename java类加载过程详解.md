转载自：http://my.oschina.net/volador/blog/87194?p=2&temp=1469091268588#blog-comments-list

今天去涉猎了一下类的加载的过程，现在也总结一下：
一个java文件从被加载到被卸载这个生命过程，总共要经历5个阶段：
加载->链接（验证+准备+解析）->初始化（使用前的准备）->使用->卸载
其中加载（除了自定义加载）+链接的过程是完全由jvm负责的，什么时候要对类进行初始化工作（加载+链接在此之前已经完成了），jvm有严格的规定（五种情况）：
1.遇到new，getstatic，putstatic，invokestatic这4条字节码指令时，假如类还没进行初始化，则马上对其进行初始化工作。其实就是3种情况：用new实例化一个类时、读取或者设置类的静态字段时（不包括被final修饰的静态字段，因为他们已经被塞进常量池了）、以及执行静态方法的时候。
2.使用java.lang.reflect.*的方法对类进行反射调用的时候，如果类还没有进行过初始化，马上对其进行。
3.初始化一个类的时候，如果他的父亲还没有被初始化，则先去初始化其父亲。
4.当jvm启动时，用户需要指定一个要执行的主类（包含static void main(String[] args)的那个类），则jvm会先去初始化这个类。
5.用Class.forName(String className);来加载类的时候，也会执行初始化动作。注意:ClassLoader的loadClass(String className);方法只会加载并编译某类，并不会对其执行初始化。
以上5种预处理称为对一个类进行主动的引用，其余的其他情况，称为被动引用，都不会触发类的初始化。下面也举了些被动引用的例子：
/**
 * 被动引用情景1
 * 通过子类引用父类的静态字段，不会导致子类的初始化
 * @author volador
 *
 */
class SuperClass{
	static{
		System.out.println("super class init.");
	}
	public static int value=123;
}

class SubClass extends SuperClass{
	static{
		System.out.println("sub class init.");
	}
}

public class test{
	public static void main(String[]args){
		System.out.println(SubClass.value);
	}
	
}
输出结果是：super class init 123。
/**
 * 被动引用情景2
 * 通过数组引用来引用类，不会触发此类的初始化
 * @author volador
 *
 */
public class test{
	public static void main(String[] args){
		SuperClass s_list=new SuperClass[10];
	}
}
输出结果：没输出
/**
 * 被动引用情景3
 * 常量在编译阶段会被存入调用类的常量池中，本质上并没有引用到定义常量类类，所以自然不会触发定义常量的类的初始化
 * @author root
 *
 */
class ConstClass{
	static{
		System.out.println("ConstClass init.");
	}
	public final static String value="hello";
}

public class test{
	public static void main(String[] args){
		System.out.println(ConstClass.value);
	}
}
输出结果：hello（tip：在编译的时候，ConstClass.value已经被转变成hello常量放进test类的常量池里面了）
以上是针对类的初始化，接口也要初始化，接口的初始化跟类的初始化有点不同：
上面的代码都是用static{}来输出初始化信息的，接口没法做到，但接口初始化的时候编译器仍然会给接口生成一个<clinit>()的类构造器，用来初始化接口中的成员变量，这点在类的初始化上也有做到。真正不同的地方在于第三点，类的初始化执行之前要求父类全部都初始化完成了，但接口的初始化貌似对父接口的初始化不怎么感冒，也就是说，子接口初始化的时候并不要求其父接口也完成初始化，只有在真正使用到父接口的时候它才会被初始化（比如引用接口上的常量的时候啦）。
下面分解一下一个类的加载全过程：加载->验证->准备->解析->初始化
首先是加载：
    这一块虚拟机要完成3件事：
        1.通过一个类的全限定名来获取定义此类的二进制字节流。
        2.将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。
        3.在java堆中生成一个代表这个类的java.lang.Class对象，作为方法区这些数据的访问入口。
关于第一点，很灵活,很多技术都是在这里切入，因为它并没有限定二进制流从哪里来：
从class文件来->一般的文件加载
从zip包中来->加载jar中的类
从网络中来->Applet
..........
相比与加载过程的其他几个阶段，加载阶段可控性最强，因为类的加载器可以用系统的，也可以用自己写的，程序猿可以用自己的方式写加载器来控制字节流的获取。
获取二进制流获取完成后会按照jvm所需的方式保存在方法区中，同时会在java堆中实例化一个java.lang.Class对象与堆中的数据关联起来。
加载完成后就要开始对那些字节流进行检验了（其实很多步骤是跟上面交叉进行的，比如文件格式验证）：
检验的目的：确保class文件的字节流信息符合jvm的口味，不会让jvm感到不舒服。假如class文件是由纯粹的java代码编译过来的，自然不会出现类似于数组越界、跳转到不存在的代码块等不健康的问题，因为一旦出现这种现象，编译器就会拒绝编译了。但是，跟之前说的一样，Class文件流不一定是从java源码编译过来的，也可能是从网络或者其他地方过来的，甚至你可以自己用16进制写，假如jvm不对这些数据进行校验的话，可能一些有害的字节流会让jvm完全崩溃。
检验主要经历几个步骤：文件格式验证->元数据验证->字节码验证->符号引用验证
文件格式验证：验证字节流是否符合Class文件格式的规范并 验证其版本是否能被当前的jvm版本所处理。ok没问题后，字节流就可以进入内存的方法区进行保存了。后面的3个校验都是在方法区进行的。
元数据验证：对字节码描述的信息进行语义化分析，保证其描述的内容符合java语言的语法规范。
字节码检验：最复杂，对方法体的内容进行检验，保证其在运行时不会作出什么出格的事来。
符号引用验证：来验证一些引用的真实性与可行性，比如代码里面引了其他类，这里就要去检测一下那些来究竟是否存在；或者说代码中访问了其他类的一些属性，这里就对那些属性的可以访问行进行了检验。（这一步将为后面的解析工作打下基础）
验证阶段很重要，但也不是必要的，假如说一些代码被反复使用并验证过可靠性了，实施阶段就可以尝试用-Xverify:none参数来关闭大部分的类验证措施，以简短类加载时间。
接着就上面步骤完成后，就会进入准备阶段了：
这阶段会为类变量（指那些静态变量）分配内存并设置类比那辆初始值的阶段，这些内存在方法区中进行分配。这里要说明一下，这一步只会给那些静态变量设置一个初始的值，而那些实例变量是在实例化对象时进行分配的。这里的给类变量设初始值跟类变量的赋值有点不同，比如下面：
public static int value=123;
在这一阶段，value的值将会是0，而不是123，因为这个时候还没开始执行任何java代码，123还是不可见的，而我们所看到的把123赋值给value的putstatic指令是程序被编译后存在于<clinit>()，所以，给value赋值为123是在初始化的时候才会执行的。
这里也有个例外：
public static final int value=123;
这里在准备阶段value的值就会初始化为123了。这个是说，在编译期，javac会为这个特殊的value生成一个ConstantValue属性，并在准备阶段jm就会根据这个ConstantValue的值来为value赋值了。
完成上步后，就要进行解析了。解析好像是对类的字段，方法等东西进行转换，具体涉及到Class文件的格式内容，并没深入去了解。
初始化过程是类加载过程的最后一步：
在前面的类加载过程中，除了在加载阶段用户可以通过自定义类加载器参与之外，其他的动作完全有jvm主导，到了初始化这块，才开始真正执行java里面的代码。
这一步将会执行一些预操作，注意区分在准备阶段，已经为类变量执行过一次系统赋值了。
其实说白了，这一步就是执行程序的<clinit>()；方法的过程。下面我们来研究一下<clinit>()方法：
<clinit>()方法叫做类构造器方法，有编译器自动手机类中的所有类变量的赋值动作和静态语句块中的语句合并而成的，置于他们的顺序与在源文件中排列的一样。
<clinit>();方法与类构造方法不一样，他不需要显示得调用父类的<clinit>();方法，虚拟机会保证子类的<clinit>();方法在执行前父类的这个方法已经执行完毕了，也就是说，虚拟机中第一个被执行的<clinit>()；方法肯定是java.lang.Object类的。
下面来个例子说明一下：
static class Parent{
    public static int A=1;
    static{
        A=2;
    }
}
static class Sub extends Parent{
    public static int B=A;
}
public static void main(String[] args){
    System.out.println(Sub.B);
}
首先Sub.B中对静态数据进行了引用，Sub类要进行初始化了。同时，其父类Parent要先进行初始化动作。Parent初始化后，A=2，所以B=2；上个过程相当于：
static class Parent{
    <clinit>(){
        public static int A=1;
        static{
            A=2;
        }
    }
}
static class Sub extends Parent{
    <clinit>(){  //jvm会先让父类的该方法执行完在执行这里
    public static int B=A;
    }
}
public static void main(String[] args){
    System.out.println(Sub.B);
}
<clinit>();方法对类跟接口来说不是必须的，假如类或者接口中没有对类变量进行赋值且没有静态代码块，<clinit>()方法就不会被编译器生成。
由于接口里面不能存在static{}这种静态代码块，但仍然可能存在变量初始化时的变量赋值操作，所以接口里面也会生成<clinit>()构造器。但跟类的不同的是，执行子接口的<clinit>();方法前并不需要执行父接口的<clinit>();方法，当父接口中定义的变量被使用时，父接口才会被初始化。
另外，接口的实现类在初始化的时候也一样不会执行接口的<clinit>()；方法。
另外，jvm会保证一个类的<clinit>();方法在多线程环境下能被正确地加锁同步。<因为初始化只会被执行一次>。
下面用个例子说明一下：
public class DeadLoopClass {

	static{
		if(true){
		System.out.println("要被 ["+Thread.currentThread()+"] 初始化了,下面来一个无限循环");
		while(true){}	
		}
	}
	
	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.out.println("toplaile");
		Runnable run=new Runnable(){

			@Override
			public void run() {
				// TODO Auto-generated method stub
				System.out.println("["+Thread.currentThread()+"] 要去实例化那个类了");
				DeadLoopClass d=new DeadLoopClass();
				System.out.println("["+Thread.currentThread()+"] 完成了那个类的初始化工作");
				
			}};
			
			new Thread(run).start();
			new Thread(run).start();
	}

}
这里面，运行的时候将会看到阻塞现象。
呼呼～先到这里`

