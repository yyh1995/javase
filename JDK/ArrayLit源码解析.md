# ArrayList实现原理与源码解析
  ArrayList 可以动态增长和缩减的索引序列，相当于**动态数组**。  
  ArrayList 继承了AbstractList，实现了List。它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。  
  ArrayList 实现了RandmoAccess接口，即提供了随机访问功能。RandmoAccess是java中用来被List实现，为List提供快速访问功能的。   
  ArrayList 实现了Cloneable接口，即覆盖了函数clone()，能被克隆。  
  ArrayList 实现java.io.Serializable接口，这意味着ArrayList支持序列化，能通过序列化去传输。  
  ArrayList是**线程不安全**当多条线程访问同一个ArrayList集合时，程序需要手动保证该集合的同步性，而Vector则是线程安全的。  
  
## 1.ArrayList实现原理    
  ![image](https://github.com/yyh1995/javase/blob/master/pic/616953-20160322185210151-491440543.png)   
  底层的数据结构就是数组，数组元素类型为Object类型，即可以存放所有类型数据。我们对ArrayList类的实例的所有的操作底层都是基于数组的。
  
## 2.ArrayList源码分析

### a.类的属性  
```java

public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, Serializable {
	// 序列化id
	private static final long serialVersionUID = 8683452581122892189L;
	
	// 默认大小为10,实际上调用无参构造函数初始化时,存放数据的数据是个空数组,调用add时才会真正初始化.
	private static final int DEFAULT_CAPACITY = 10;
	
	// 一个空对象
	private static final Object[] EMPTY_ELEMENTDATA = {};
	
	// 一个空对象，如果使用默认构造函数创建，则默认对象内容默认是该值
	//与 EMPTY_ELEMENTDATA 区分开来，以便知道第一个元素添加时如何扩容  
	private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA ={};
	
	// 真正存放数据的数组，当前对象不参与序列化
	transient Object[] elementData;
	
	// 当前数组长度
	private int size;
 
	// 省略方法。。
}
```
### b.构造方法  
```java
public ArrayList() {
                //调用无参构造函数初始化时,存放数据的数据是个空数组,调用add时才会真正初始化
		this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
	}
	
	
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {   //初始容量大于0
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {  // 初始容量为0 
            this.elementData = EMPTY_ELEMENTDATA;    // 为空对象数组,区别DEFAULTCAPACITY_EMPTY_ELEMENTDATA
        } else {   // 初始容量小于0，抛出异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    } 
    
public ArrayList(Collection<? extends E> c) { // 集合参数构造函数
        elementData = c.toArray(); // 转化为数组
        if ((size = elementData.length) != 0) { // 参数为非空集合
            if (elementData.getClass() != Object[].class) // 是否成功转化为Object类型数组
                elementData = Arrays.copyOf(elementData, size, Object[].class); // 不为Object数组的话就进行复制
        } else { // 集合大小为空，则设置元素数组为空
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

### c.方法解析
#### 1.ArrayList容量相关方法
**add**的具体函数如下：
```java 
 public boolean add(E e) { // 添加元素
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```
在add函数我们发现还有其他的函数ensureCapacityInternal，此函数可以理解为确保elementData数组有合适的大小。**ensureCapacityInternal**的具体函数如下：  
```java
private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) { // 判断元素数组是否为空数组
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity); // 取较大值
        }
        
        ensureExplicitCapacity(minCapacity);
    }
```
在ensureCapacityInternal函数中我们又发现了ensureExplicitCapacity函数，这个函数也是为了确保elemenData数组有合适的大小。**ensureExplicitCapacity**的具体函数如下：  
```java
private void ensureExplicitCapacity(int minCapacity) {
        // 结构性修改加1
        modCount++;
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
 ```
在ensureExplicitCapacity函数我们又发现了grow函数，grow函数才会对数组进行扩容，ensureCapacityInternal、ensureExplicitCapacity都只是过程，最后完成实际扩容操作还是得看grow函数。**grow**函数的具体函数如下：  
```java
private void grow(int minCapacity) {
        int oldCapacity = elementData.length; // 旧容量
        int newCapacity = oldCapacity + (oldCapacity >> 1); // 新容量为旧容量的1.5倍
        if (newCapacity - minCapacity < 0) // 新容量小于参数指定容量，修改新容量
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0) // 新容量大于最大容量
            newCapacity = hugeCapacity(minCapacity); // 指定新容量
        // 拷贝扩容
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
 ```



 
  
