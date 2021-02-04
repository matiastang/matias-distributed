<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 15:02:57
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 15:15:08
 * @Description: 一致性哈希算法 CARP
-->
# 一致性哈希算法 CARP

[算法网-carp协议](http://ddrv.cn/a/604406)

在后端服务开发的过程中, 遇到了这样一个问题: 需要在 mysql 前面部署 redis 做一层缓存, 要求 redis 是集群部署, 并且每台 redis 节点只缓存总数据量的 1/N, N 为 redis 的个数.

看到这里大家都能想到到一个方法是使用 hash(key)%N 来选取 redis 进行 value 的存取, 这种方式当然可以很均匀的将数据分配到 N 个 redis 服务上, 并且实现起来也非常的简单. 但是使用这种哈希取余的方式有一个很大的问题, 那就是当 redis 集群扩容或者缩容, 或者发生宕机的时候, 也就是上述公式中的 N 发生变化的时候, 这个时候 hash(key)%N 的值保持不变的概率非常小, 换句话说, 缓存系统会在这种情况下整个失效, 这对于后端的 mysql 来说瞬时压力会非常大, 很可能造成 mysql 的奔溃, 进而造成整个服务的不可用.

所以必须想一种办法来应对上述的情况, 即当一台 redis 服务挂掉的时候, 能不能做到只有 1/N 的缓存失效呢?

答案就是使用一致性哈希算法 CARP, 严格来讲 CARP 并不是一种算法, 而是一种协议, Cache Array Routing Protocol，Cache 群组路由协议. 下面来介绍些下它的工作原理:

首先假设有 N 个 redis 服务, 分别是 redis1, redis2 .... redisN, key 是想要获取的数据在 redis 中的键.

第一步, 计算所有的服务名与 key 的哈希值:

hash_v1 = hash(redis1 + key)
hash_v2 = hash(redis2 + key)
...
hash_vN = hash(redisN + key)
第二步, 计算值最大的 hash_vX, 那么选中的便是 redisX:

hash_vX = max(hash_v1, hash_v2, ..., hash_vN)
为什么这么做就可以达到增加一台 redis 服务的时候, 只有 1/(N+1) 的缓存失效呢?

hash_vX1 = max(hash_v1, hash_v2, ..., hash_vN)
hash_vX2 = max(hash_v1, hash_v2, ..., hash_vN, hash_vN+1)
看上面两个表达式, 如果要求 hash_vX2 大于 hash_vX1, 那么必须 hash_vN+1 大于前面 N 个哈希值, 那么你选取的 hash 函数足够散列的话, hash_vN+1 大于前面 N 个哈希值的概率为 1/(N+1). 你可以自行算一下减少一台 redis 服务时, 缓存失效的概率.

最后, 附上 Golang 实现版本:
```go
package carp

import (
    "crypto/md5"
    "errors"
)

var (
    ErrHaveNoRedis = errors.New("have no redis")
)

// 根据 carp 算法, 从 redisArr 的数组中选择合适的 redis, 返回值为下标
func GetTargetIndex(key string, redisArr []string) (idx int, err error) {
    if len(redisArr) < 1 {
        return -1, ErrHaveNoTarget
    } else if len(redisArr) == 1 {
        return 0, nil
    }

    hashArr := make([]string, len(redisArr))
    for k, v := range redisArr {
        hashArr[k] = hashString(v + key)
    }
    idx = minIdx(hashArr)
    return
}

// 返回 arr 数组中最小值的下标
func minIdx(arr []string) (idx int) {
    if len(arr) < 1 {
        return -1
    }

    idx, min := 0, arr[0]

    for k, v := range arr {
        if v < min {
            idx = k
            min = v
        }
    }
    return
}

func hashString(str string) (hash string) {
    md5sum := md5.Sum([]byte(str))
    return string(md5sum[:])
}
```
上面的代码中使用了 md5 的哈希算法, 如果你对性能有更高的要求可以使用 [FNV](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) 哈希算法.