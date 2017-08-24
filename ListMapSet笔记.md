# 1. Collection接口

Collection接口下的：

## 1. List

> 接口， 代表有序， 可重复的集合， 列表

### 1. ArrayList 

Class， 数组， 随机访问， 没有同步， 线程不安全

### 2. Vector

Class 数组， 同步， 线程安全

### 3. LinkedList 

Class， 链表， 插入删除， 没有同步

### 4. Stack

class

## 2. Set

> 接口， 不能包含重复元素， 仅接收一次并做内部排序， 集

### 1. HashSet

Class

### 2. LinkedHashSet 

Class

### 3. TreeSet

Class



# 2. Map 接口

Map接口下的：

## 1. Map

接口， 集合映射

### 1. HashMap

Class 不同步， 线程不安全， 除了不同和和允许使用null之外， 与 HashTable 大致相同

### 2. HashTable

Class 同步， 线程安全， 不允许实施null键值





# 数组的特点

1. 效率高， 但容量固定且无法动态改变， array还有一个缺点是， 无法判断其中实际存储有多少元素， length只是告诉我们array的容量

2. Java中有一个Arrays类，专门用来操作array。

   ​     arrays中拥有一组static函数，

   ​     equals()：比较两个array是否相等。array拥有相同元素个数，且所有对应元素两两相等。

   ​     fill()：将值填入array中。

   ​     sort()：用来对array进行排序。

   ​     binarySearch()：在排好序的array中寻找元素。

   ​     System.arraycopy()：array的复制。

## 数组Array和集合的区别：

