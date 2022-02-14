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
```
List
```

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
## 