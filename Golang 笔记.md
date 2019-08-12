## Golang 中的map

> 哈希查找表用一个哈希函数将 key 分配到不同的桶（bucket，也就是数组的不同 index）。

> 哈希查找表一般会存在“碰撞”的问题，就是说不同的 key 被哈希到了同一个 bucket。一般有两种应对方法：链表法和 开放地址法。链表法将一个 bucket 实现成一个链表，落在同一个 bucket 中的 key 都会插入这个链表。开放地址法则是碰撞发生后，通过一定的规律，在数组的后面挑选“空位”，用来放置新的 key。


### 1. map 内存模型
在源码中，表示 map 的结构体是 hmap，它是 hashmap 的“缩写”：

```go
// A header for a Go map.
type hmap struct {

	// 元素个数，调用 len(map) 时，直接返回此值
	count int
	flags uint8
	// buckets 的对数 log_2
	B uint8
	// overflow 的 bucket 近似数
	noverflow uint16
	// 计算 key 的哈希的时候会传入哈希函数
	hash0 uint32
	// 指向 buckets 数组，大小为 2^B
	// 如果元素个数为0，就为 nil
	buckets unsafe.Pointer
	// 扩容的时候，buckets 长度会是 oldbuckets 的两倍
	oldbuckets unsafe.Pointer
	// 指示扩容进度，小于此地址的 buckets 迁移完成
	nevacuate uintptr
	extra     *mapextra // optional fields
}
```

* `B`是 buckets 数组的长度的对数，也就是说 buckets 数组的长度就是 2^B。 buckets 是一个指针，最终它指向的是一个结构体：
```go
type bmap struct {
    tophash [bucketCnt]uint8
}
```
* 但这只是表面(src/runtime/hashmap.go)的结构，编译期间会给它加料，动态地创建一个新的结构：
```go
type bmap struct {
    topbits  [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr
}
```
> bmap 就是我们常说的“桶”，桶里面会最多装 8 个 key，这些 key 之所以会落入同一个桶，是因为它们经过哈希计算后，哈希结果是“一类”的。在桶内，又会根据 key 计算出来的 hash 值的高 8 位来决定 key 到底落入桶内的哪个位置（一个桶内最多有8个位置）。

### 2. Key 定位过程

> 将key通过hash函数得出hash值, 取hash值低5位找到对应bucket, 使用hash值高8位来找bucket中tophash, 如果在bucket中没找到, 并且overflow不为空, 就继续去overflow里的bucket中找, 直到找到或所有key槽位都找遍了