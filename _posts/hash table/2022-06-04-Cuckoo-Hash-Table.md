---
title: "「Cuckoo Hashing」"
subtitle: "Implementation and Pricinples of Cuckoo Hashing"
layout: post
author: "XionYono"
header-style: text
hidden: false
tags:
  - Hash table
  - Collections
---



[TOC]



# Cuck hashing

要理解Cuckoo hashing，看[原论文][1]比较难懂，因为当时并没有以Cuckoo graph的角度来诠释和证明Cuckoo hashing。推荐斯坦福的教学PPT：https://web.stanford.edu/class/archive/cs/cs166/cs166.1146/lectures/13/Small13.pdf

# Optimistic cuckoo hashing

- [partial key cuckoo hashing][6]

  - 当Key很大时，为了压缩Hash table的大小，通常存储Key的指针。但是使用指针时，在对Hash table查找时，需要比较Key，因此需要解引用。通常为了减少解引用带来的开销和挤占Cache，使用tag（一般是hash value的部分或全部）来代替Key比较，只有当tag相等时，才会解引用进一步比较Key。

  - 原始的Cuckoo hash table插入过程中如果发生替换，那么需要解引用以计算Hash value从而获取另一张table中的替换位置。因此可以使用该替换位置作为tag。

  - 使用另一张表的索引作为tag的缺点在于 限制了hash table的最大大小，比如tag取8bit的话，那么hash table的最大大小就是 256. 因此 Optimistic cuckoo hashing 采用hash value的低8bit位作为tag（适合高位差异的概率较小，低位差异概率较大的情况）。

    

- [Associativity][6]

  - 原始的Cuckoo hash table的中的bucket为1-set associativity。导致空间利用率很低。
  - 4-set associativity 的bucket的空间利用率能够达到95%

- Tag-Based insertion

  - 避免了Hash(key)

- Insertion 操作优化

  - 先查找有效路径（并行跟踪），然后在执行交换操作。

## Hash Table Structure

```c
int kick_count;
static int cuckoo(int depth) {
    int cur;
    size_t idx;

    kick_count = 0;    
    while (1) {

        cur = cp_search(depth, &idx);
        if (cur < 0)
            return -1;
        assert(idx >= 0);
        cur = cp_backmove(cur, idx);
        if (cur == 0) {
            return idx;
        }

        depth = cur - 1;
    }

    return -1;
}

size_t j;
    idx = cuckoo(depth);
    if (idx >= 0) {
        i1 = cp_buckets[depth][idx];
        j = cp_slots[depth][idx];
        if (buckets[i1].vals[j] != 0)
            printf("ououou\n");
        if (try_add(it, tag, i1, lock))
            return 1;
        printf("mmm i1=%zu i=%d\n", i1, idx);
    }

    printf("hash table is full (hashpower = %d, hash_items = %u, load factor = %.2f), need to increase hashpower\n",
           hashpower, hash_items, 1.0 * hash_items / bucket_size / hashsize);
    return 0;
```





# 基于CLOCK的LRU替换算法

基于CLOCK的LRU替换算法实现所需要的元数据要比传统LRU替换算法的实现更少。这样每个对象占用内存就会减少。

# Optimistic locking

用于线程间同步。在MemC3中使用该同步机制确保hash table 的lookup/ insert, LRU 替换的原子操作和一致性。

先不论是如何高效，但毕竟是基于锁的，效率肯定会降低。

# User Space Read-Copy Update 

RCU实现分为Linux kernel space RCU 和 User Space RCU。

## User Space RCU Vs Kernel Space RCU

## User Space RCU Implementation

## DPDK RCU Library





# Batch Insertion

源于[CuckooSwtich][4] 中，有点没看明白，什么是Batch Insertion。



# Flow Cache

## Microflow Caching





# Hugepage

利用Hugepage提高TLB命中。Cuckooswitch中展示了使用Hugepage能够有效提高cuckoo hash table的查找，更新速率。







# 参考文献

[1]: https://www.sciencedirect.com/science/article/pii/S0196677403001925 "Pagh, Rasmus, and Flemming Friche Rodler. "Cuckoo hashing." *Journal of Algorithms* 51.2 (2004): 122-144."
[2]: https://www.usenix.org/conference/nsdi15/technical-sessions/presentation/pfaff "Pfaff, Ben, et al. "The Design and Implementation of Open {vSwitch}." *12th USENIX symposium on networked systems design and implementation (NSDI 15)*. 2015."
[3]: https://www.usenix.org/conference/nsdi13/technical-sessions/presentation/fan "Fan, Bin, David G. Andersen, and Michael Kaminsky. "{MemC3}: Compact and Concurrent {MemCache} with Dumber Caching and Smarter Hashing." *10th USENIX Symposium on Networked Systems Design and Implementation (NSDI 13)*. 2013."
[4]: https://dl.acm.org/doi/abs/10.1145/2535372.2535379 "Zhou, Dong, et al. "Scalable, high performance ethernet forwarding with cuckooswitch." *Proceedings of the ninth ACM conference on Emerging networking experiments and technologies*. 2013."
[5]: https://ieeexplore.ieee.org/abstract/document/8117530/ "Yu, Ye, et al. "A concise forwarding information base for scalable and fast name lookups." *2017 IEEE 25th International Conference on Network Protocols (ICNP)*. IEEE, 2017."
[6]: https://www.usenix.org/conference/nsdi11/memory-efficient-high-performance-key-value-store "Lim, Hyeontaek, et al. "A {Memory-Efficient},{High-Performance}{Key-Value} Store." (2011)."
[7]: https://ieeexplore.ieee.org/abstract/document/5871597 "Desnoyers, Mathieu, et al. "User-level implementations of read-copy update." *IEEE Transactions on Parallel and Distributed Systems* 23.2 (2011): 375-382."

