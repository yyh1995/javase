# ArrayList实现原理与源码解析
  ArrayList 可以动态增长和缩减的索引序列，相当于**动态数组**。  
  ArrayList 继承了AbstractList，实现了List。它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。  
  ArrayList 实现了RandmoAccess接口，即提供了随机访问功能。RandmoAccess是java中用来被List实现，为List提供快速访问功能的。   
  ArrayList 实现了Cloneable接口，即覆盖了函数clone()，能被克隆。  
  ArrayList 实现java.io.Serializable接口，这意味着ArrayList支持序列化，能通过序列化去传输。  
  ArrayList是**线程不安全**当多条线程访问同一个ArrayList集合时，程序需要手动保证该集合的同步性，而Vector则是线程安全的。  
  
## 1.ArrayList实现原理  
  ArrayList 底层其实就是数组去保存数据的。
  
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
		this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
	}
	
	
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    } 
```



 
  
