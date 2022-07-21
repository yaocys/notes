# String

## String类

* String类由final修饰，不能被继承
* String类所有方法都没有改变字符串本身的值，而是返回了一个新的对象
* Java 9前采用`char[]`数组，后采用`byte[]`数组
>  出于安全和性能考虑

### 常用方法

1. `char charAt(int index)`：返回指定索引处的字符

2. `String substring(int beginIndex, int endIndex)`：从此字符串中截取出一部分子字符串

3. `String[] split(String regex)`：以指定的规则将此字符串分割成数组

4. `String trim()`：删除字符串前导和后置的空格

5. `int indexOf(String str)`：返回子串在此字符串首次出现的索引

   `int lastIndexOf(String str)`：返回子串在此字符串最后出现的索引

6. `String toUpperCase()`：将此字符串中所有的字符大写

   `String toLowerCase()`：

7. `String replaceFirst(String regex, String replacement)`：用指定字符串替换第一个匹配的子串

   `String replaceAll(String regex, String replacement)`：用指定字符串替换所有的匹配的子串

## String和StringBuffer

1. String和StringBuffer

   String是不可变类

   而StringBuffer是一个字符序列可变的字符串，通过其成员方法（如`append()`）操作，并最终可通过`toString()`方法将其转换为一个字符串

### StringBuffer和StringBuilder

1. StringBuffer和StringBuilder的区别

   StringBuffer是线程安全的，而StringBuilder是非线程安全的，所以StringBuilder性能略高。

### 创建字符串

使用字符串时，new和“”两种方式

1. `String a = "abc"`

   JVM使用常量池来管理字符串直接量。

   在执行这句话时，JVM会先检查常量池（方法区中）中是否已经存有"abc"，若没有则将"abc"存入常量池，否则就复用常量池中已有的"abc"，将其引用赋值给变量a。

2. `new String("abc")`

   在执行这句话时，JVM会先使用常量池来管理字符串直接量，即将"abc"存入常量池。

   然后再创建一个新的String对象，这个对象会被保存在==堆==内存中。并且，堆中对象的数据会指向常量池中的直接量。

一般建议使用直接量的方式创建字符串

### 字符串拼接

   1. \+ 运算符：如果拼接的都是字符串直接量

      > * 如果拼接的都是字符串直接量，则在编译时编译器会将其直接优化为一个完整的字符串，和直接写一个完整的字符串是一样的，所以效率非常的高
      > * 如果拼接的字符串中包含变量，则在编译时编译器采用StringBuilder对其进行优化，即自动创建StringBuilder实例并调用其append()方法，将这些字符串拼接在一起，效率也很高。

   2. StringBuilder：如果拼接的字符串中包含变量，并不要求线程安全

   3. StringBuffer：如果拼接的字符串中包含变量，并且要求线程安全

      > - StringBuilder/StringBuffer都是==可变对象==，都有字符串缓冲区，缓冲区的容量在创建对象时确定，并且默认为16。当拼接的字符串超过缓冲区的容量时，会触发缓冲区的扩容机制，即缓冲区加倍。
      > - 可以指定缓冲区容量，避免频繁扩容降低拼接性能

   4. String类的concat方法：如果只是对两个字符串进行拼接，并且包含变量，则适合使用concat方法

      > * 先创建一个足以容纳待拼接的两个字符串的字节数组，然后先后将两个字符串拼到这个数组里，最后将此数组转换为字符串。
      > * 在拼接大量字符串的时候，concat方法的效率低于StringBuilder