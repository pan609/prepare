## Prepare
### java
#### 常规基础问题
##### String，StringBuilder,StringBuffer
- 都是final类，都不允许被继承；
- String类长度是不可变的，StringBuffer和StringBuilder类长度是可以改变的；
- StringBuffer类是线程安全的，StringBuilder不是线程安全的；
- **String**是不可变的对象，因此每次在对String类进行改变的时候都会生成一个新的string对象，然后将指针指向新的string对象，所以经常要改变字符串长度的话不要使用string，因为每次生成对象都会对系统性能产生影响，特别是当内存中引用的对象多了以后，JVM的GC就会开始工作，性能就会降低；
- 使用**StringBuffer**类时，每次都会对StringBuffer对象本身进行操作，而不是生成新的对象并改变对象引用，所以多数情况下推荐使用StringBuffer，特别是字符串对象经常要改变的情况
- **StringBuilder**是5.0新增的，此类提供一个与 StringBuffer 兼容的 API，但不保证同步。该类被设计用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。如果可能，建议优先采用该类，因为在大多数实现中，它比 StringBuffer 要快。两者的方法基本相同
- 在某些情况下，String对象的字符串拼接其实是被Java Compiler编译成了StringBuffer对象的拼接，所以这些时候String对象的速度并不会比StringBuffer对象慢
- 基本原则：如果要操作少量的数据，用String ；单线程操作大量数据，用StringBuilder ；多线程操作大量数据，用StringBuffer。
- 不要使用String类的”+”来进行频繁的拼接，因为那样的性能极差的，应该使用StringBuffer或StringBuilder类
- StringBuilder一般使用在方法内部来完成类似”+”功能，因为是线程不安全的，所以用完以后可以丢弃。StringBuffer主要用在全局变量中
##### [equals and ==](https://www.cnblogs.com/qianguyihao/p/3929585.html)
- 基础数据类型，== 比较的是他们的值
- 引用数据类型，== 比较的是他们在内存中的存放地址（堆内存地址）
- 对于引用数据类型，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。因为每new一次，都会重新开辟堆内存空间。
- equals()这个方法的初始默认行为是比较对象的内存地址值，在一些类库当中这个方法被重写了，如String、Integer、Date，一般都是用来比较对象的成员变量值是否相同
##### static
- static关键字可以用来修饰代码块表示静态代码块，修饰成员变量表示全局静态成员变量，修饰方法表示静态方法，不能修饰普通类，可以修饰内部类
- static变量和普通成员变量区别
    - static变量属于类，不单属于任何对象；普通成员变量属于某个对象
    - static变量位于方法区；普通成员变量位于堆区。
    - static变量生命周期与类的生命周期相同；普通成员变量和其所属的对象的生命周期相同。
    - 在对象序列化时（Serializable），static变量会被排除在外（因为static变量是属于类的，不属于对象）
- 静态内部类
    - 非静态内部类对象持有外部类对象的引用（编译器会隐式地将外部类对象的引用作为内部类的构造器参数）；而静态内部类对象不会持有外部类对象的引用
- 静态方法
    - 在静态方法中不能访问类的非静态成员变量和非静态成员方法，因为非静态成员方法/变量都是必须依赖具体的对象才能够被调用
    - 非静态方法是可以访问静态方法与静态成员变量的。
- 静态变量
    - 静态变量属于类，在内存中只有一个复制，只要静态变量所在的类被加载，这个静态变量就会被分配空间，因此就可以被使用了
##### final，finally，finalize
- final
    -  如果一个类被声明为final,意味着它不能再派生新的子类，不能作为父类被继承；
    - 将变量或方法声明为final,可以保证他们使用中不被改变。被声明为final的变量必须在声明时给定初值，而以后的引用中只能读取，不可修改，被声明为final的方法也同样只能使用，不能重载。
