# HashMap实现原理及源码分析
  HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。</br>
  HashMap 继承于AbstractMap，实现了Map、Cloneable、java.io.Serializable接口。</br>
  HashMap 的实现不是同步的，这意味着它不是线程安全的。它的key、value都可以为null。此外，HashMap中的映射不是有序的。</br>

## 1.什么是哈希表
我们知道，数据结构的物理存储结构只有两种：顺序存储结构和链式存储结构（像栈，队列，树，图等是从逻辑结构去抽象的，映射到内存中，也这两种物理组织形式），而在上面我们提到过，在数组中根据下标查找某个元素，一次定位就可以达到，哈希表利用了这种特性，哈希表的主干就是数组。  
比如我们要新增或查找某个元素，我们通过把当前元素的关键字 通过某个函数映射到数组中的某个位置，通过数组下标一次定位就可完成操作。  
                           <font color=red> 存储位置 = f(关键字) </font>  
                           <font color=#00ffff size=72>color=#00ffff</font>
