---
title: "note"
draft: true
categories:
    - unarchives
tags:
    - draft
------
title: "note"
draft: true
categories:
    - unarchives
tags:
    - draft
---

### 杂七杂八

#### 进程调度相关
常见算法：
    1. 实时进程调度： SCHED_FIIFO, SCHED_RR
    2. 普通进程调度算法：CFS

对应调度器的一些行为：
    优先级上，对于实时进程，高优先级的会持续占用cpu（但一般会默认留下5%的资源给普通进程）
    对于CFS，其一个重要的运行时参数是所谓的vruntime，其公平策略的核心就是保障每个进程在vruntime的数值上尽可能的相同。
    
>我们启动进程时设定的nice值越大，相同时间下vruntime增长的速度就越慢。
    
> 此外CFS的公平机制可以是基于同一个CGROUP下的线程而言的，更具体的说：在cpu.share一致的场景下，CFS会尽可能的让每个thread的vruntime一致，这样会导致同一个CGROUP下的进程开越多的CPU就能获获取到更多的CPU时间片

> 对于CGROUP资源调控可以通过对cgoup.procs和cpu.cfs_quota_us进行配置实现