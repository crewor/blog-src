# Java字符串（==）

#### 字符串池

JAVA虚拟机(JVM)中有一个字符串常量池，其中保存着很多String对象;并且可以被共
享使用，因此它提高了效率。由于String类是final的，它的值一经创建就不可改变。
字符串池由String类维护，我们可以调用intern()方法来访问字符串池。

    String s1="123" ; //在字符串池创建了一个对象
    String s2="123" ; //字符串pool已经存在对象“abc”(共享),所以创建0个对象，累计创建一个对象
    s1==21 ;//true

#### new String("")
new String("12") 是运行期间才会执行的，编译期间并未向字符串池放入"12"
    
    String s2 = new String("12") ;//创建了两个对象，"12"存放在字符串池中，s2存在与堆区中；s2引用了s1的地址，但是s2的地址与s1的地址不同；s2是栈中的地址    
    String s3= new String("12"); //创建了一个对象，因为字符串池已经存在"12"
    s2==s3 ;// false,  s2和s3栈区的地址不同，指向堆区的不同地址

    String s1 = "12" ;//字符串池已经存在
    s1==s2; //false
    
#### 关于intern
由于String类是final的，它的值一经创建就不可改变。
字符串池由String类维护，我们可以调用intern()方法来访问字符串池。
    
    String s1 = new String("1");
    s1==s1.intern(); // false ,因为s1.intern指向的是"1"的常量池，而s1是堆栈地址
    
    String s2 = "1";
    s2==s1.intern() ;// true ，因为都是指向的同一个常量12的地址
    
#### 编译优化
由于常量的值在编译的时候就被确定(优化)了。
在这里，"12"和"34"都是常量，因此变量s1的值在编译时就可以确定。
这行代码编译后的效果等同于： String s1 = "1234";

    String s1 = "12" + "34";
    String s2 = "1234";
    s1==s2; //true

#### 局部变量问题
s3的流程最终为 String s3 = new StringBuilder(s1).append(s2).toString();
最后将刚生成的String对象的堆地址存放在局部变量s3中。
而s4存储的是字符串池中"1234"所对应的字符串对象的地址。
(s3.intern()==s4)//因为字符串常量池已经有1234了，所以s3.intern()返回了它的地址。

    String s1="12";
    String s2="34";
    String s3 = s1+s2;
    String s4 = "1234";
    s3==s4;//false 
    s3.intern()==s4//true
    
#### 编译器优化
JAVA编译器对string + 基本类型/常量 是当成常量表达式直接求值来优化的
行期的两个string相加，会产生新的对象的，存储在堆(heap)中

    String s1 = "1";
    String s2 = s1+"2";
    String s3 = "12";
    s2==s3;//false ，因为s2是在运行期被解析的
    
    但是如果把s1定义为final的常量变量，编译期会被优化
    final String s1 = "1";
    s2 ==s3;//true 
