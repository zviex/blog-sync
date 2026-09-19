cloudwego/netpoll是基于一个高性能的非阻塞的网络库。

# 特性
## NoCopyAPI
netpoll 提供了一组无复制的api，本质上是对buffer内部的数据进行原地类型解析和操作，减少了用户态的buffer内容拷贝次数。
常规的网络实现中，对于一个request的body，他的类型是一个io.reader，我们通常使用比如io.read等方法来读取这个缓存区内数据并将其反序列化为对应想要的结构体，这个本身就会发生一次buffer的拷贝。NoCopyApi就是做了这个的优化，让我们直接原地对buffer进行操作。

拥有c开发经验的应该都知道，对于一个比如缓冲区内部的数据，如果想要把他转换为一个对应的结构，我们可以通过某个类型指针去对他进行强制类型转换。nocopy的做法类似，如netpoll内部有一个很简单的实现。

```go
func unsafeSliceToString(b []byte) string {
    return *(*string)(unsafe.Pointer(&b))
}
```
这基本与C中的强制类型转换类似，对于一个指定的bufferr ，采用指针去读取被转换为字符串类型指针，同时解引用他。