# 类文件结构

## Class类文件结构

Class文件是一组以8位字节为基础的二进制流，各个数据项目严格按照顺序紧凑地排列在Class文件之中，中间没有添加任何分隔符，这使得整个Class文件中存储的内容几乎全部是程序运行的必要数据，没有空隙存在。当遇到需要占用8位字节以上空间的数据项时，则会按照高位在前的方式分割成若干个8位字节进行存储。

Class文件格式采用一种类似于C语言结构体的伪结构来存储数，这种伪结构有两种数据类型：无符号数和表。

这里需要重复提一下，Class文件结构不像XML等描述语言，由于它没有任何分割符号，所以无论是数量甚至于数据存储的字节序这样的细节都被严格限定，哪个字节代表什么含义，长度是多少，先后顺序如何，都不允许改变。

### 魔数与Class文件版本

每个Class文件的头四个字节称为魔数（Magic Number）,它的唯一作用是确定这个文件 **是否为一个能被虚拟机接收的Class文件**。紧接着魔数的四个字节存储的是Class文件的版本号：第五和第六是 **次版本号**，第七和第八是 **主版本号**。

### 常量池

紧接着主次版本号之后的是常量池入口，常量池可以理解为 **Class文件之中的资源仓库**，它是Class文件结构中与其他项目关联最多的数据类型，也是占用Class文件空间最大的数据项目之一，同时它还是在Class文件中第一个出现的表类型数据项目。常量池主要存放两大常量：**字面量**和 **符号引用**。字面量比较接近于java语言层面的的常量概念，如文本字符串、声明为final的常量值等。而符号引用则属于编译原理方面的概念。包括下面三类常量：

* 类和接口的全限定名
* 字段的名称和描述符
* 方法的名称和描述符

### 访问标志

在常量池结束之后，紧接着的两个字节代表访问标志，这个标志用于识别一些类或者接口层次的访问信息，包括：这个Class是类还是接口，是否为 `public` 或者 `abstract` 类型，如果是类的话是否声明为 `final` 等等。具体标志位及标志的含义如下图所示：

![image](../resources/jvm_3_6.PNG)

### 类索引、父类索引与接口索引集合

类索引、父类索引与接口索引集合都按顺序排列在访问标志之后，Class文件由这三项数据来确定这个类的继承关系。类索引用于确定这个类的全限定名，父类索引用于确定这个类的父类的全限定名，由于Java语言的单继承，所以父类索引只有一个，除了`java.lang.Object`之外，所有的Java类都有父类，因此除了`java.lang.Object`外，所有java类的父类索引都不为0。接口索引集合用来描述这个类实现了那些接口，这些被实现的接口将按`implents`(如果这个类本身是接口的话则是`extends`)后的接口顺序从左到右排列在接口索引集合中。

### 字段表集合

字段表（field info）用于描述接口或类中声明的变量。字段包括类级变量以及实例变量，但不包括在方法内部声明的局部变量。

字段的作用域（`public` 、`private`和 `protected`修饰符），是实例变量还是类变量（`static`修饰符）、可变性（`final`）、并发可见性（`volatile`修饰符，是否强制从主内存读写）、可否被序列化（`transient`修饰符）、字段数据类型、字段名称。上述这些信息中，各个修饰符都是布尔值，要么有某个修饰符，要么没有，很适合使用标志位来表示。而字段叫什么名字、字段被定义为什么数据类型这些都是无法固定的，只能引用常量池中常量来描述。

### 方法表集合

结构如同字段表一样，依次包括了访问标志、名称索引、描述符索引、属性表集合几项。
在这里稍微提一下，因为volatile修饰符和transient修饰符不可以修饰方法，所以方法表的访问标志中没有这两个对应的标志，但是增加了`synchronized`、`native`、`abstract`等关键字修饰方法，所以也就多了这些关键字对应的标志。

### 属性表结合

在Class文件，字段表，方法表中都可以携带自己的属性表集合，以用于描述某些场景专有的信息。与Class文件中其它的数据项目要求的顺序、长度和内容不同，属性表集合的限制稍微宽松一些，不再要求各个属性表具有严格的顺序，并且只要不与已有的属性名重复，任何人实现的编译器都可以向属性表中写 入自己定义的属性信息，Java虚拟机运行时会忽略掉它不认识的属性。
