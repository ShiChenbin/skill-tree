@[TOC](目录)
注：本篇文章仅用于2022年2月14日实习方便自己学习以及查找，需要学习的东西很多脑子也没有清楚的路线。

# 环境准备
一、环境准备：
1. jdk 1.8+
2. gradle 7.0+
3. intellij idea 2021.2+
4. git （代码版本管理工具）
5. chiner （数据库建模工具）
6. navicat / dbeaver （数据库连接工具）
7. postman （接口测试工具）
8. tabby terminal （服务器连接工具） 

# 汇总
1. java 语法基础知识（List、Map、CompletableFuture、Stream API）
2. http get/post 知识
3. json、restful 知识
4. annotation、orm 概念
5. spring、spring mvc、springboot、jpa 概念
6. vue、element ui 概念

# java
## List
List类型的集合是有序集合，特点是可以精确控制每个元素的位置，用户可以通过整数的索引来访问List中的元素。

### 常用的子类
ArrayList 继承自AbstractList，实现了List接口。
- 底层是基于数组的
- 实现容量大小动态变化
- 所谓的有序是值按照先后添加的先后顺序排列。
- - 如果要对ArrayL按照元素的值进行排序，可以调用Collection.sort()方法并且提供一个Comparator比较器。

linkedlist
- 底层是基于双向链表
- - 每个节点维护了prev和next指针，用于遍历链表。同时链表还维护了first和last指针，分别指向第一个元素和最后一个元素。
- linkedlist的线程是不安全的。

List\<E> 的主要几个接口方法

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


## stream

Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。使用Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数据库查询。也可以使用 Stream API 来并行执行操作。简而言之，Stream API 提供了一种高效且易于使用的处理数据的方式。

将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。

元素流在管道中经过中间操作（intermediate operation）的处理，最后由最终操作(terminal operation)得到前面处理的结果。

1. 通过Collection系列集合提供的stream()方法或者parallelStream()方法来创建Stream。（在 Java 8 中, 集合接口有两个方法来生成流：）
```java
List<string> list = new ArrayList<>();
list.stream();//顺序流
list.parallelStream();//并行流
```
2. 通过Arrays中的静态方法stream()获取数组流。
```java
public static <T> Stream <T> stream(T[] arrays){
	return stream(array, 0, array.length);
}
```
---
```java
Integer[] nums = new Integer[]{1,2,3,4,5,6,7,8,9};
Stream<Integer> numStream = Arrays.stream(nums);
```

3. 通过Stream类的静态方法of()获取数组流。
可以使用静态方法Stream.of(),通过显示值创建一个流。他可以接收任意数量的参数。
```java
Stream<String> strStream = Stream.of("a", "b", "c");
```
4. 创建无限流
可以使用静态方法Stream.iterate()和Stream.generate()，创建无限流。
```java
        //从0开始一直输出，会输出偶数
        Stream<Integer> intStream = Stream.iterate(0, (x)-> x + 2);
        intStream.forEach(System.out::println);
```
```java
        //对输出数量进行限制
        Stream<Integer> intStream = Stream.iterate(0, (x)-> x + 2);
        intStream.limit(10).forEach(System.out::println);
```
例子
```java
     Stream.generate(()->Math.random()).limit(5).forEach(System.out::println);
```
5. 创建空流

```java
Stream<String> empty = Stream.empty();
```

