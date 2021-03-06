<!-- TOC -->

- [stackoverflow](#stackoverflow)
    - [将InputStream转换为String](#将inputstream转换为string)
        - [使用 JDk `Scanner`](#使用-jdk-scanner)
        - [使用 Jdk `InputStreamReader` 和 `StringBuilder`](#使用-jdk-inputstreamreader-和-stringbuilder)
        - [使用 JDK `ByteArrayOutputStream` 和 `inputStream.read`](#使用-jdk-bytearrayoutputstream-和-inputstreamread)
        - [使用 JDK `BufferedReader `](#使用-jdk-bufferedreader-)
        - [使用 JDK `BufferedInputStream` 和 `ByteArrayOutputStream`](#使用-jdk-bufferedinputstream-和-bytearrayoutputstream)
        - [使用 JDK `inputStream.read()` 和 `StringBuilder`](#使用-jdk-inputstreamread-和-stringbuilder)
        - [使用 Java8 Stream](#使用-java8-stream)
        - [使用 parallel Stream](#使用-parallel-stream)
        - [使用 Apache Utils](#使用-apache-utils)
        - [使用 Apache Commons](#使用-apache-commons)
        - [使用 Guava](#使用-guava)
    - [将 Array 转化为 List](#将-array-转化为-list)
    - [HashMap 遍历](#hashmap-遍历)
        - [方法1 使用 For-Each 迭代 entries](#方法1-使用-for-each-迭代-entries)
        - [方法2 使用 For-Each 迭代 keys 和 values](#方法2-使用-for-each-迭代-keys-和-values)
        - [方法3 使用 Iterator 迭代](#方法3-使用-iterator-迭代)
        - [方法4 迭代keys并搜索values（低效的）](#方法4-迭代keys并搜索values低效的)
        - [总结](#总结)
    - [修饰符作用范围](#修饰符作用范围)
    - [如何测试一个数组是否包含指定的值](#如何测试一个数组是否包含指定的值)
        - [使用 JDK](#使用-jdk)
        - [使用 Apache Commons Lang](#使用-apache-commons-lang)
        - [使用 Java 8](#使用-java-8)
    - [重写（Override） `equals` 和 `hashCode` 方法时应考虑的问题](#重写override-equals-和-hashcode-方法时应考虑的问题)
    - [合并两个数组](#合并两个数组)
        - [使用 JDK](#使用-jdk-1)
        - [使用 Apache Common Lang](#使用-apache-common-lang)
    - [通过 String 查找 Enum](#通过-string-查找-enum)
    - [`finally` 总会被执行？](#finally-总会被执行)
    - [创建一个文件并写入内容](#创建一个文件并写入内容)
        - [创建文本文件](#创建文本文件)
        - [创建二进制文件](#创建二进制文件)
        - [创建文本文件 Java7+](#创建文本文件-java7)
        - [创建二进制文件 Java7+](#创建二进制文件-java7)
    - [Java `foreach` 工作原理](#java-foreach-工作原理)
    - [1927 年两个时间相减会得到奇怪结果](#1927-年两个时间相减会得到奇怪结果)
    - [计算 MD5 值](#计算-md5-值)
    - [一种奇怪的内部类定义方法](#一种奇怪的内部类定义方法)
    - [如何创建泛型数组](#如何创建泛型数组)
    - [获取完整的堆栈信息](#获取完整的堆栈信息)
    - [一行代码初始化列表](#一行代码初始化列表)
    - [初始化静态 Map](#初始化静态-map)
    - [给 3 个布尔变量，当其中有 2 个或者 2 个以上为 true 才返回 true](#给-3-个布尔变量当其中有-2-个或者-2-个以上为-true-才返回-true)
    - [输出数组最简单的方式](#输出数组最简单的方式)
    - [Java 源码中设计模式](#java-源码中设计模式)
    - [生成随机字符串](#生成随机字符串)
    - [将堆栈信息转化为字符串](#将堆栈信息转化为字符串)
    - [在整数左边填充 0](#在整数左边填充-0)
    - [从文件读取字符串](#从文件读取字符串)
    - [使用 `URLConnection` 接受和发送 HTTP 请求](#使用-urlconnection-接受和发送-http-请求)
    - [内存泄露代码](#内存泄露代码)

<!-- /TOC -->

# stackoverflow

## 将InputStream转换为String

### 使用 JDk `Scanner`

```java
Scanner s = new Scanner(inputStream).useDelimiter("\\A");
String result = s.hasNext() ? s.next() : "";
```

### 使用 Jdk `InputStreamReader` 和 `StringBuilder`

```java
final int bufferSize = 1024;
final char[] buffer = new char[bufferSize];
final StringBuilder out = new StringBuilder();
Reader in = new InputStreamReader(inputStream, "UTF-8");
for (; ; ) {
    int rsz = in.read(buffer, 0, buffer.length);
    if (rsz < 0)
        break;
    out.append(buffer, 0, rsz);
}
return out.toString();
```

### 使用 JDK `ByteArrayOutputStream` 和 `inputStream.read`

```java
ByteArrayOutputStream result = new ByteArrayOutputStream();
byte[] buffer = new byte[1024];
int length;
while ((length = inputStream.read(buffer)) != -1) {
    result.write(buffer, 0, length);
}
// StandardCharsets.UTF_8.name() > JDK 7
return result.toString("UTF-8");
```

### 使用 JDK `BufferedReader `

```java
String newLine = System.getProperty("line.separator");
BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
StringBuilder result = new StringBuilder();
String line; boolean flag = false;
while ((line = reader.readLine()) != null) {
    result.append(flag? newLine: "").append(line);
    flag = true;
}
return result.toString();
```

> 会将换行符转换为系统默认换行符

### 使用 JDK `BufferedInputStream` 和 `ByteArrayOutputStream`

```java
BufferedInputStream bis = new BufferedInputStream(inputStream);
ByteArrayOutputStream buf = new ByteArrayOutputStream();
int result = bis.read();
while(result != -1) {
    buf.write((byte) result);
    result = bis.read();
}
// StandardCharsets.UTF_8.name() > JDK 7
return buf.toString("UTF-8");
```

### 使用 JDK `inputStream.read()` 和 `StringBuilder`

```java
int ch;
StringBuilder sb = new StringBuilder();
while((ch = inputStream.read()) != -1)
    sb.append((char)ch);
reset();
return sb.toString();
```

> 存在 Unicode 编码问题

### 使用 Java8 Stream

```java
String result = new BufferedReader(new InputStreamReader(inputStream))
    .lines()
    .collect(Collectors.joining("\n"));
```

> 会将不同换行符转换为 `\n`

### 使用 parallel Stream

```java
String result = new BufferedReader(new InputStreamReader(inputStream))
    .lines()
    .parallel()
    .collect(Collectors.joining("\n"));
```

> 会将不同换行符转换为 `\n`

### 使用 Apache Utils

```java
String result = IOUtils.toString(inputStream, StandardCharsets.UTF_8);
```

### 使用 Apache Commons

```java
StringWriter writer = new StringWriter();
IOUtils.copy(inputStream, writer, "UTF-8");
return writer.toString();
```

### 使用 Guava

```java
String result = CharStreams.toString(new InputStreamReader(inputStream, Charsets.UTF_8));
```

> [原问题](http://stackoverflow.com/questions/309424/read-convert-an-inputstream-to-a-string)

## 将 Array 转化为 List

```java
Arrays.asList(array)
```

这种方式有两个问题：

1. `Arrays.asList()` 返回的是 `Arrays` 内部静态类，而不是 `Java.util.ArrayList`的类。这个 `java.util.Arrays.ArrayList` 有 `set()`，`get()` ，`contains()` 方法，但是没有任何 `add()` 方法，所以它是固定大小的，如果你对它做 `add` 或者 `remove` ，都会抛 `UnsupportedOperationException` 。

2. 如果修改数组的值，list中的对应值也会改变

```java
new ArrayList<Element>(Arrays.asList(array));

Collections.addAll(arraylist, array);
```

> [原问题](https://stackoverflow.com/questions/157944/create-arraylist-from-array)

## HashMap 遍历

在Java中有多种遍历HashMAp的方法。让我们回顾一下最常见的方法和它们各自的优缺点。由于所有的Map都实现了Map接口，所以接下来方法适用于所有Map（如：HaspMap，TreeMap,LinkedMap,HashTable,etc）

### 方法1 使用 For-Each 迭代 entries

这是最常见的方法，并在大多数情况下更可取的。当你在循环中需要使用Map的键和值时，就可以使用这个方法

```java
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
for(Map.Entry<Integer, Integer> entry : map.entrySet()){
	System.out.println("key = " + entry.getKey() + ", value = " + entry.getValue())
}
```

> 注意：For-Each 循环是Java5新引入的，所以只能在Java5以上的版本中使用。如果你遍历的 map 是 `null` 的话，For-Each循环会抛出 `NullPointerException` 异常，所以在遍历之前你应该判断是否为空引用。

### 方法2 使用 For-Each 迭代 keys 和 values

如果你只需要用到 map 的 keys 或 values 时，你可以遍历 `KeySet` 或者 `values` 代替 `entrySet`

```java
Map<Integer, Integer> map = new HashMap<Integer, Integer>();

//iterating over keys only
for (Integer key : map.keySet()) {
    System.out.println("Key = " + key);
}

//iterating over values only
for (Integer value : map.values()) {
    System.out.println("Value = " + value);
}
```

这个方法比 `entrySet` 迭代具有轻微的性能优势(大约快10%)并且代码更简洁

### 方法3 使用 Iterator 迭代

使用泛型

```java
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();
while (entries.hasNext()) {
	Map.Entry<Integer, Integer> entry = entries.next();
	System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
}
```

不使用泛型

```java
Map map = new HashMap();
Iterator entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry entry = (Map.Entry) entries.next();
    Integer key = (Integer)entry.getKey();
    Integer value = (Integer)entry.getValue();
    System.out.println("Key = " + key + ", Value = " + value);
}
```

你可以使用同样的技术迭代 `keyset` 或者 `values`

这个似乎有点多余但它具有自己的优势。首先，它是遍历老java版本map的唯一方法。另外一个重要的特性是可以让你在迭代的时候从map中删除entries的(通过调用 `iterator.remover()` )唯一方法。如果你试图在For-Each迭代的时候删除entries，你将会得到unpredictable resultes 异常。

从性能方法看，这个方法等价于使用For-Each迭代

### 方法4 迭代keys并搜索values（低效的）

```java
	Map<Integer, Integer> map = new HashMap<Integer, Integer>();
	for (Integer key : map.keySet()) {
    	Integer value = map.get(key);
    	System.out.println("Key = " + key + ", Value = " + value);
	}
```

这个方法看上去比方法1更简洁，但是实际上它更慢更低效，通过 key 得到 value 值更耗时（这个方法在所有实现map接口的map中比方法1慢20%-200%）。如果你安装了 FindBugs，它将检测并警告你这是一个低效的迭代。这个方法应该避免

### 总结

如果你只需要使用key或者value使用方法2，如果你坚持使用java的老版本（java 5 以前的版本）或者打算在迭代的时候移除entries，使用方法3。其他情况请使用1方法。避免使用4方法。

> [原问题](http://stackoverflow.com/questions/1066589/iterate-through-a-hashmap)

## 修饰符作用范围

修饰符  |   当前类  |   同 包   |   子 类   |   其他包
--- |   --- |   --- |   --- |   ---
`public`    |   √   |   √   |   √   |   √
`protected`	|   √   |   √   |   √   |   ×
`default`	|   √   |   √   |   ×   |   ×
`private`	|   √   |   ×   |   ×   |   ×

> [原问题](https://stackoverflow.com/questions/215497/in-java-difference-between-package-private-public-protected-and-private)


## 如何测试一个数组是否包含指定的值

### 使用 JDK

```java
Arrays.asList(test).contains("123");
```

### 使用 Apache Commons Lang 

```java
ArrayUtils.contains(test,"123");
```

### 使用 Java 8

```java
Stream.of(test).anyMatch(x->x=="test");

// 基本类型
IntStream.of(nums).anyMatch(x->x==4);
```

> [原问题](https://stackoverflow.com/questions/1128723/how-can-i-test-if-an-array-contains-a-certain-value)

## 重写（Override） `equals` 和 `hashCode` 方法时应考虑的问题

```java
public class Person {
    private String name;
    private int age;
    // ...

    @Override
    public int hashCode() {
        return new HashCodeBuilder(17, 31). // two randomly chosen prime numbers
            // if deriving: appendSuper(super.hashCode()).
            append(name).
            append(age).
            toHashCode();
    }

    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Person))
            return false;
        if (obj == this)
            return true;

        Person rhs = (Person) obj;
        return new EqualsBuilder().
            // if deriving: appendSuper(super.equals(obj)).
            append(name, rhs.name).
            append(age, rhs.age).
            isEquals();
    }
}
```

## 合并两个数组

### 使用 JDK

```java
// 使用非泛型
public Foo[] concat(Foo[] a, Foo[] b) {
    int aLen = a.length;
    int bLen = b.length;
    Foo[] c= new Foo[aLen+bLen];
    System.arraycopy(a, 0, c, 0, aLen);
    System.arraycopy(b, 0, c, aLen, bLen);
    return c;
}
```

```java
// 使用泛型
public <T> T[] concatenate (T[] a, T[] b) {
    int aLen = a.length;
    int bLen = b.length;

    @SuppressWarnings("unchecked")
    T[] c = (T[]) Array.newInstance(a.getClass().getComponentType(), aLen+bLen);
    System.arraycopy(a, 0, c, 0, aLen);
    System.arraycopy(b, 0, c, aLen, bLen);

    return c;
}
```

### 使用 Apache Common Lang

```java
String[] both = ArrayUtils.addAll(first, second);
```

## 通过 String 查找 Enum

定义一个枚举

```java
public enum Blah{
    A, B, C, D
}
```

通过字符串 `A` 获取 `Blah.A`

```java
Blah.valueOf("A");
```

## `finally` 总会被执行？

1. 使用 `System.exit()` ；
2. jvm 崩溃；
3. 其他线程干扰了现在运行的线程（通过 `interrupt` 方法）；

> [原问题](https://stackoverflow.com/questions/65035/does-finally-always-execute-in-java?page=1&tab=votes#tab-top)

## 创建一个文件并写入内容

### 创建文本文件

```java
PrintWriter writer = new PrintWriter("the-file-name.txt", "UTF-8");
writer.println("The first line");
writer.println("The second line");
writer.close();
```

### 创建二进制文件

```java
byte data[] = ...
FileOutputStream out = new FileOutputStream("the-file-name");
out.write(data);
out.close();
```

### 创建文本文件 Java7+

```java
List<String> lines = Arrays.asList("The first line", "The second line");
Path file = Paths.get("the-file-name.txt");
Files.write(file, lines, Charset.forName("UTF-8"));
//Files.write(file, lines, Charset.forName("UTF-8"), StandardOpenOption.APPEND);
```

### 创建二进制文件 Java7+

```java
byte data[] = ...
Path file = Paths.get("the-file-name");
Files.write(file, data);
//Files.write(file, data, StandardOpenOption.APPEND);
```

> [原问题](https://stackoverflow.com/questions/2885173/how-do-i-create-a-file-and-write-to-it-in-java)

## Java `foreach` 工作原理

```java
// add "monkey", "donkey", "skeleton key" to someList
List<String> someList = new ArrayList<String>();

for (String item : someList) {
    System.out.println(item);
}
```

相当于

```java
for (Iterator<String> i = someIterable.iterator(); i.hasNext();) {
    String item = i.next();
    System.out.println(item);
}
```

## 1927 年两个时间相减会得到奇怪结果

```java
public static void main(String[] args) throws ParseException {
    SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");  
    String str3 = "1927-12-31 23:54:07";  
    String str4 = "1927-12-31 23:54:08";  
    Date sDt3 = sf.parse(str3);  
    Date sDt4 = sf.parse(str4);  
    long ld3 = sDt3.getTime() /1000;  
    long ld4 = sDt4.getTime() /1000;
    System.out.println(ld4-ld3);
}

// 353
```

```java
String str3 = "1927-12-31 23:54:08";  
String str4 = "1927-12-31 23:54:09";  

// 1
```

```
java version "1.6.0_22"
Java(TM) SE Runtime Environment (build 1.6.0_22-b04)
Dynamic Code Evolution Client VM (build 0.2-b02-internal, 19.0-b04-internal, mixed mode)

Timezone(`TimeZone.getDefault()`):

sun.util.calendar.ZoneInfo[id="Asia/Shanghai",
offset=28800000,dstSavings=0,
useDaylight=false,
transitions=19,
lastRule=null]

Locale(Locale.getDefault()): zh_CN
```

因为1927年11月31日上海的时区变了。在1927年12月31日的午夜，时钟回调了5分52秒，所以 `1927-12-31 23:54:08` 这个时间实际上发生了两次。

[原问题](https://stackoverflow.com/questions/6841333/why-is-subtracting-these-two-times-in-1927-giving-a-strange-result)

## 计算 MD5 值

```java
byte[] byteOfData = test.getBytes(Charset.forName("utf-8"));
MessageDigest md = MessageDigest.getInstance("md5");
byte[] md5Value = md.digest(byteOfData);
```

如果需要计算的数据量大，可以先循环调用 `md.update(byteOfData)` 来加载数据，最后调用 `md.digest()` 

## 一种奇怪的内部类定义方法

```java
class A {
    int t() { return 1; }
    static A a =  new A() { int t() { return 2; } };
}
```

**测试了，好像不行**

## 如何创建泛型数组

**检查：强类型**

```java
public class GenSet<E> {

    private E[] a;

    public GenSet(Class<E> c, int s) {
        // Use Array native method to create array
        // of a type only known at run time
        @SuppressWarnings("unchecked")
        final E[] a = (E[]) Array.newInstance(c, s);
        this.a = a;
    }

    E get(int i) {
        return a[i];
    }
}
```

**未检查：弱类型**

```java
public class GenSet<E> {

    private Object[] a;

    public GenSet(int s) {
        a = new Object[s];
    }

    E get(int i) {
        @SuppressWarnings("unchecked")
        final E e = (E) a[i];
        return e;
    }
}
```

上述代码在编译期能够通过，但因为泛型擦除的缘故，在程序执行过程中，数组的类型有且仅有 `Object` 类型存在，这个时候如果我们强制转化为`E`类型的话，在运行时会有 `ClassCastException` 抛出。所以，要确定好泛型的上界。

```java
public class GenSet<E extends Foo> { // E has an upper bound of Foo

    private Foo[] a; // E erases to Foo, so use Foo[]

    public GenSet(int s) {
        a = new Foo[s];
    }

    ...
}
```

[原问题](https://stackoverflow.com/questions/529085/how-to-create-a-generic-array-in-java)

## 获取完整的堆栈信息

```java
Thread.currentThread().getStackTrace();
```

## 一行代码初始化列表

```java
List<String> list =new ArrayList<String>(){{
    add("A");
    add("B");
    add("C");
}};
```

## 初始化静态 Map

```java
public class Test {
    private static final Map<Integer, String> myMap;
    static {
        Map<Integer, String> aMap = ....;
        aMap.put(1, "one");
        aMap.put(2, "two");
        myMap = Collections.unmodifiableMap(aMap);
    }
}
```

**使用 Guava**

```java
static final Map<Integer, String> MY_MAP = ImmutableMap.of(
    1, "one",
    2, "two"
);
```

元素数量较多（超过 5 个）时，使用下面方式

```java
static final Map<Integer, String> MY_MAP = ImmutableMap.<Integer, String>builder()
    .put(1, "one")
    .put(2, "two")
    // ... 
    .put(15, "fifteen")
    .build();
```

## 给 3 个布尔变量，当其中有 2 个或者 2 个以上为 true 才返回 true

```java
a ? (b || c) : (b && c);

a && (b || c) || (b && c);

a ^ b ? c : a

(a==b) ? a : c;
```

## 输出数组最简单的方式

```java
Arrays.toString(intArray);

Arrays.deepToString(strArray);
```

区别在于 `deepToString` 更适合多维数组

## Java 源码中设计模式

[原问题](https://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries)

## 生成随机字符串

```java
import java.security.SecureRandom;
import java.util.Locale;
import java.util.Objects;
import java.util.Random;

public class RandomString {

    /**
     * Generate a random string.
     */
    public String nextString() {
        for (int idx = 0; idx < buf.length; ++idx)
            buf[idx] = symbols[random.nextInt(symbols.length)];
        return new String(buf);
    }

    public static final String upper = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

    public static final String lower = upper.toLowerCase(Locale.ROOT);

    public static final String digits = "0123456789";

    public static final String alphanum = upper + lower + digits;

    private final Random random;

    private final char[] symbols;

    private final char[] buf;

    public RandomString(int length, Random random, String symbols) {
        if (length < 1) throw new IllegalArgumentException();
        if (symbols.length() < 2) throw new IllegalArgumentException();
        this.random = Objects.requireNonNull(random);
        this.symbols = symbols.toCharArray();
        this.buf = new char[length];
    }

    /**
     * Create an alphanumeric string generator.
     */
    public RandomString(int length, Random random) {
        this(length, random, alphanum);
    }

    /**
     * Create an alphanumeric strings from a secure generator.
     */
    public RandomString(int length) {
        this(length, new SecureRandom());
    }

    /**
     * Create session identifiers.
     */
    public RandomString() {
        this(21);
    }

}
```

使用举例

```java
RandomString gen = new RandomString(8, ThreadLocalRandom.current());
```

```java
RandomString session = new RandomString();
```

```java
String easy = RandomString.digits + "ACEFGHJKLMNPQRUVWXYabcdefhijkprstuvwx";
RandomString tickets = new RandomString(23, new SecureRandom(), easy);
```

[原问题](https://stackoverflow.com/questions/41107/how-to-generate-a-random-alpha-numeric-string)

## 将堆栈信息转化为字符串

使用 Apache commons lang

```java
org.apache.commons.lang.exception.ExceptionUtils.getStackTrace(Throwable)
```

使用 JDK

```java
import java.io.StringWriter;
import java.io.PrintWriter;

// ...

StringWriter sw = new StringWriter();
PrintWriter pw = new PrintWriter(sw);
e.printStackTrace(pw);
String sStackTrace = sw.toString(); // stack trace as a string
System.out.println(sStackTrace);
```

## 在整数左边填充 0

```java
String.format("%05d", yournumber);
```

[原问题](https://stackoverflow.com/questions/473282/how-can-i-pad-an-integer-with-zeros-on-the-left)

## 从文件读取字符串

从文件里读取所有文本

```java
static String readFile(String path, Charset encoding) throws IOException 
{
    byte[] encoded = Files.readAllBytes(Paths.get(path));
    return new String(encoded, encoding);
}
```

从文件读取所有行（Java 7）

```java
List<String> lines = Files.readAllLines(Paths.get(path), encoding);
```

使用 Java 8 `BufferedReader`

```java
try (BufferedReader r = Files.newBufferedReader(path, encoding)) {
    r.lines().forEach(System.out::println);
}
```

使用 Java 8 `Stream`

```java
try (Stream<String> lines = Files.lines(path, encoding)) {
    lines.forEach(System.out::println);
}
```

[原问题](https://stackoverflow.com/questions/326390/how-do-i-create-a-java-string-from-the-contents-of-a-file)

## 使用 `URLConnection` 接受和发送 HTTP 请求

准备参数

```java
String url = "http://example.com";
String charset = "UTF-8";  // Or in Java 7 and later, use the constant: java.nio.charset.StandardCharsets.UTF_8.name()
String param1 = "value1";
String param2 = "value2";
// ...

String query = String.format("param1=%s&param2=%s", 
    URLEncoder.encode(param1, charset), 
    URLEncoder.encode(param2, charset));
```

发送带有参数的 HTTP GET 请求

```java
URLConnection connection = new URL(url + "?" + query).openConnection();
connection.setRequestProperty("Accept-Charset", charset);
InputStream response = connection.getInputStream();
```

如果没有任何参数，可以直接使用 `openStream()`

```java
InputStream response = new URL(url).openStream();
```

发送带有参数的 HTTP POST 请求

```java
URLConnection connection = new URL(url).openConnection();
connection.setDoOutput(true); // Triggers POST.
connection.setRequestProperty("Accept-Charset", charset);
connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded;charset=" + charset);

try (OutputStream output = connection.getOutputStream()) {
    output.write(query.getBytes(charset));
}

InputStream response = connection.getInputStream();
// ...
```

也可以将 `URLConnection` 强转为 `HttpURLConnection`，使用其 `setRequestMethod()` 方法，但是若要获取连接输出，仍然要设置 `setDoOutput(true)`

```java
HttpURLConnection httpConnection = (HttpURLConnection) new URL(url).openConnection();
httpConnection.setRequestMethod("POST");
// ...
```

[原问题](https://stackoverflow.com/questions/2793150/how-to-use-java-net-urlconnection-to-fire-and-handle-http-requests)


## 内存泄露代码

```java
import java.io.IOException;
import java.net.URLClassLoader;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.nio.file.Path;

/**
 * Example demonstrating a ClassLoader leak.
 *
 * <p>To see it in action, copy this file to a temp directory somewhere,
 * and then run:
 * <pre>{@code
 *   javac ClassLoaderLeakExample.java
 *   java -cp . ClassLoaderLeakExample
 * }</pre>
 *
 * <p>And watch the memory grow! On my system, using JDK 1.8.0_25, I start
 * getting OutofMemoryErrors within just a few seconds.
 *
 * <p>This class is implemented using some Java 8 features, mainly for
 * convenience in doing I/O. The same basic mechanism works in any version
 * of Java since 1.2.
 */
public final class ClassLoaderLeakExample {

  static volatile boolean running = true;

  public static void main(String[] args) throws Exception {
    Thread thread = new LongRunningThread();
    try {
      thread.start();
      System.out.println("Running, press any key to stop.");
      System.in.read();
    } finally {
      running = false;
      thread.join();
    }
  }

  /**
   * Implementation of the thread. It just calls {@link #loadAndDiscard()}
   * in a loop.
   */
  static final class LongRunningThread extends Thread {
    @Override public void run() {
      while(running) {
        try {
          loadAndDiscard();
        } catch (Throwable ex) {
          ex.printStackTrace();
        }
        try {
          Thread.sleep(100);
        } catch (InterruptedException ex) {
          System.out.println("Caught InterruptedException, shutting down.");
          running = false;
        }
      }
    }
  }
  
  /**
   * A simple ClassLoader implementation that is only able to load one
   * class, the LoadedInChildClassLoader class. We have to jump through
   * some hoops here because we explicitly want to ensure we get a new
   * class each time (instead of reusing the class loaded by the system
   * class loader). If this child class were in a JAR file that wasn't
   * part of the system classpath, we wouldn't need this mechanism.
   */
  static final class ChildOnlyClassLoader extends ClassLoader {
    ChildOnlyClassLoader() {
      super(ClassLoaderLeakExample.class.getClassLoader());
    }
    
    @Override protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException {
      if (!LoadedInChildClassLoader.class.getName().equals(name)) {
        return super.loadClass(name, resolve);
      }
      try {
        Path path = Paths.get(LoadedInChildClassLoader.class.getName()
            + ".class");
        byte[] classBytes = Files.readAllBytes(path);
        Class<?> c = defineClass(name, classBytes, 0, classBytes.length);
        if (resolve) {
          resolveClass(c);
        }
        return c;
      } catch (IOException ex) {
        throw new ClassNotFoundException("Could not load " + name, ex);
        }
    }
    }

  /**
   * Helper method that constructs a new ClassLoader, loads a single class,
   * and then discards any reference to them. Theoretically, there should
   * be no GC impact, since no references can escape this method! But in
   * practice this will leak memory like a sieve.
   */
    static void loadAndDiscard() throws Exception {
    ClassLoader childClassLoader = new ChildOnlyClassLoader();
    Class<?> childClass = Class.forName(
        LoadedInChildClassLoader.class.getName(), true, childClassLoader);
    childClass.newInstance();
    // When this method returns, there will be no way to reference
    // childClassLoader or childClass at all, but they will still be
    // rooted for GC purposes!
    }

  /**
   * An innocuous-looking class. Doesn't do anything interesting.
   */
    public static final class LoadedInChildClassLoader {
    // Grab a bunch of bytes. This isn't necessary for the leak, it just
    // makes the effect visible more quickly.
    // Note that we're really leaking these bytes, since we're effectively
    // creating a new instance of this static final field on each iteration!
    static final byte[] moreBytesToLeak = new byte[1024 * 1024 * 10];

    private static final ThreadLocal<LoadedInChildClassLoader> threadLocal
        = new ThreadLocal<>();
    
    public LoadedInChildClassLoader() {
        // Stash a reference to this class in the ThreadLocal
        threadLocal.set(this);
        }
    }
}

```