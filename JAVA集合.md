# JAVA集合

> Java集合框架核心：List、Map，重点掌握 HashMap 和 ConcurrentHashMap 源码设计

## 核心内容

- ArrayList
- LinkedList
- HashMap
- ConcurrentHashMap

## 重点

- HashMap
- ConcurrentHashMap
- ArrayList VS LinkedList

---

## 1. ArrayList VS LinkedList

| 区别 | ArrayList | LinkedList |
| :---- | :--------- | :---------- |
| 底层 | 数组 | 双向链表 |
| 查询 | O(1)快 | O(n)慢 |
| 增删 | O(n)慢（尾部O(1)） | O(1)快（指定位置O(n)） |
| 内存 | 连续，有扩容开销 | 分散，每个节点存前后指针 |

---

## 2. HashMap核心

### 底层

- 数组 + 链表 + 红黑树（JDK8）

### put流程
1. `hash(key)` 计算哈希
2. `(n - 1) & hash` 找下标
3. 无冲突 → 直接插入
4. 有冲突 → 遍历链表 / 红黑树
   - 找到相同 key → 替换 value
   - 未找到 → 尾插
5. 链表长度 ≥ 8 且数组 ≥ 64 → 树化

### 扩容

- 2倍扩容，`resize()`重新计算位置

### 线程不安全

- JDK7：扩容头插导致死循环
- JDK8：改尾插但仍有数据覆盖

### ConcurrentHashMap

- JDK7：分段锁
- JDK8：CAS + `synchronized`锁头节点

---

## 3. HashSet底层

- 基于 HashMap 实现，元素存 key，value 占位 PRESENT（key，value 是一个固定的 PRESENT 对象）

---

## 追问

### 1. HashMap的hash方法具体怎么实现的？为什么这样设计？

```java
static final int hash(Object key){
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

- 高16位与低16位异或，目的是让高位也参与下标计算
- 因为数组长度通常较小，只有低位生效，异或后减少哈希碰撞

---

### 2. 为什么HashMap的数组长度必须是2的幂？

- 计算下标时用`(n - 1) & hash`代替`hash % n`，位运算更快
- 当 n 是2的幂时，n - 1的低位全是1，与运算结果均匀分布，减少碰撞

---

### 3. 为什么链表树化的阈值是8？红黑树退化为链表的阈值是6？

- 阈值8是根据泊松分布来的，链表长度达到8的概率极低（< 0.000001），同时红黑树节点占内存约2倍，阈值8平衡空间和时间
- 阈值6是为了防止频繁转换（如果也是8，链表和红黑树会反复转换）

---

### 4. HashMap扩容时，红黑树怎么拆分？

- 红黑树拆分成两个小树（低位树和高位树）
- 计算`(e.hash & oldCap) == 0`判断留在原位置还是移动到原位置 + oldCap
- 拆分后如果小树节点 <= 6，退化为链表

---

### 5. put时，发现key已存在，value怎么替换？

- 遍历链表/红黑树找到相同key，记录旧值，用新值替换，返回旧值

---

### 6. get方法的时间复杂度？最坏情况？

- 理想O(1)，最坏O(log n)（红黑树）或O(n)（链表退化）
- JDK8 已优化，链表过长会树化

---

### 7. ConcurrentHashMap的`size()`方法怎么实现的？还需要全表加锁吗？

- JDK8 用`baseCount + CounterCell[]`累加，每个线程更新自己的CounterCell，最后求和，不需要全表加锁

---

### 8. LinkedHashMap

#### 底层结构

- HashMap + 双向链表（维护插入顺序或访问顺序）

#### 怎么实现LRU

- `accessOrder = true`，每次get/put把元素移到链表末尾，头部就是最久未使用

#### removeEldestEntry作用

- 重写这个方法，返回true时删除头部元素，实现容量限制

---

## 补充：TreeMap vs HashMap

| 特性 | HashMap | TreeMap |
|:-----|:--------|:--------|
| 底层 | 数组+链表+红黑树 | 红黑树 |
| 有序 | 无序 | 自然序/Comparator |
| 复杂度 | O(1) 平均 | O(log n) |
| null 键 | 允许一个 | 不允许 |

---

## 总结

### 1. hash方法（HashMap：位运算取模 + 高低位异或 + 8 树化 6 退化 + 2 倍扩容）

- `key.hashCode()`的高16位异或低16位，让高位参与运算

### 2. 长度是2的幂

- 位运算代替取模，更快且均匀分布

### 3. 树化阈值8

- 泊松分布，概率极低，平衡时空

### 4. 扩容时红黑树拆分

- `(e.hash & oldCap) == 0`判断原位置还是移动 

### 5. ConcurrentHashMap（CAS + synchronized 锁头 + CounterCell 计数）的size

- `baseCount + CounterCell`累加，无全局锁

### 6. LinkedHashMap（双向链表）+ accessOrder 实现 LRU

- 双向链表维护顺序，可做LRU