- finally
    - 在异常处理时提供finally块来执行清楚操作。如果抛出一个异常，那么相匹配的catch语句就会执行，然后控制就会进入finally块，如果有的话。
    - finally的代码一定执行吗：不一定，finally块的执行时间点在try和catch中return语句的表达式值计算之后返回之前。 不论try和catch代码块中有没有return语句，执行顺序都是先计算try或catch中return语句的表达式的值并暂存，然后执行finally代码块。若finally代码块中无return，则返回之前try或catch中暂存的return语句的表达式的值；若finally代码块中有return，则直接就地返回。
- [finalize](https://houbb.github.io/2019/04/16/java-base-03-finalize-03)
    - 垃圾回收线程调用 finalize()
    - finalize()方法在Object中进行了定义，用于在对象“消失”时，由JVM进行调用用于对对象进行垃圾回收；一旦垃圾回收器准备释放对象所占的内存空间, 如果对象覆盖了finalize()并且函数体内不能是空的, 就会首先调用对象的finalize(), 然后在下一次垃圾回收动作发生的时候真正收回对象所占的空间
    - JVM始终只调用一次. 无论这个对象被垃圾回收器标记为什么状态, finalize()始终只调用一次. 但是程序员在代码中主动调用的不记录在这之内
    - 为了防止finalize函数抛出的异常影响到垃圾回收线程的运作，垃圾回收线程会在调用每一个finalize函数时进行try catch，如果捕获到异常，就直接丢弃，然后接着处理下一个失效对象的finalize函数
##### sleep 和 wait
- sleep方法是定义在Thread上，wait方法是定义在Object上
- sleep不会释放锁，wait会释放锁
- sleep可以使用在任何代码块，wait必须在同步方法或同步代码块执行
- 进入wait状态的线程能够被notify和notifyAll线程唤醒，但是进入sleeping状态的线程不能被notify方法唤醒。
- wait通常有条件地执行，线程会一直处于wait状态，直到某个条件变为真。但是sleep仅仅让你的线程进入睡眠状态
##### 重写重载
- 重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变(父子类间的多态)
- 重载是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同（多态具体形式)
##### 抽象类和接口
- 抽象类 extends 继承，接口 implement 实现
- 抽象类可以有构造器，而接口不能有构造器
- 一个子类只能继承一个父类，但是可以存在多个接口。
- 抽象类可以有非抽象方法，接口只能有抽象方法
- 接口的方法变量被隐式 public修饰，抽象类中抽象方法不能用 private
##### 内部类和静态内部类
- 只能在内部类中定义静态类
- 静态类的方法可以是静态的方法也可以是非静态的方法，静态的方法可以在外层通过静态类调用，而非静态的方法必须要创建类的对象之后才能调用
- 只能引用外部类的static成员变量（也就是类变量）
- 如果一个内部类不是被定义成静态内部类，那么在定义成员变量或者成员方法的时候，是不能够被定义成静态的
- 静态内部类通过外部类本身创建，内部类通过外部类实例创建
##### 值传递和引用传递
- 值传递：方法调用时，实际参数的值被传递给对应的形式参数，函数接收的是原始值的一个copy， 此时内存中存在两个相等的基本类型，即实际参数和形式参数，后面方法中的操作 都是对形参这个值的修改，不影响实际参数的值
- 引用传递/址传递：方法调用时，实际参数的地址被传递给方法中相对应的形式参数，函数接收的是原始值的内存地址。在方法执行中， 形参和实参内容相同，指向同一块内存地址，方法执行中对引用的操作将会影响到实际对象
- java 是共享对象传递：先获取到实际参数的地址，然后将其复制，并把该地址的拷贝传递给被调函数的形式参数。因为参数的地址都指向同一个对象，所以我们称也之为"传共享对象"，所以，如果在被调函数中改变了形式参数的值，调用者是可以看到这种变化的
##### Object 中的方法
#### 数据类型
##### 基本数据类型(Java基本类型存储在栈)
- byte：8位，最大存储数据量是255，存放的数据范围是-128~127之间。
- short：16位，最大数据存储量是65536，数据范围是-32768~32767之间
- int：32位，最大数据存储容量是2的32次方减1，数据范围是负的2的31次方到正的2的31次方减1。
- long：64位，最大数据存储容量是2的64次方减1，数据范围为负的2的63次方到正的2的63次方减1。
- float：32位，数据范围在3.4e-45~1.4e38，直接赋值时必须在数字后加上f或F
- double：64位，数据范围在4.9e-324~1.8e308，赋值时可以加d或D也可以不加。
- boolean：只有true和false两个取值。
- char：16位，存储Unicode码，用单引号赋值。
##### 引用数据类型
- 引用数据类型分3种：类，接口，数组；
- 引用类型常见的有：String，StringBuffer，ArrayList，HashSet，HashMap等。
- 在堆中进行分配,堆的读写速度远不及栈
##### 自动拆装箱
- 自动装箱就是Java自动将原始类型值转换成对应的对象，比如将int的变量转换成Integer对象，这个过程叫做装箱，反之将Integer对象转换成int类型值，这个过程叫做拆箱。
- 自动装箱时编译器调用valueOf将原始类型值转换成对象，同时自动拆箱时，编译器通过调用类似intValue(),doubleValue()这类的方法将对象转换成原始类型值。
#### 集合
- Java集合大致可以分为Set、List、Queue和Map四种体系，其中Set代表无序、不可重复的集合；List代表有序、重复的集合；而Map则代表具有映射关系的集合，Java 5 又增加了Queue体系集合，代表一种队列集合实现.Java的集合类主要由两个接口派生而出：Collection和Map,Collection和Map是Java集合框架的根接口。
![Collection](https://upload-images.jianshu.io/upload_images/3985563-e7febf364d8d8235.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)
![map集合](https://upload-images.jianshu.io/upload_images/3985563-06052107849a7603.png?imageMogr2/auto-orient/strip|imageView2/2/w/835/format/webp)
##### 使用Iterator遍历集合元素
- Iterator接口经常被称作迭代器，它是Collection接口的父接口。但Iterator主要用于遍历集合中的元素。Iterator接口中主要定义了2个方法：
    - hasNext:如果有下一个元素仍可迭代，返回 true
    - next：返回下一个可迭代元素
##### set
- Set集合与Collection集合基本相同，没有提供任何额外的方法。实际上Set就是Collection，只是行为略有不同（Set不允许包含重复元素）。Set集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set集合中，则添加操作失败，add()方法返回false，且新元素不会被加入。
- HashSet
    - 特点 
      - 不能保证元素的排列顺序，顺序可能与添加顺序不同，顺序也有可能发生变化
      - HashSet不是同步的，如果多个线程同时访问一个HashSet，则必须通过代码来保证其同步
      - 集合元素可以是 null
      - HashSet判断两个元素是否相等的标准也是其一大特点。HashSet集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。
    - equals()和hashCode()
      - 重写这两个方法判断两个元素是否相等
    - HashSet判断两个元素相等的标准：两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。
    - LinkedHashSet
      - LinkedHashSet是HashSet对的子类，也是根据元素的hashCode值来决定元素的存储位置，同时使用链表维护元素的次序，使得元素是以插入的顺序来保存的。当遍历LinkedHashSet集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素。但是由于要维护元素的插入顺序，在性能上略低与HashSet，但在迭代访问Set里的全部元素时有很好的性能。
- TreeSet（线程不安全）
    - TreeSet是SortedSet接口的实现类，正如SortedSet名字所暗示的，TreeSet可以确保集合元素处于排序状态。此外，TreeSet还提供了几个额外的方法。
    ```
      comparator():返回对此 set 中的元素进行排序的比较器；如果此 set 使用其元素的自然顺序，则返回null。
      first():返回此 set 中当前第一个（最低）元素。
      last(): 返回此 set 中当前最后一个（最高）元素。
      lower(E e):返回此 set 中严格小于给定元素的最大元素；如果不存在这样的元素，则返回 null。
      higher(E e):返回此 set 中严格大于给定元素的最小元素；如果不存在这样的元素，则返回 null。
      subSet(E fromElement, E toElement):返回此 set 的部分视图，其元素从 fromElement（包括）到 toElement（不包括）。
      headSet(E toElement):返回此 set 的部分视图，其元素小于toElement。
      tailSet(E fromElement):返回此 set 的部分视图，其元素大于等于 fromElement。
    ```
    - 排序方式
      - 自然排序(todo)
      - 定值排序(todo)
- EnumSet(todo)
##### List
- List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List集合允许使用重复元素，可以通过索引来访问指定位置的集合元素 。List集合默认按元素的添加顺序设置元素的索引，例如第一个添加的元素索引为0，第二个添加的元素索引为1......
List作为Collection接口的子接口，可以使用Collection接口里的全部方法。而且由于List是有序集合，因此List集合里增加了一些根据索引来操作集合元素的方法。
- ArrayList
    - 遍历
      - 迭代器
      - for 循环
      - 随机访问
    - ArrayList是一个动态扩展的数组，线程不安全
- LinkedList（线程不安全）
    - LinkedList类是List接口的实现类——这意味着它是一个List集合，可以根据索引来随机访问集合中的元素。除此之外，LinkedList还实现了Deque接口，可以被当作成双端队列来使用，因此既可以被当成“栈"来使用，也可以当成队列来使用。
    - LinkedList的实现机制与ArrayList完全不同。ArrayList内部是以数组的形式来保存集合中的元素的，因此随机访问集合元素时有较好的性能；而LinkedList内部以链表的形式来保存集合中的元素，因此随机访问集合元素时性能较差，但在插入、删除元素时性能比较出色。
- 使用
    - 对于需要快速插入，删除元素，应该使用LinkedList。
    - 对于需要快速随机访问元素，应该使用ArrayList。
    - 对于“单线程环境” 或者 “多线程环境，但List仅仅只会被单个线程操作”，此时应该使用非同步的类(如ArrayList)。对于“多线程环境，且List可能同时被多个线程操作”，此时，应该使用同步的类(如Vector)。
##### Map
- HashMap与Hashtable的区别
    - Hashtable是一个线程安全的Map实现，但HashMap是线程不安全的实现，所以HashMap比Hashtable的性能好一些；但如果有多个线程访问同一个Map对象时，是盗用Hashtable实现类会更好。
    - Hashtable不允许使用null作为key和value，如果试图把null值放进Hashtable中，将会引发NullPointerException异常；但是HashMap可以使用null作为key或value。
- HashMap的构造函数
```
// 默认构造函数。
HashMap()
// 指定“容量大小”的构造函数
HashMap(int capacity)
// 指定“容量大小”和“加载因子”的构造函数
HashMap(int capacity, float loadFactor)
// 包含“子Map”的构造函数
HashMap(Map<? extends K, ? extends V> map)
```
    - 默认加载因子是 0.75
- [HashMap线程安全](https://www.cnblogs.com/jmcui/p/11422083.html)
    - hashmap 是线程不安全的,JDK1.7是数组+链表，多线程背景下，在数组扩容的时候，存在 Entry 链死循环和数据丢失问题。JDK1.8是数组+链表+红黑树,优化了 1.7 中数组扩容的方案，解决了 Entry 链死循环和数据丢失问题。但是多线程背景下，put 方法存在数据覆盖的问题。
    - hashtable使用 synchronize 保证线程安全,但在线程竞争激烈的情况下，效率低下,因为当一个线程访问 HashTable 的同步方法，其他线程也访问 HashTable 的同步方法时，会进入阻塞或轮询状态。如线程1使用 put 进行元素添加，线程2不但不能使用 put 方法添加元素，也不能使用 get 方法来获取元素，所以竞争越激烈效率越低。
    - ConcurrentHashMap线程安全，JDK 1.7 ConcurrentHashMap 采用Segment数组 + HashEntry数组实现。 Segment 是一种可重入锁（ReentrantLock），在 ConcurrentHashMap 里扮演锁的角色；HashEntry 则用于存储键值对数据。一个 ConcurrentHashMap 里包含一个 Segment 数组，一个 Segment 里包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素。JDK 1.8 ConcurrentHashMap 采用数组 + 链表 + 红黑树的方式实现，结构基本上和 1.8 中的 HashMap 一样，不过大量的利用了 volatile，final，CAS 等 lock-free 技术来减少锁竞争对于性能的影响，从而保证线程安全性。


- TreeMap
    - 红黑树

#### 设计模式
##### 具体设计模式
- 单例模式
    ```java
        // DCL
        public class Singleton {  
            private volatile static Singleton singleton;  
            private Singleton (){}  
            public static Singleton getSingleton() {  
            if (singleton == null) {  
                synchronized (Singleton.class) {  
                if (singleton == null) {  
                    singleton = new Singleton();  
                }  
                }  
            }  
            return singleton;  
            }  
        }
    ```
    - volatile 作用：在 new Singleton()时禁止指令重排序
    - 两次 synchronized 作用：![](https://img-blog.csdnimg.cn/20190328172048440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNzI3MjE4,size_16,color_FFFFFF,t_70)第一个 if 解决一个雷只能实例化一个对象，如果两个线程同时通过了第一个 if 判断，第一个if判断避免了其他无用线程竞争锁来造成性能浪费，第二个if判断能拦截除第一个获得对象锁线程以外的线程,如果不加第二次判空，我们考虑下线程A，线程B都阻塞在了获取锁的步骤上，其中A获得锁---实例化了对象----释放锁，之后B---获得锁---实例化对象
    ```JAVA
    // lazy
    public class Singleton {  
        private static Singleton instance;  
        private Singleton (){}  
        public static synchronized Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
        }  
    }
    ```
    - volatile 不能保证原子性，可以保证可见性有序性,保证有序性是禁止指令重排序
- builder模式(非线程安全)
    - 应用：AlertDialog
    - 优点：客户端代码的可用性和可读性得到了大大提高。与此同时，构造函数的参数数量明显减少调用起来非常直观
- [观察者模式](https://www.jianshu.com/p/8f32da74cd8b)
    - 应用：对控件的监听 listener
- 工厂
##### [六大设计原则](http://www.uml.org.cn/sjms/201211023.asp#2)
- 单一职责原则
- 开闭原则
- 里氏替换原则
- 依赖倒置原则
- 接口隔离原则
- 迪米特法则 
##### [代理模式](https://juejin.cn/post/6925692200802058254#heading-4)
- 代理模式:简单的来讲其实我们的目的就是为一个业务类提供一个代理对象来完成业务逻辑
- 构成
    - 共同的行为（常用作接口或抽象类）
    - 委托对象（真实对象，向代理对象提供委托）
    - 代理对象（代理对象，负责实现真实对象的委托）
- 遵循原则
    - 委托类与代理类又共同行为或相似的行为
    - 代理类负责针对委托类进行增强和管理
- 静态代理
    - 是指程序的设计者在程序运行前就已经为委托对象创建好了代理对象，换句话说就是我们在启动服务之前，代理对象的.class文件就已经生成了。
- 动态代理（Dynamic Proxy）
    - 动态代理是java提供的一种运行时加载代理技术，相比于静态代理，动态代理在为对象提供代理服务的时候更加灵活，动态代理类可以在程序运行时由java通过反射动态的生成。简单的来说动态代理是在实现阶段不用关系我去代理谁，而在运行阶段才会去指定委托对象。常见的动态代理有两种实现形式：接口代理 和 cglib代理。
    - 接口代理（jdk 代理）
        - 接口代理方式是java自带的一种动态代理模式,其通过Proxy类的静态方法newProxyInstance返回一个接口的代理实例已达到针对不同的委托类都能生成对应的代理类的目的。
    - Cglib代理
        - Cglib代理模式采用了非常底层的字节码技术来实现动态代理。其不要求委托类实现某一个接口，而是通过字节码技术为委托类创建一个子类，并通过拦截父类的方法调用的形式来动态的将代理逻辑织入到方法中，但是由于采用的是创建子类集成的形式，父类不可以用final来进行类修饰。这里需要注意的是cglib并不是java自带的API，因此我们想要使用前需要导入相关的依赖JAR包
#### 锁机制