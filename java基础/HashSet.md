# HashSet

`HashSet` 是一个不允许存储重复元素的集合，它的实现比较简单，只要理解了 `HashMap`，`HashSet` 就水到渠成了。

## 成员变量
首先了解下 `HashSet` 的成员变量:

```
    private transient HashMap<E,Object> map;

    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();
```

发现主要就两个变量:

- `map` ：用于存放最终数据的。
- `PRESENT` ：是所有写入 map 的 `value` 值。

## 构造函数

```
    public HashSet() {
        map = new HashMap<>();
    }
    
    public HashSet(int initialCapacity, float loadFactor) {
        map = new HashMap<>(initialCapacity, loadFactor);
    }    
```
构造函数很简单，利用了 `HashMap` 初始化了 `map` 。

## add

```
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

比较关键的就是这个 `add()` 方法。
可以看出它是将存放的对象当做了 `HashMap` 的健，`value` 都是相同的 `PRESENT` 。由于 `HashMap` 的 `key` 是不能重复的，所以每当有重复的值写入到 `HashSet` 时，`value` 会被覆盖，但 `key` 不会受到影响，这样就保证了 `HashSet` 中只能存放不重复的元素。

## 总结

`HashSet` 的原理比较简单，几乎全部借助于 `HashMap` 来实现的。

所以 `HashMap` 会出现的问题 `HashSet` 依然不能避免。

# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 