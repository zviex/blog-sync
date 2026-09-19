
## raft
### quorum

`MajorityConfig`: 一个存储着集群内所有的节点状态的map，他的方法如下
```go
func (c MajorityConfig) CommittedIndex(l AckedIndexer) Index  
func (c MajorityConfig) Describe(l AckedIndexer) string  
func (c MajorityConfig) Slice() []uint64  
func (c MajorityConfig) String() string  
func (c MajorityConfig) VoteResult(votes map[uint64]bool) VoteResult
```
其中CommitedIndex函数中，有一个计算集群的索引算法，他的核心就是将整个集群内部所有节点的提交索引进行从小到大排序，然后获取这个数组的中位数，中位数索引即为整个集群的索引，因为中位数本身代表有大于一半的节点存储了这个日志。raft的半数原则就代表这个索引上日志是被集群认可的日志。

## 实验性Cache库

### demux
dmux是cache库中设计的一个基于ringbuffer(循环队列)的任务分发和配置中心。他的核心定义为
```go
type demux struct {
	mu sync.RWMutex
	// activeWatchers & laggingWatchers hold the first revision the watcher still needs (nextRev).
	activeWatchers  map[*watcher]int64
	laggingWatchers map[*watcher]int64
	resyncInterval  time.Duration
	// Range of revisions maintained for demux operations, inclusive. Broader than history as event revision is not contious.
	// maxRev tracks highest seen revision; minRev sets watcher compaction threshold (updated to evictedRev+1 on history overflow)
	minRev, maxRev int64
	// History stores events within [minRev, maxRev].
	history ringBuffer[[]*clientv3.Event]
}
```
他的主要功能为，所有的watch resp都会写入到dmux的history的这个队列中。然后由具体的watcher去消费内容，两个watchers分别代表活跃的watcher(消费的数据位于history中)和迟钝的watcher(他最新的消费数据已经不在环形队列中)，每个watcher拿到最新的消息消费后，都要更新map里面已经消费的最新的rev值。

这个设计可以保障每个消费者(watcher)可以实现无竞争的消费，而且允许落后，对于已经落后的会采用resync。
