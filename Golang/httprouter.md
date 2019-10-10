## 数据结构

### 普通的trie缺点

* 树的深度和路由字符串长度正相关
* 占用较多的内存
* 字符串越长, 匹配超慢

### radix tree

由于同一路由可以有多个method的, radix tree也不能够满足要求

### httprouter 选型
httprouter采用多颗radix tree的数据结构, 一个method对应一颗树

## 定义

```
node: 节点
nType:
    static 非根节点的普通字符串节点
    root 根节点
    param(wildcard) 参数节点, 例如:id
    catchAll 通配符节点, 例如 *filename
path: 到达节点时, 所经过的字符串路径
indices: 子节点索引, 当子节点为非参数类型, 即本节点的wildChild为false时, 会将每个子节点的首字母放在该索引数组. 说是数组, 实际上是个string
```

### httprouter 中radix tree 的构造过程