### foreach
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
[资料1](https://juejin.cn/post/6844904195162636295)
[资料2](https://www.liaoxuefeng.com/wiki/1252599548343744/1306581182447650)

# HTTP get/post
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
理想的数据交换语言,数据传输是在我们敲代码时，经常遇到的一个场景，前后端交互。给数据一个统一的格式有利于我们编写和解析数据。
## json语法规则
json 语法是 javaScript 对象表示语法的子集
数据在名称/值对中
数据由逗号分隔
大括号 {} 保存对象
中括号 [] 保存数组，数组可以包含多个对象
    
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
- JSON 对象在大括号 {} 中书写：
```
{key1 : value1, key2 : value2, ... keyN : valueN }

var str2 = {"name":"asam","sex":"man"};
```
    
- 逻辑值（true 或 false）
```
{"flag":true}
```

- null
```
{"runoob":null}
```
    
## json对象和json字符串的区别
   json 对象和 json 字符串的区别：

json 对象（在大括号中）
```
var str2 = { "name": "asam", "sex": "man" };
```
json字符串
```
var str1 = '{ "name": "deyuyi", "sex": "man" }';
```

## json数组
json数组在中括号中
数组作为json对象
```json
{
    "name":"网站"
    "num":3,
    "sites":["Google", "Runoob", "Taobao"]
}
```

## JSON 提供了 stringify 和 parse 方法的内置对象
JSON是一个提供了stringify和parse方法的内置对象
- stringify将js对象转化为符合json标准的字符串
- parse将符合json标准的字符串转化为js对象

## json.parse
JSON通常用于与服务端交换数据。
在接受服务器数据时一般是字符串
我们可以使用JSON.parse()方法将数据转换为JavaScript对象

### 语法
```
JSON,parse(text[,reviver])
```
参数说明：
    text必需，一个有效的JSON文件
    reviver可选，一个转换结果的参数，将为对象的每个成员调用此函数。
```
    var obj = JSON.parse('{"name":"runoob", "alexa":10000,"site":"runoob.com"}');
    document.getElementById("demo").innerHTML = obj.name + ": " + abj.site;
```
## json实例
```
// 假设我想查询符合某些条件的前3个人的信息.
// 然后我发送了一个请求，服务器处理完之后，扔回了这些JSON数据.
var json = {
    'one':{'name':'John green', 'sex':'female', 'age':20},
    'two':{'name':'Hank green', 'sex':'male', 'age':18},
    'three':{'name':'Lucy', 'sex':'unknown', 'age':30}
};


//使用数据的3种方法
//alert(json.one.name);      
//alert(json['one']['name']);
//alert(json['one'].name);    //也可以混合着用
//建议使用第二种方法..也就是json['one']['name']


// 循环处理的小案例(可以去掉注释运行看看)
/*
for(a in json){
   alert(json[a]['name']);
}
*/


//我们可以对JSON处理，然后添加到页面上
for(a in json){ 
    var node = document.createElement('li');  
    //创建li标签
    node.className = "yoholi";
    var text = document.createTextNode(json[a]['name']); 
    //创建文本节点
    
    node.appendChild(text);
    //文本节点添加到li标签里
    
    document.body.appendChild(node); 
    //把li标签添加到body标签里
}

```
    
## 注：比较优秀javaScript码风
[链接](https://www.zhihu.com/search?type=content&q=javascript%20%E4%BC%98%E7%A7%80%E7%9A%84%E7%A0%81%E9%A3%8E)
# RESTful 架构详解
REST全称是Representational State Transfer，中文意思是表述（编者注：通常译为表征）性状态转移。 它首次出现在2000年Roy Fielding的博士论文中，Roy Fielding是HTTP规范的主要编写者之一。 他在论文中提到："我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。REST指的是一组架构约束条件和原则。" 如果一个架构符合REST的约束条件和原则，我们就称它为RESTful架构。

REST本身并没有创造新的技术、组件或服务，而隐藏在RESTful背后的理念就是使用Web的现有特征和能力， 更好地使用现有Web标准中的一些准则和约束。虽然REST本身受Web技术的影响很深， 但是理论上REST架构风格并不是绑定在HTTP上，只不过目前HTTP是唯一与REST相关的实例。 所以我们这里描述的REST也是通过HTTP实现的REST。

[资料1](https://www.runoob.com/w3cnote/restful-architecture.html)
[资料2](https://www.ruanyifeng.com/blog/2014/05/restful_api.html)

# annotation 概念
Annotation 从JDK5.0开始引入
1. Annotationde的作用

Annotaion不是程序，只是对程序做出解释（和注释一样）
Annotation可以被其他程序（如：编译器）读取，然后进行处理

2. Annotation的格式
-"@注解名称(参数)"如@SuppressWarnings(value=“all”)

3. 注解放在哪里
注解可以卸载类，方法，属性等上面，相当于添加额外的辅助信息，可以通过反射机制变成实现对这些注解进行读取访问

4. 自定义注解
使用@interface 自定义注解
自定义注解时，@interface 自动继承了java.lang.annotation.Annotation接口

5. 源注解
源注解的作用就是负责注解其他注解。
Java中定义了4个标准的meta-annotation类型，他们被用来提供对其他annotation类型做说明。
*
```java
@Target //用来描述注解的使用范围， 例如注解能作用到类上，变量上等等。
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
```
*
```java
 @Retention // 表示要在什么级别保留该注解信息，用于描述注解的生命周期
```

保留策略 | 作用
-------- | -----
SOURCE |	在源文件中有效
CLASS	| 在class文件中有效
RUNTIME | 	运行时有效：为runtime时则可以使用反射机制读取
*
```java
@Retention(RetentionPolicy.RUNTIME)
```
*
@Documented
@Documented注解表明这个注解应该被 javadoc工具记录. 默认情况下,javadoc是不包括注解的. 但如果声明注解时指定了 @Documented,则它会被 javadoc 之类的工具处理, 所以注解类型信息也会被包括在生成的文档中，是一个标记注解，没有成员。

*
@Inherited
@InheritedAnno注解加在某个类A上时，假如类B继承了A，则B也会带上该注解。

# orm
ORM 就是通过实例对象的语法，完成关系型数据库的操作的技术，是"对象-关系映射"（Object/Relational Mapping） 的缩写。
- 数据库的表（table） --> 类（class）
- 记录（record，行数据）--> 对象（object）
- 字段（field）--> 对象的属性（attribute）

ORM 使用对象，封装了数据库操作，因此可以不碰 SQL 语言。开发者只使用面向对象编程，与数据对象直接交互，不用关心底层数据库。
[阮一峰资料](http://www.ruanyifeng.com/blog/2019/02/orm-tutorial.html)

#  spring、spring mvc、springboot 概念
[来源](https://cloud.tencent.com/developer/article/1665045)

1. Spring
Spring是一个开源容器框架，可以接管web层，业务层，dao层，持久层的组件，并且可以配置各种bean,和维护bean与bean之间的关系。其核心就是控制反转(IOC),和面向切面(AOP),简单的说就是一个分层的轻量级开源框架。

2. SpringMVC
SpringMVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。SpringMVC是一种web层mvc框架，用于替代servlet（处理|响应请求，获取表单参数，表单校验等。SpringMVC是一个MVC的开源框架，SpringMVC=struts2+spring，springMVC就相当于是Struts2加上Spring的整合。

3. SpringBoot
Springboot是一个微服务框架，延续了spring框架的核心思想IOC和AOP，简化了应用的开发和部署。Spring Boot是为了简化Spring应用的创建、运行、调试、部署等而出现的，使用它可以做到专注于Spring应用的开发，而无需过多关注XML的配置。提供了一堆依赖打包，并已经按照使用习惯解决了依赖问题--->习惯大于约定。

SpringBoot只是Spring本身的扩展，使开发，测试和部署更加方便。

## springboot 入门教程
[spring boot maven 入门](https://www.exception.site/springboot/spring-boot-tutorial)
[spring boot gradle 入门](https://zhuanlan.zhihu.com/p/56961123)
[spring](https://spring.io/)

# PS
[后端开发入门](https://www.zhihu.com/question/387365793/answer/1152871587)