1. 数组是大小固定的，并且同一个数组只能存放类型一样的数据（基本类型/引用类型）
2.  JAVA集合可以[存储](http://www.storworld.com/)和操作数目不固定的一组数据。
3.  若程序时不知道究竟需要多少对象，需要在空间不足时自动扩增容量，则需要使用容器类库，array不适用。

## Set Map List的区别

> 1. Set 其中的值**不允许有重复**， **无序的数据结构**， 只能通过游标来取值
> 2. List 其中的值**允许有重复**， 因为其为**有序的数据结构，** 可以通过下标来取值
> 3. Map 成对的数据结构， 键值必须具有唯一性， （键不能相同， 否则值替换）

> 1. Set 对每个对象只接收一次， 并使用自己内部的排序方法， （通常， 只需关心某个元素是否属于Set， 而不需要关心它的存储顺序）
> 2. List 按照对象进入的顺序存储对象， 不做排序或编辑操作
> 3. Map同样对每一个元素保存一份， 但这是基于键的， Map也有内置的排序， 因而不用关心元素的添加顺序， 如果元素的添加顺序很重要， 就应该使用 LinkedHashSet 或 LinkedHashMap

**Collection是对象集合， Collection有两个子接口， List 和 Set**

**ArrayList ， Vector ， LinkedList 是 List 的实现类**

ArrayList 是线程不安全的， Vector 是线程安全的，这两个类底层都是由数组实现的

LinkedList 是线程不安全的，底层是由链表实现的  

Map 是键值对集合

HashTable 和 HashMap 是 Map 的实现类 

HashTable 是线程安全的，不能存储 null 值

HashMap 不是线程安全的，可以存储 null 值



## Collections类和Collection接口

Collections是针对集合类的一个帮助类，他提供一系列静态方法实现对各种集合的搜索、排序、线程安全化等操作。

Collection是最基本的集合接口，一个Collection代表一组Object，即Collection的元素（Elements）。一些 Collection允许相同的元素而另一些不行。一些能排序而另一些不行。Java SDK不提供直接继承自Collection的 类，Java SDK提供的类都是继承自Collection的“子接口”如List和Set。

所有实现 Collection 接口的类都必须提供两个标准的构造函数：无参数的构造函数用于创建一个空的 Collection ，有一个 Collection 参数的构造函数用于创建一个新的 Collection ，这个新的 Collection 与传入的 Collection 有相同的元素。后一个构造函数允许用户复制一个 Collection 。

**集合类的遍历:遍历通用Collection:**

　　如何遍历 Collection 中的每一个元素？不论 Collection 的实际类型如何，它都支持一个 iterator() 的方法，该方法返回一个迭代子，使用该迭代子即可逐一访问 Collection 中每一个元素。典型的用法如下：

```
    Iterator it = collection.iterator(); // 获得一个迭代子  
    　　while(it.hasNext()) {  
    　　　Object obj = it.next(); // 得到下一个元素  
    }  
```

由 Collection 接口派生的两个接口是 List 和 Set 。 List 按对象进入的顺序保存对象，不做排序或编辑操作。 Set 对每个对象只接受一次，并使用自己内部的排序方法 ( 通常，你只关心某个元素是否属于 Set, 而不关心它的顺序--
 否则应该使用 List) 。



##  List接口，有序可重复的集合

实际上有两种List: 一种是基本的ArrayList,其优点在于随机访问元素，另一种是更强大的LinkedList,它并不是为快速随机访问设计的，而是具有一套更通用的方法。 

**List :**  次序是List最重要的特点：它保证维护元素特定的顺序。List为Collection添加了许多方法，使得能够向List中间插入与移除元素（这只推荐LinkedList使用。）一个List可以生成ListIterator,使用它可以从两个方向遍历List,也可以从List中间插入和移除元素。

1.  ArrayList类

   1. ArrayList实现了可变大小的数组。它允许所有元素，包括null。ArrayList没有同步。
   2. size，isEmpty，get，set方法运行时间为常数。但是add方法开销为分摊的常数，添加n个元素需要O(n)的时间。其他的方法运行时间为线性。
   3.  每个ArrayList实例都有一个容量（Capacity），即用于存储元素的数组的大小。这个容量可随着不断添加新元素而自动增加，但是增长[算法](http://lib.csdn.net/base/datastructure) 并没有定义。当需要插入大量元素时，在插入前可以调用ensureCapacity方法来增加ArrayList的容量以提高插入效率。
   4.  和LinkedList一样，ArrayList也是非同步的（unsynchronized）。
   5. 由数组实现的List。允许对元素进行快速随机访问，但是向List中间插入与移除元素的速度很慢。ListIterator只应该用来由后向前遍历ArrayList,而不是用来插入和移除元素。因为那比LinkedList开销要大很多。

2. Vector类

   　Vector非常类似ArrayList，但是Vector是同步的。由Vector创建的Iterator，虽然和ArrayList创建的Iterator是同一接口，但是，因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此必须捕获该异常。

3. LinkedList 类

   LinkedList实现了List接口，允许null元素。此外LinkedList提供额外的get，remove，insert方法在inkedList的首部或尾部。如下列方法：addFirst(), addLast(), getFirst(), getLast(), removeFirst() 和 removeLast(), 这些方法 （没有在任何接口或基类中定义过）。这些操作使LinkedList可被用作堆栈（stack），队列（queue）双向队列（deque）。

   　　注意LinkedList没有同步方法。如果多个线程同时访问一个List，则必须自己实现访问同步。一种解决方法是在创建List时构造一个同步的List：

   ```
   　　List list = Collections.synchronizedList(new LinkedList(...));
   ```

4. Stack类

   Stack继承自Vector，实现一个后进先出的堆栈。Stack提供5个额外的方法使得Vector得以被当作堆栈使用。基本的push和pop方法，还有peek方法得到栈顶的元素，empty方法[测试](http://lib.csdn.net/base/softwaretest)堆栈是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈。

   用法： 

   ```
   package Test;  
     
   import java.util.ArrayList;  
   import java.util.Iterator;  
   import java.util.List;  
   public class TestList {  
       public static void main(String dd[]) {  
           // new了一个存储list  
           List l = new ArrayList();  
           // 因为Collection framework只能存储对象所以new封装类  
           l.add(new Integer(1));  
           l.add(new Integer(2));  
           l.add(new Integer(3));  
           l.add(new Integer(4));  
     
           Iterator it = l.iterator();  
           //使用 迭代器（Iterator）:  
           // hasNext是取值取的是当前值.他的运算过程是判断下个是否有值如果有继续.  
           while (it.hasNext()) {  
               System.out.println("iterator：Element in list is :   " + it.next());  
           }  
           //用for循环和get()方法：  
           for (int i = 0; i < l.size(); i++) {  
               System.out.println("for：Element in list is :   " + l.get(i));  
           }  
       }  
   } 
   ```

   LinkedList

   ```
       package Test;  
         
       import java.util.Iterator;  
       import java.util.LinkedList;  
         
       public class TestLinkedList {  
           public static void main(String arg[]) {  
               LinkedList ll = new LinkedList();// 声明LinkedList并实例化  
               // 使用add（）方法添加元素  
               ll.add("a");  
               ll.add("b");  
               ll.add("c");  
               // 使用Iterator迭代器遍历出集合的元素并打印  
               Iterator it = ll.iterator();  
               while (it.hasNext()) {  
                   System.out.println(it.next());  
               }  
               System.out.println("------------------");  
               // 向链表头和尾分别添加x和z  
               ll.addFirst("z");  
               ll.addLast("x");  
               // 遍历查看添加后的结果  
               for (Iterator i = ll.iterator(); i.hasNext();) {  
                   System.out.println(i.next());  
               }  
           }  
       }  
   ```

> ArrayList和LinkedList的区别。
>
> 1.ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。
>
> 2.对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。
>
> 3.对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。
>
> 如果熟悉数据结构，就会一下明白，ArrayList就是线性表的顺序表示，LinkedList就是线性表的链表表示。



## Set接口，代表无序，不可重复的集合

1. Set具有与Collection完全一样的接口，因此没有任何额外的功能，不像前面有两个不同的List。实际上Set就是Collection,只是行为不同。（这是继承与多态思想的典型应用：表现不同的行为。）Set不保存重复的元素（至于如何判断元素相同则较为负责） 
2. Set : 存入Set的每个元素都必须是唯一的，因为Set不保存重复元素。加入Set的元素必须定义equals()方法以确保对象的唯一性。Set与Collection有完全一样的接口。Set接口不保证维护元素的次序。 

**1. HashSet **

​     为快速查找设计的Set。存入HashSet的对象必须定义hashCode()。 
**2. TreeSet **

​    保存次序的Set, 底层为树结构。使用它可以从Set中提取有序的序列。 

**3. LinkedHashSet **

  具有HashSet的查询速度，且内部使用链表维护元素的顺序（插入的次序）。于是在使用迭代器遍历Set时，结果会按元素插入的次序显示。

```
	Set set=new HashSet();  
    String s1=new String("hello");  
    String s2=s1;  
    String s3=new String("world");  
    set.add(s1);  
    set.add(s2);  
    set.add(s3);  
    System.out.println(set.size());//打印集合中对象的数目 为 2。  
    Set 的 add()方法是如何判断对象是否已经存放在集合中？  
    boolean isExists=false;  
    Iterator iterator=set.iterator();  
    while(it.hasNext())  {  
        String oldStr=it.next();  
        if(newStr.equals(oldStr)){  
           isExists=true;  
        }  
    } 
```

## Map接口：映射

Map没有继承Collection接口， Map 提供 key 到 value 的映射，你可以通过“键”查找“值”。一个 Map 中不能包含相同的 key ，每个 key 只能映射一个 value 。 Map 接口提供3 种集合的视图， Map 的内容可以被当作一组 key 集合，一组 value 集合，或者一组 key-value 映射。

方法 put(Object key, Object value) 添加一个“值” ( 想要得东西 ) 和与“值”相关联的“键” (key) ( 使用它来查找 ) 。方法get(Object key) 返回与给定“键”相关联的“值”。可以用 containsKey() 和 containsValue() 测试 Map 中是否包含某个“键”或“值”。 标准的 Java 类库中包含了几种不同的 Map ： HashMap, TreeMap, LinkedHashMap, WeakHashMap, IdentityHashMap 。它们都有同样的基本接口 Map ，但是行为、效率、排序策略、保存对象的生命周期和判定“键”等价的策略等各不相同。

Map 同样对每个元素保存一份，但这是基于 " 键"
 的， Map 也有内置的排序，因而不关心元素添加的顺序。如果添加元素的顺序对你很重要，应该使用 LinkedHashSet 或者 LinkedHashMap.
执行效率是 Map 的一个大问题。看看 get() 要做哪些事，就会明白为什么在 ArrayList 中搜索“键”是相当慢的。而这正是 HashMap 提高速度的地方。 HashMap 使用了特殊的值，称为“散列码” (hash code) ，来取代对键的缓慢搜索。“散列码”是“相对唯一”用以代表对象的int
 值，它是通过将该对象的某些信息进行转换而生成的（在下面总结二：需要的注意的地方有更进一步探讨）。所有 Java 对象都能产生散列码，因为 hashCode() 是定义在基类 Object 中的方法 。 HashMap 就是使用对象的 hashCode() 进行快速查询的。此方法能够显著提高性能。

**1.  Hashtable类**
　　Hashtable继承Map接口，实现一个key-value映射的哈希表。任何非空（non-null）的对象都可作为key或者value。Hashtable是同步的。

　　添加数据使用 put(key, value) ，取出数据使用get(key) ，这两个基本操作的时间开销为常数。Hashtable 通过初始化容量 (initial capacity) 和负载因子 (load factor) 两个参数调整性能。通常缺省的 load factor0.75 较好地实现了时间和空间的均衡。增大 load factor 可以节省空间但相应的查找时间将增大，这会影响像get
 和 put 这样的操作。使用 Hashtable 的简单示例如下，将 1 ，2 ，3 放到 Hashtable 中，他们的 key 分别是 ”one” ， ”two” ， ”three” ：

```
 Hashtable numbers =new
 Hashtable();
 numbers.put(“one”, new Integer(1));
 numbers.put(“two”, new Integer(2));
 numbers.put(“three”, new Integer(3));
 //要取出一个数，比如 2 ，用相应的 key ：
 Integer n = (Integer)numbers.get(“two”);
 System.out.println(“two=”+ n);
```

由于作为 key 的对象将通过计算其散列函数来确定与之对应的 value 的位置，因此任何作为 key 的对象都必须实现 hashCode 方法和 equals 方法。 hashCode 方法和 equals 方法继承自根类 Object ，如果你用自定义的类当作 key 的话，要相当小心，按照散列函数的定义，如果两个对象相同，即 obj1.equals(obj2)=true ，则它们的 hashCode 必须相同，但如果两个对象不同，则它们的 hashCode 不一定不同，如果两个不同对象的 hashCode 相同，这种现象称为冲突，冲突会导致操作哈希表的时间开销增大，所以尽量定义好的 hashCode() 方法，能加快哈希表的操作。
如果相同的对象有不同的 hashCode ，对哈希表的操作会出现意想不到的结果（期待的 get 方法返回null），要避免这种问题，只需要牢记一条：要同时复写 equals 方法和 hashCode 方法，而不要只写其中一个。 Hashtable 是同步的。 

**2.  HashMap类**
　　HashMap和Hashtable类似，也是基于hash散列表的实现。不同之处在于 HashMap是非同步的，并且允许null，即null value和null key。，但是将HashMap视为Collection时 （values()方法可返回Collection），其迭代子操作时间开销和HashMap的容量成比例。因此，如果迭代操作的性能相当重要的话，不要 将HashMap的初始化容量设得过高，或者load factor过低。

　　 LinkedHashMap 类：类似于 HashMap ，但是迭代遍历它时，取得“键值对”的顺序是其插入次序，或者是最近最少使用 (LRU) 的次序。只比 HashMap 慢一点。而在迭代访问时发而更快，因为它使用链表维护内部次序。
**3.  WeakHashMap类 （弱键（ weak key ））**
　　WeakHashMap是一种改进的HashMap，它是为解决特殊问题设计的，它对key实行“弱引用”，如果一个key不再被外部所引用，那么该key可以被GC回收。

**4. TreeMap 类**
基于红黑树数据结构的实现。查看“键”或“键值对”时，它们会被排序 ( 次序由 Comparabel 或 Comparator 决定 ) 。 TreeMap 的特点在于，你得到的结果是经过排序的。 TreeMap 是唯一的带有 subMap() 方法的 Map ，它可以返回一个子树。

**5. IdentifyHashMap 类**
使用 == 代替 equals() 对“键”作比较的 hash map 。专为解决特殊问题而设计。

用法：  

1. 添加， 删除造作

```
Object put(Object key, Object value): 向集合中加入元素  
Object remove(Object key):   删除与KEY相关的元素  
void putAll(Map t):   将来自特定映像的所有元素添加给该映像  
void clear(): 从映像中删除所有映射  

```

2 查询操作：

​    Object get(Object key): 获得与关键字key相关的值

​    Map集合中的键对象不允许重复，也就说，任意两个键对象通过equals()方法比较的结果都是false.

​    但是可以将任意多个键独享映射到同一个值对象上。

​    Conllections : 集合实用类

​    Conllections提供了供JAVA集合实用的静态方法



## 如何选择

1. 容器类和Array的区别、择取

    容器类仅能持有对象引用（指向对象的指针），而不是将对象信息copy一份至数列某位置。

   一旦将对象置入容器内，便损失了该对象的型别信息。

2.  在各种Lists中，最好的做法是以ArrayList作为缺省选择。当插入、删除频繁时，使用LinkedList()；           Vector总是比ArrayList慢，所以要尽量避免使用。

    在各种Sets中，HashSet通常优于HashTree（插入、查找）。只有当需要产生一个经过排序的序列，才用TreeSet。

   HashTree存在的唯一理由：能够维护其内元素的排序状态。

3. 在各种Maps中,HashMap用于快速查找。

4. 当元素个数固定，用Array，因为Array效率是最高的。

**结论：最常用的是ArrayList，HashSet，HashMap，Array。而且，我们也会发现一个规律，用TreeXXX都是排序的。**

**注意：**

1. Collection没有get()方法来取得某个元素。只能通过iterator()遍历元素。

2. Set和Collection拥有一模一样的接口。

3. List，可以通过get()方法来一次取出一个元素。使用数字来选择一堆对象中的一个，get(0)...。(add/get)

4. 一般使用ArrayList。用LinkedList构造堆栈stack、队列queue。

5. Map用 put(k,v) / get(k)，还可以使用containsKey()/containsValue()来检查其中是否含有某个key/value。      HashMap会利用对象的hashCode来快速找到key。

   hashing:哈希码就是将对象的信息经过一些转变形成一个独一无二的int值，这个值存储在一个array中。我们都知道所有存储结构中，array查找速度是最快的。所以，可以加速查找。发生碰撞时，让array指向多个values。即，数组每个位置上又生成一个梿表。

6. Map中元素，可以将key序列、value序列单独抽取出来。

   使用keySet()抽取key序列，将map中的所有keys生成一个Set。

   使用values()抽取value序列，将map中的所有values生成一个Collection。

   为什么一个生成Set，一个生成Collection？那是因为，key总是独一无二的，value允许重复。