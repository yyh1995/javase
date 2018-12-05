# HashMap实现原理及源码分析
  HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。</br>
  HashMap 继承于AbstractMap，实现了Map、Cloneable、java.io.Serializable接口。</br>
  HashMap 的实现不是同步的，这意味着它不是线程安全的。它的key、value都可以为null。此外，HashMap中的映射不是有序的。</br>
  JDK1.8 之前 HashMap 由 **数组+链表** 组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）.
  JDK1.8 以后在解决哈希冲突时有了较大的变化，由 **数组+链表+树** 组成，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树，以减少搜索时间。  
  
## 1.什么是哈希表
我们知道，数据结构的物理存储结构只有两种：顺序存储结构和链式存储结构（像栈，队列，树，图等是从逻辑结构去抽象的，映射到内存中，也这两种物理组织形式），而在上面我们提到过，在数组中根据下标查找某个元素，一次定位就可以达到，哈希表利用了这种特性，哈希表的主干就是数组。  
比如我们要新增或查找某个元素，我们通过把当前元素的关键字 通过某个函数映射到数组中的某个位置，通过数组下标一次定位就可完成操作。  

**存储位置 = f(关键字)**  

  其中，这个函数f一般称为哈希函数，这个函数的设计好坏会直接影响到哈希表的优劣。举个例子，比如我们要在哈希表中执行插入操作：  
![](https://github.com/yyh1995/javase/blob/master/pic/1.png)
查找操作同理，先通过哈希函数计算出实际存储地址，然后从数组中对应地址取出即可。

### 哈希冲突 
  然而万事无完美，如果两个不同的元素，通过哈希函数得出的实际存储地址相同怎么办？也就是说，当我们对某个元素进行哈希运算，得到一个存储地址，然后要进行插入的时候，发现已经被其他元素占用了，其实这就是所谓的**哈希冲突**，也叫哈希碰撞。前面我们提到过，哈希函数的设计至关重要，好的哈希函数会尽可能地保证 计算简单和散列地址分布均匀,但是，我们需要清楚的是，数组是一块连续的固定长度的内存空间，再好的哈希函数也不能保证得到的存储地址绝对不发生冲突。那么哈希冲突如何解决呢？  
  哈希冲突的解决方案有多种:开放定址法（发生冲突，继续寻找下一块未被占用的存储地址），再散列函数法，链地址法，而HashMap即是采用了**链地址法**，也就是数组+链表的方式.JDK1.8版本中，对数据结构做了进一步的优化，引入了红黑树。而当链表长度太长（TREEIFY_THRESHOLD默认超过8）时，链表就转换为红黑树，利用红黑树快速增删改查的特点提高HashMap的性能（O(logn)）。当长度小于（UNTREEIFY_THRESHOLD默认为6），就会退化成链表。
  
## 2.HashMap实现原理(JDK1.8)
### a.位桶
HashMap的主干是一个Node[] table，即哈希桶数组，明显它是一个Node的数组。
Node是HashMap的基本组成单元，每一个Node包含一个key-value键值对.
```Java
transient Node<k,v>[] table;//存储（位桶）的数组
```

### b.链表  
Node是HashMap的一个静态内部类，实现了Map.Entry接口，本质是就是一个映射(键值对)。
```Java
//Node是单向链表，它实现了Map.Entry接口
static class Node<k,v> implements Map.Entry<k,v> {
    final int hash;
    final K key;
    V value;
    Node<k,v> next;
    //构造函数Hash值 键 值 下一个节点
    Node(int hash, K key, V value, Node<k,v> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
 
    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + = + value; }
 
    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }
 
    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }
    //判断两个node是否相等,若key和value都相等，返回true。可以与自身比较为true
    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<!--?,?--> e = (Map.Entry<!--?,?-->)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```
可以看到，node中包含一个next变量，这个就是链表的关键点，hash结果相同的元素就是通过这个next进行关联的。

### c.红黑树
```Java
//红黑树
static final class TreeNode<k,v> extends LinkedHashMap.Entry<k,v> {
    TreeNode<k,v> parent;  // 父节点
    TreeNode<k,v> left; //左子树
    TreeNode<k,v> right;//右子树
    TreeNode<k,v> prev;    // needed to unlink next upon deletion
    boolean red;    //颜色属性
    TreeNode(int hash, K key, V val, Node<k,v> next) {
        super(hash, key, val, next);
    }
 
    //返回当前节点的根节点
    final TreeNode<k,v> root() {
        for (TreeNode<k,v> r = this, p;;) {
            if ((p = r.parent) == null)
                return r;
            r = p;
        }
    }
}
```
红黑树比链表多了四个变量，parent父节点、left左节点、right右节点、prev上一个同级节点，红黑树内容较多，不在赘述。
