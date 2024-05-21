# Java集合框架
JCF定义了14种容器接口（collection interfaces），它们的关系如下图所示：

![输入图片说明](images/jihe.pngimage.png)

Map接口没有继承自Collection接口，因为Map表示的是关联式容器而不是集合。但Java为我们提供了从Map转换到Collection的方法，可以方便的将Map切换到集合视图。

上图中提供了Queue接口，却没有Stack，这是因为Stack的功能已被JDK 1.6引入的Deque取代。
如下列表：

![输入图片说明](images/image1.png)

## 1、ArrayList
ArrayList实现了List接口，是顺序容器，即元素存放的数据与放进去的顺序相同，允许放入null元素，底层通过数组实现。除该类未实现同步外，其余跟Vector大致相同。每个ArrayList都有一个容量（capacity），表示底层数组的实际大小，容器内存储元素的个数不能多于当前容量。当向容器中添加元素时，如果容量不足，容器会自动增大底层数组的大小。前面已经提过，Java泛型只是编译器提供的语法糖，所以这里的数组是一个Object数组，以便能够容纳任何类型的对象。

size(), isEmpty(), get(), set()方法均能在常数时间内完成，add()方法的时间开销跟插入位置有关，addAll()方法的时间开销跟添加元素的个数成正比。其余方法大都是线性时间。

ArrayList 默认初始化是10，扩容的时候是  int newCapacity = oldCapacity + (oldCapacity >> 1);//原来的1.5倍
类似 oldCapacity=10，新的大小是 15。
### ArrayList remove会遇到的坑
经常遇到的一个场景是：遍历list, 然后找到合适条件的给删除掉，比如删除所有的偶数。

```java
@Test
public void testRemove2(){
    List<Integer> integers = new ArrayList<>(5);
    integers.add(1);
    integers.add(2);
    integers.add(2);
    integers.add(4);
    integers.add(5);

    for (int i = 0; i < integers.size(); i++) {
        if (integers.get(i)%2==0){
            integers.remove(i);
        }
    }
    System.out.println(integers);
}
```
看起来好像没问题，加入面试的时候当面问：输出结果是什么？再问真不会报错吗？再问结果是什么？
报错
结果是空list
结果是[1, 2, 5]

List.remove()有两个，一个public E remove(int index)，一个是public boolean remove(Object o)，那下面的结果是什么：

```java
@Test
public void testRemove(){
    ArrayList<Integer> integers = Lists.newArrayList(1, 2, 3, 4);
    System.out.println(integers);
    integers.remove(1);
    System.out.println(integers);
}
```
[1, 3, 4]

经常会使用一个Arrays.asList的API, 那么下面的结果是什么：

```java
@Test
public void testRemove3(){
    List<String> list = Arrays.asList("a","b");
    list.add("c");
    System.out.println(list);
}
```

报错： java.lang.UnsupportedOperationException

使用foreach是否可以实现刚开始的问题

```java
@Test
public void testRemove4(){
    List<String> strings = new ArrayList<>();
    strings.add("a");
    strings.add("b");
    strings.add("c");
    strings.add("d");

    for (String string : strings) {
        strings.remove(string);
    }
}
```
否，报错java.util.ConcurrentModificationException

为了性能问题，我们推荐把list.size的计算提取出来

```java
@Test
public void testRemove5(){
    List<String> strings = new ArrayList<>();
    strings.add("a");
    strings.add("b");
    strings.add("c");
    strings.add("d");

    int size = strings.size();
    for (int i = 0; i < size; i++) {
        strings.remove(i);
    }
}
```

报错： java.lang.IndexOutOfBoundsException: Index: 2, Size: 2

这是很好的习惯， 不像开头那样每次循环都计算一次size，而且按这种情况还可以再运行的时候报错。文初的做法不报错，但结果并不是我们想要的。
使用Iterator是不是就可以remove了
```java
@Test
public void testRemove6(){
    List<String> strings = new ArrayList<>();
    strings.add("a");
    strings.add("b");
    strings.add("c");
    strings.add("d");

    Iterator<String> iterator = strings.iterator();
    while (iterator.hasNext()){
        String next = iterator.next();
        strings.remove(next);
    }

    System.out.println(strings);
}
```
报错： java.util.ConcurrentModificationException
正确的remove做法是什么
```java
@Test
public void testRemove7(){
    List<String> strings = new ArrayList<>();
    strings.add("a");
    strings.add("b");
    strings.add("c");
    strings.add("d");

    Iterator<String> iterator = strings.iterator();
    while (iterator.hasNext()){
        String next = iterator.next();
        iterator.remove();
    }

    System.out.println(strings);
}
```
## 2、Map

高度注意 Map 类集合 K-V 能不能存储 null 值的情况，如下表格：

| 集合类            | Key             | Value           | Super       | 说明                   |
| ----------------- | --------------- | --------------- | ----------- | ---------------------- |
| HashMap           | **允许为 null** | **允许为 null** | AbstractMap | 线程不安全             |
| ConcurrentHashMap | 不允许为 null   | 不允许为 null   | AbstractMap | 锁分段技术（JDK8:CAS） |
| Hashtable         | 不允许为 null   | 不允许为 null   | Dictionary  | 线程安全               |
| TreeMap           | 不允许为 null   | **允许为 null** | AbstractMap | 线程不安全             |

反例： 由于 HashMap 的干扰，很多人认为 ConcurrentHashMap 是可以置入 null 值，而事实上， 存储 null 值时会抛出 NPE 异常。


 ...... 未完成 ..........