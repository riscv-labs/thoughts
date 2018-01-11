# **labeled resource management for supporting differentiated services in Data Center**

# Abstract

在数据中心中，为保证服务质量，通过调低资源利用率来和硬件独占来保证延迟敏感型应用的QoS。为提高数据中心的整体执行效率，需要在保证应用的QoS（比如响应延迟）的前提下，提高服务器的资源利用率。通过分析challenge of  保持low-latency workloads的同时提高其他负载，找出破坏QoS的原因是在硬件和软件层面上提供的共享机制无法有效地实现时间和空间的优先级信息传递与优先级隔离。为此，我们提出基于label的跨OS和CPU的优先级传递机制，并基于当前先进的CPU硬件隔离机制，通过建立OS资源调度器来动态且细粒度地实现QoS隔离和资源利用率最大化。效果XXX

# 1. Introduction

需求分析：

目前是否可以把阿里云生产环境的应用分类为LC，BE两类？有其他类型吗？

通过保证LC服务足够QoS的情况下，最大限度的将空闲资源用来运行BE服务，阿里云生产环境能产生多大的收益。进一步分析...

另外，如果在现有条件下让LC服务和BE服务的一般办法是：软件方式：CPU/Cache通过Linux Scheduling， Memory通过VM，network通过TC， block设备 IO调度器，这些只需要粗粒度的硬件信息，比如时间/内存大小。硬件方式：PARD, CAT，直接通过静态方式进行调整，也有通过动态方式进行调整的，比如 Heracles，等。进一步分析



# 2. Shared Mechanism Interference

## 2.1 Last Level Cache

## 2.2. DRAM bandwidth

## 2.3 Software Lock

## 2.4 block/net IO traffic

# 3.  Latency-critical Workloads Analysis

## 3.1 web search

## 3.2 deep NN

## 3.3 memcache



H-CPU/L-MEM...

L-CPU/H-Cache/H-MEM...

H-Ccache/H-MEM/H-LOCK

H-MEM/H-IO

# 4. Resource Management（RM） Design

## 4.1 RM architecture

### 跨层次label传递

通过label方式，让app能把其相关的信息传递给kernel，kernel再进一步传递给pard。

### 细粒度在线反馈与控制

基于label机制，OS能够获取app的Latency/throughput信息，也能获取cpu的LLC/MEM BW/NET Queue/BLK Queue的信息，然后基于某些算法进行空间大小的调整或优先级的调整。

## 4.2 workload perceptron

offline & online结合

通过label方式，对于LC服务，首先通过offline方式提前知道Latency/throughput的下限，以及对应的LLC/MEM BW/LOCK/Running Queue/NET Queue(NQ)/BLK Queue(BQ)/Time Slice(TS)特征信息；然后在实际运行中，online地基于label，随时获取当前的Latency/throughput。需要修改内核。

如果能够进一步分析出，调整哪些特征信息（LLC/MEM BW/NET Queue/BLK Queue/Time Slice特征信息），可以最有效地提升/降低LC服务的性能，就更好了。

由于app的label通过某种方式，可以传递到hardware中，所以OS也能够同时获取与此应用相关的LLC/MEM BW/NET Queue/BLK Queue的信息。为后续资源管理提供依据。

4.3  资源调度策略

基于label方式，确定调度管理策略：调整 LLC/BW/LOCK/RQ/NQ/BQ/TS

TOP level，确定最有效的几种，

目标：对于某种情况，达到近似最优解（latency逼近Deadline, SE的through尽量大）

当前考虑的是针对某种情况(input：latency down，并进一步体现在某些label的resource的变化上，取相对变化），进行多种参数的混调（output: 基于最有效的多个参数，包括label资源进行调整，恢复label resource的变化）。这个是在线调整出来的。

梯度，对于接近Deadline：最速下降；对于其他情况：缓慢下降。



4.4 Evaluation



# questions

## 1 service分类

## 2 混合模式类型

## 3 label的跨层问题

## 当前考虑的是针对某种情况(input：latency down，并进一步体现在某些label的resource的变化上，取相对变化），进行多种参数的混调（output: 基于最有效的多个参数，包括label资源进行调整，恢复label resource的变化）。4 label的体现/表示

## 5 优化的方法

## 6 搭建原型实验环境

1. 服务器，RISCV开发板
2. 测试用例
3. 各种参数采集，资源调整的优化框架