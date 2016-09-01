JAVA中Final的用法  



  
1.         修饰基础数据成员的final
这是final的主要用途，其含义相当于C/C++的const，即该成员被修饰为常量，意味着不可修改。如java.lang.Math类中的PI和E是final成员，其值为3.141592653589793和2.718281828459045。
2.         修饰类或对象的引用的final
在Java中，我们无法让对象被修饰为final，而只能修饰对象的引用，这意味着即使你写public final A a = new A(); 事实上a指向的对象的数据依然可以被修改，不能修改的是a本身的引用值，即你不能再对a进行重赋值。同样的情况出现在数组中，比如public final int[] a = {1, 2, 3, 4, 5}，事实上a中的数值是可修改的，即可以写a[0] = 3。据目前了解，java中数组内的数据是无法修饰为不可修改的，而C/C++可以。
3.         修饰方法的final
修饰方法的final和C/C++中修饰成员对象的const大不相同。首先，修饰方法的final含义不是“不可修改”，而是指该方法不可被继承成员重新定义。（注意，这里所说的不能被重新定义，并不是指子类一定不能定义同名方法，如果父类的方法是私有类型，子类是允许定义该方法的，这里指的不能重新定义是指不能通过改写方法来使得方法重写的多态性得以实现，如不希望A a = new B(); a.f();这样的重写方法情况出现）
示例：
public class A {
    // final方法f
    public final void f() {
       System.out.println("类A中的final方法f被调用了");
    }
}
public class B extends A {
    // 编译错误！父类的f方法是final类型，不可重写！
    //! public void f() {
    //!     System.out.println("类B中的方法f被调用了");
    //! }
}
此外，当一个方法被修饰为final方法时，意味着编译器可能将该方法用内联(inline)方式载入，所谓内联方式，是指编译器不用像平常调用函数那样的方式来调用方法，而是直接将方法内的代码通过一定的修改后copy到原代码中(将方法主体直接插入到调用处，而不是进行方法调用)。这样可以让代码执行的更快（因为省略了调用函数的开销），比如在int[] arr = new int[3]调用arr.length()等。
另一方面，私有方法也被编译器隐式修饰为final，这意味着private final void f()和private void f()并无区别。
4.         修饰类的final
当一个类被修饰为final时，它的含义很明确，就是不允许该类被继承，也就是说，该类“绝后”了，任何继承它的操作都会以编译错误告终。这也凸显出Java用final而不用const作为标识符的理由。(成员变量可以不是final，成员方法直接是final的)
       示例：
       public final class A {
}
// 编译错误！A是final类型，不可被继承！
//!public class B extends A{
//!}
5.                        参数final
 对对象参数做final修饰。以为对象变量传递的是其引用，为防止调用过程中无意的更改而修饰。