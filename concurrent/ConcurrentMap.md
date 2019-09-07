# ConcurrentMap
ConcurrentMap 接口表示了一个能够对别人的访问(插入和提取)进行并发处理的 java.util.Map。 

ConcurrentMap 除了从其父接口 java.util.Map 继承来的方法之外还有一些额外的原子性方法。

## ConcurrentMap的实现
既然 ConcurrentMap 是个接口，你想要使用它的话就得使用它的实现类之一。java.util.concurrent 包具备 ConcurrentMap 接口的以下实现类：ConcurrentHashMap

# ConcurrentHashMap
ConcurrentHashMap 和 java.util.HashTable 类很相似，但 ConcurrentHashMap 能够提供比 HashTable 更好的并发性能。在你从中读取对象的时候 ConcurrentHashMap 并不会把整个 Map 锁住。此外，在你向其中写入对象的时候，ConcurrentHashMap 也不会锁住整个 Map。它的内部只是把 Map 中正在被写入的部分进行锁定。

另外一个不同点是，在被遍历的时候，即使是 ConcurrentHashMap 被改动，它也不会抛ConcurrentModificationException。尽管 Iterator 的设计不是为多个线程的同时使用。

## ConcurrentMap 例子
以下是如何使用 ConcurrentMap 接口的一个例子。本示例使用了 ConcurrentHashMap 实现类：

```java
ConcurrentMap concurrentMap = new ConcurrentHashMap();  
 
concurrentMap.put("key", "value");  
 
Object value = concurrentMap.get("key");  
```

# 并发导航映射 ConcurrentNavigableMap
java.util.concurrent.ConcurrentNavigableMap 是一个支持并发访问的 java.util.NavigableMap，它还能让它的子 map 具备并发访问的能力。所谓的 “子 map” 指的是诸如 headMap()，subMap()，tailMap() 之类的方法返回的 map。

NavigableMap 中的方法不再赘述，本小节我们来看一下 ConcurrentNavigableMap 添加的方法。

## headMap()
headMap(T toKey) 方法返回一个包含了小于给定 toKey 的 key 的子 map。 
如果你对原始 map 里的元素做了改动，这些改动将影响到子 map 中的元素(译者注：map 集合持有的其实只是对象的引用)。

以下示例演示了对 headMap() 方法的使用：

```java
ConcurrentNavigableMap map = new ConcurrentSkipListMap();  
 
map.put("1", "one");  
map.put("2", "two");  
map.put("3", "three");  
 
ConcurrentNavigableMap headMap = map.headMap("2");  
```

headMap 将指向一个只含有键 “1” 的 ConcurrentNavigableMap，因为只有这一个键小于 “2”。关于这个方法及其重载版本具体是怎么工作的细节请参考 Java 文档。

## tailMap()
tailMap(T fromKey) 方法返回一个包含了不小于给定 fromKey 的 key 的子 map。 
如果你对原始 map 里的元素做了改动，这些改动将影响到子 map 中的元素(译者注：map 集合持有的其实只是对象的引用)。

以下示例演示了对 tailMap() 方法的使用：

```java
ConcurrentNavigableMap map = new ConcurrentSkipListMap();  
 
map.put("1", "one");  
map.put("2", "two");  
map.put("3", "three");  
 
ConcurrentNavigableMap tailMap = map.tailMap("2");  
```

tailMap 将拥有键 “2” 和 “3”，因为它们不小于给定键 “2”。关于这个方法及其重载版本具体是怎么工作的细节请参考 Java 文档。

## subMap()
subMap() 方法返回原始 map 中，键介于 from(包含) 和 to (不包含) 之间的子 map。示例如下：

```java
ConcurrentNavigableMap map = new ConcurrentSkipListMap();  
 
map.put("1", "one");  
map.put("2", "two");  
map.put("3", "three");  
 
ConcurrentNavigableMap subMap = map.subMap("2", "3");  
```

返回的 submap 只包含键 “2”，因为只有它满足不小于 “2”，比 “3” 小。

## 更多方法
ConcurrentNavigableMap 接口还有其他一些方法可供使用，比如：
- descendingKeySet()
- descendingMap()
- navigableKeySet()
