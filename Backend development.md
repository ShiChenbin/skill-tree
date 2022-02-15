# 实习基础知识目录
1. java 语法基础知识（List、Map、CompletableFuture、Stream API）
2. http get/post 知识
3. json、restful 知识
4. annotation、orm 概念
5. spring、spring mvc、springboot、jpa 概念
6. vue、element ui 概念

# java

## list
list类型的集合是有序集合，特点是可以精确控制每个元素的位置，用户可以通过整数的索引来访问list中的元素。

### 常用的子类
arraylist 继承自abstractlist，实现了list接口。】
- 底层是基于数组的
- 实现容量大小动态变化
- 所谓的有序是值按照先后添加的先后顺序排列。
- - 如果要对arraylist按照元素的值进行排序，可以调用Collection.sort()方法并且提供一个Comparator比较器。

linkedlist
- 底层是基于双向链表
- - 每个节点维护了prev和next指针，用于遍历链表。同时链表还维护了first和last指针，分别指向第一个元素和最后一个元素。
- linkedlist的线程是不安全的。

List<E>的主要几个接口方法
- 在末尾添加一个元素boolean add(E e)
- 在指定索引添加一个元素：boolean add(int index, E e)
- 删除指定索引的元素：E remove(int index)
- 删除某个元素：boolean remove(Object e)
- 获取指定索引的元素：E get(int index)
- 获取链表大小（包含元素的个数）：int size()

我们可以通过List接口提供的of()方法，根据给定元素快速创建List：
```java
List<Integer> list = List.of(1, 2, 3);
```

和数组类型，我们要遍历一个List，完全可以用for循环根据索引配合get(int)方法遍历：
```java
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        for (int i=0; i<list.size(); i++) {
            String s = list.get(i);
            System.out.println(s);
        }
    }
}
```
但这种方式并不推荐，一是代码复杂，二是因为get(int)方法只有ArrayList的实现是高效的，换成LinkedList后，索引越大，访问速度越慢。

所以我们要始终坚持使用迭代器Iterator来访问List。Iterator本身也是一个对象，但它是由List的实例调用iterator()方法的时候创建的。Iterator对象知道如何遍历一个List，并且不同的List类型，返回的Iterator对象实现也是不同的，但总是具有最高的访问效率。

Iterator对象有两个方法：boolean hasNext()判断是否有下一个元素，E next()返回下一个元素。因此，使用Iterator遍历List代码如下
```java
import java.util.Iterator;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
            String s = it.next();
            System.out.println(s);
        }
    }
}
```
vector 和 arraylist非常类似，同样继承自abstractlist。
- 两者的区别在于并发安全上，vector是线程安全的，为arraylist是线程不安全的
- - 例，当vector创建一个iterator并使用时，另一个线程改变了vector的状态，比如说添加或删除了一些元素，这时候再调用iterator会抛出异常。
- 
## map
与list和set不同，map类型不是collection接口的继承。
map中是通过key来索引value，那么就要求了key唯一的特性。
key-value也就是所谓的键值对

### map常用的实现类
- hashmap 是基于哈希表实现的。适用于在map中插入、删除和定位元素。
- treemap 是基于红黑树实现的。适用于按自然顺序和自定义顺序遍历键

HashMap通常比TreeMap快一点，树和哈希表的数据结构使然，建议一般场合多使用HashMap，在需要排序的场合才用TreeMap。HashMap和TreeMap都不是线程安全的，AbstractMap的子类HashTable才是线程安全的，不过不建议使用该类，建议在多线程环境下使用JUC包中的ConcurrentHashMap类。


map方法用于映射每个元素对应的结果
```
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
List<Integer> squaresList = numbers.stream().map(i->i*i).distinct().collect(Collectors.toList());
```

## map和list有什么区别
List：是存储单列数据的集合，存储的数据是有序并且是可以重复的

Map：存储双列数据的集合，通过键值对存储数据，存储 的数据是无序的，Key值不能重复，value值可以重复

## foreach
stream提供了新的方法来迭代流中的每个数据
输出了10个随机数

```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

java8中foreach的使用

```java
    Map<String, Integer> items = new HashMap<>();
    items.put("A", 10);
    items.put("B", 20);
    items.put("C", 30);
    items.put("D", 40);
    items.put("E", 50);
    items.put("F", 60);

    items.forEach((k,v)->System.out.println("Item : " + k + " Count : " + v));

    items.forEach((k,v)->{
        System.out.println("Item : " + k + " Count : " + v);
        if("E".equals(k)){
            System.out.println("Hello E");
        }
    });
```

## stream api
Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。
这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。
元素流在管道中经过中间操作（intermediate operation）的处理，最后由最终操作(terminal operation)得到前面处理的结果。
在 Java 8 中, 集合接口有两个方法来生成流：

- stream() − 为集合创建串行流。
- parallelStream() − 为集合创建并行流。

```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
```

## filter
filter 方法用于通过设置的条件过滤出元素。
Stream.filter()是Stream的另一个常用转换方法。

所谓filter()操作，就是对一个Stream的所有元素一一进行测试，不满足条件的就被“滤掉”了，剩下的满足条件的元素就构成了一个新的Stream。

例如，我们对1，2，3，4，5这个Stream调用filter()，传入的测试函数f(x) = x % 2 != 0用来判断元素是否是奇数，这样就过滤掉偶数，只剩下奇数，因此我们得到了另一个序列1，3，5：

```java
import java.util.stream.IntStream;
public class Main {
    public static void main(String[] args) {
        IntStream.of(1, 2, 3, 4, 5, 6, 7, 8, 9)
                .filter(n -> n % 2 != 0)
                .forEach(System.out::println);
    }
}
```
## CompletableFuture
使用future获得异步执行结果时，要么调用阻塞方法get(),要么轮询看isDone()是否位true，这两种方法都不是很好，因为主线程也会被迫等待。
从java 8开始引入了completableFuture，它针对Future做了改进，可以传入回调对象，当异步任务完成或者发生异常时，自动回调对象的回调方法。

# http get/post
GET和POST，两者都是超文本传输协议（HTTP）协议中发送请求的方法，本质上都是TCP链接，并无差别
## GET
GET方法请求一个指定资源的表示形式，使用GET的请求应该只被用于获取数据
## POST
POST方法用于将实体提交到指定的资源，通常导致在服务器上的状态变化或副作用。
但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中会体现出一些区别

### 区别
GET 用于获取信息，是无副作用的，是幂等的，且可缓存
POST 用于修改服务器上的数据，有副作用，非幂等，不可缓存

GET产生一个TCP数据包，POST产生两个TCP数据包

    
# json
## 简介
JSON(JavaScript Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。
理想的数据交换语言
## json与js的关系
JSON 是 JS 对象的字符串表示法，它使用文本表示一个 JS 对象的信息，本质是一个字符串。
## json 名称/值对
```json
    "name":"rname"
```
## JSON 值
- 数字（整数或浮点数）
    ```json
    { "age":30 }
    ```
- 字符串（在双引号中）
    ```json
    
    ```
    
- 逻辑值（true 或 false）``` ```
- 数组（在中括号中）``` ```
- 对象（在大括号中）``` ```
- null``` ```
    
 ## json对象和json字符串的区别
    json 对象和 json 字符串的区别：

json 对象
```json
var str2 = { "name": "asan", "sex": "man" };
```
json字符串
    
```json
var str1 = '{ "name": "deyuyi", "sex": "man" }';
```
