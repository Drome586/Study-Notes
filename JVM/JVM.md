## https://www.yuque.com/u21195183/jvm

## LINUX命令

**CPU进程相关**

1. **top**
2. **ps -ef | grep 所需要进程**  （grep：筛选）
3. **kill 进程**   
4. **kill -9** 强制杀死进程



## JVM内存结构

```java
//反编译
javap -v demo.class     -v表示显示具体信息
```

## StringTable

字符串加载到串池并不是一下子加载进去，而是走到代码的哪一步将哪一步的字符串加载到串池里面，之后有重复的字符串的话不会加载到串池。

```java
String s1= "a";
String s2= "b";
String s3=s1+s2;	//相当于	new StringBuilder().append("a").append("b").toString();
```

```java
public static void main(String[] args){
    
    //new 出来的在堆里，并没有在串池中；
    String s = new String("a") + new String("b");
    
  	//将这个字符串对象尝试放入到串池中，如果有则不会放入，如果没有则放入串池，会把串池中的对象返回；
    String s2 = s.intren();   
    
}

//例子
String str1 = new String("a") + new String("b");
String str2 = "ab";
String str3 = str1.intern();

System.out.print(str2 == str3) //false;
    
如果	String str3 = str1.intern();
	 String str2 = "ab";
	System.out.print(str2 == str3)	//true
```

### StringTable位置

1. **1.6之前是存放在方法去的，1.8开始存放在堆中**。

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230327224942498.png" alt="image-20230327224942498" style="zoom: 50%;" />

2.**StringTable是可以进行垃圾回收的**

如果有大量的地址字符串要存入到常量池中，可以通过intern()来减少堆内存的使用



## 直接内存(不受JVM内存回收管理)

**常见于NIO操作，用作数据缓冲区；分配回收成本较高，但读写性能极高；不受JVM内存回收管理**

1. 直接内存与二次缓存之间的区别

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230327233437494.png" alt="image-20230327233437494" style="zoom: 67%;" />

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230327233458131.png" alt="image-20230327233458131" style="zoom: 67%;" />

2. ```java
   //通过allocateDirect（）来分配direct Memory的大小
   ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_100Mb);
   ```

### 直接内存释放原理

通过反射获得底层**unsafe**对象，一个是**allocateMemory()**来分配内存，一个是**freeMemory()**来释放内存

回收时主动调用了unsafe的**freeMemory（）**方法来回收。

ByteBuﬀer 的实现类内部，使用了 Cleaner （虚引用）来监测 ByteBuﬀer 对象，一旦 ByteBuﬀer 对象被垃圾回收，那么就会由 ReferenceHandler 线程通过 Cleaner 的 clean 方法调 用 freeMemory 来释放直接内存

## 垃圾回收（堆中的垃圾回收）

#### 1.判断对象存活

​	引用计数法和可达性分析法和四种引用

```java
//软引用分析,内存只给了4M，每次循环加1MB,如果不使用弱引用的话，是会OOM的，若加上了弱引用，那么当超过内存时会自动进行回收，下面代码不会报错，但是加入进去之后再重新遍历会发现前面几个引用都是空（NULL）只保留了最后一个
List<SoftReference<byte[]>> list = new ArrayList<>();
for(int i = 0;i < 5;i++){
    SoftReference<byte[]> ref= new SoftReference<>(new byte[4MB]);
    list.add(ref);
}
for(){
    ....
}

```



#### 2.垃圾回收算法

1. 标记清除算法

2. 标记整理法

3. 复制法

4. 分代回收法

   ```java
   新生代（伊甸园，幸存区FROM，幸存区To）	老年代
   //一般来说新生代，伊甸园跟幸存区的比例是8：1：1
       
   //若发生再线程之间的话
       当以个线程抛出OOM异常，它所占据的内存资源并不会马上被全部释放掉，而是失去引用，可以被垃圾回收，从而不会影响其他线程的运行。
   ```

   

**相关JVM堆中的指令**

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230328152458325.png" alt="image-20230328152458325" style="zoom: 67%;" />

#### 垃圾回收器

1. 串行

   1. 单线程，堆内存较小，适合个人电脑
   2. **Serial**
   3. **SerialOld**

2. 吞吐量优先

   1. 多线程，堆内存较大，多核CPU
   2. **ParalleGC**
   3. **ParallelOldGC**

3. 响应时间优先

   1. 多线程，堆内存较大，多核CPU

   2. **CMS（concurrent Mark Swap）**；与上面ParalleGC不同的是，ParalleGC并行进行垃圾回收，此时stop the world 不允许用户线程同时进行，但是CMS能够允许GC与用户线程同时并发执行。

   3. CMS流程

      <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230328191005837.png" alt="image-20230328191005837" style="zoom: 67%;" />

​		4.**G1（Garbage first 默认版本jdk9以后）**；jdk9之前要手动启动，同时注重吞吐量和低延迟，默认的暂停目标是200ms（strop the world）。适用于超大堆内存，会将堆划分为多个大小相等的Region，整体上是**标记+整理**算法，两个区域之间是复制算法。

## 语法糖

1. **默认构造**：构造器里面super();

2. **自动拆装箱**

3. **泛型擦除**：List<Integer>   其实再list.add(i)的时候是list.add(Object i);

4. **泛型反射**

5. **可变参数**

   ```java
   public static void kebian(String... args){
       
   }
   //如果调用了kebian()无参等价于kebian(new String[]{}),创建了一个空的数组，而不会传递null进去。
   
   ```

6. **foreach**

7. **switch - string**：配合字符串使用

8. **switch-enum**：配合枚举类使用

9. **枚举类**

   ```java
   enum{
       MALE,FEMALE
   }
   ```

## 类加载

1. 加载
   1. 通过类名获取类的二进制流
   2. 将二进制流的静态存储结构转为方法区的运行时数据结构
   3. 再堆中为类生成一个class对象
2. 验证：验证该class文件中的字节流信息符合虚拟机的要求，不会威胁到JVM的安全
3. 准备：为class对象的静态变量分配内存，初始化其初始值
   1. static变量的分配空间和赋值是两个步骤，分配空间再准备阶段完成，赋值再初始化阶段完成
   2. 如果static变量是finall的基本类型，那么编译阶段值就确定了，赋值在准备阶段完成
   3. 如果static 变量是finall的但属于引用类型，那么赋值也会在初始化阶段完成
4. 解析
5. 初始化

## synchronized

1. **可见性问题**:run改为false之后，程序并未停止下来原因是：

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329215251522.png" alt="image-20230329215251522" style="zoom: 80%;" />

   

​	原因:

​	<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329215406251.png" alt="image-20230329215406251" style="zoom: 67%;" />

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329215433854.png" alt="image-20230329215433854" style="zoom: 67%;" />

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329215450760.png" alt="image-20230329215450760" style="zoom: 67%;" />

2. **处理可见性问题**
   1. **volatile（易变关键字）**：他可以用来修饰成员变量和静态成员变量，他可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取他的值，线程操作volatile变量都是直接操作主存。

​			 <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329215817766.png" alt="image-20230329215817766" style="zoom:67%;" />

​		2.volatile不能保证原子性。  适合一个写线程，多个读线程。

3. **happens-before**:规定了哪些写操作对其他线程的读操作可见，他是可见性与有序性的一套规律的总结。

## CAS

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230329224859551.png" alt="image-20230329224859551" style="zoom:67%;" />
