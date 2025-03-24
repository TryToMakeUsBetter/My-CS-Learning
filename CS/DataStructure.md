# 常见数据结构

## 顺序表

顺序结构
典型是Slice之类的。

## 链表

### 结构

```go
type Node struct{
    Val int
    Next *Node
}

type List struct{
    Head *Node
}
```

## 树

## 图

### 优劣

增删通过操作指针可以很快完成，但是查找某一个值不方便

### 常见面试题

#### 反转链表

```go
mark:=&Node{
    val,
    head,
}
pre := mark
cur := pre.Next
for{
    nxt = cur.Next
    cur.Next=nxt.Next
    nxt.Next=pre.Next
    pre.Next=nxt
}
```
