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

### 类的属性  



 
  
