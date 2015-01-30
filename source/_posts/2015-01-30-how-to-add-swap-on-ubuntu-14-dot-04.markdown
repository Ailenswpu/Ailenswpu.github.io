---
layout: post
title: "How To Add swap on Ubuntu 14.04"
date: 2015-01-30 20:55:06 +0800
comments: true
categories: 
---

###背景
最近公司几款游戏的staging server是用的是DO的$5的套餐，配置是512M的内存，装了很多软件，在用cap做部署的时候经常报出了内存不足的错误，选择使用swap(交换分区)的方法来一定程度上缓解这个问题。    

###简介
swap(交换分区)是当计算机物理内存不足时用来暂时存储数据的地方，占用的是硬盘空间，当内存不足时候会将内存中不活跃的页转移到swap。swap在一定程度上可以缓解内存不足
的情况，但是由于存在IO交互，效率存在问题。总的来说，当你的内存不足时候为了继续运行，使用swap是一个不错的方案。下面来介绍一下如何在Ubuntu14.04中来增加swap(PS:我
在DO的vps默认是没有swap的)  

####1.查看操作系统的swap的信息
我们可以通过输入以下命令查新系统的swap信息： 

```bash
    sudo swapon -s 
    Filename                Type        Size    Used    Priority    
```
这时候看到系统中没有交换空间，另外可以通过free查看当前系统的内存和交换空间的使用情况：  
```bash
    free -m
                 total       used       free     shared    buffers     cached
    Mem:          3953        154       3799          0          8         83
    -/+ buffers/cache:         62       3890
    swap:            0          0          0
```

####2.查看磁盘当前可用空间

在创建swap之前首先要查看当前磁盘的可用空间，可以通过df命令来查看:   

```bash
    df -h
    
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/vda1        30G  9.9G   19G  36% /
    none            4.0K     0  4.0K   0% /sys/fs/cgroup
    udev            487M  4.0K  487M   1% /dev
    tmpfs           100M  324K  100M   1% /run
    none            5.0M     0  5.0M   0% /run/lock
    none            498M     0  498M   0% /run/shm
    none            100M     0  100M   0% /run/user
```

可以看到磁盘还有接近20G的空间，可以腾出1G来做swap，默认情况下认为swap为RAM的2倍比较合适，不过这个看个人的需求，我的RAM为1G，因此加上2G swap就够了，不过
还是创建了4G的空间。

####3.创建swap文件

要创建swap，首先在文件系统创建一个文件，我们在root(/)下创建一个叫`swapfile`的文件，这个文件必须有足够的大小来做swap，有两种方式可以来创建swap文件：  

#####3.1传统的慢的方法：

```bash
    sudo dd if=/dev/zero of=/swapfile bs=1G count=4
    
    ls -lh /swapfile
    
    -rw-r--r-- 1 root root 4.0G Jan 26 01:43 /swapfile
``` 
#####3.2快速方法：

```bash
    sudo fallocate -l 4G /swapfile
    
    ls -lh /swapfile
    
    -rw-r--r-- 1 root root 4.0G Jan 26 01:50 /swapfile
```
####4.格式化swap文件
当创建好了swap文件之后OS并不知道这个文件的用途，因此我们需要告诉操作系统创建好的文件用作swap。在格式化之前我们必须保证交换空间仅能为root只读，因此需要输入：  

```bash    
    sudo chmod 600 /swapfile
    
    ls -lh /swapfile
    
    -rw------- 1 root root 4.0G Jan 26 01:43 /swapfile
```
可以看到/swapfile文件为root只读。接下来通过mkswap来格式化swap文件：  

```bash
    sudo mkswap /swapfile
    
    Setting up swapspace version 1, size = 4194300 KiB
    no label, UUID=e2f1e9cf-c0a9-4ed4-b8ab-714b8a7d6944
```

文件已经为操作系统识别的swap,通过swapon来加载当前到操作系统：  

```bash
    sudo swapon /swapfile
```
通过以下命令可以验证是否创建成功：  

```bash
    sudo swapon -s
    
    Filename				Type		Size	Used	Priority
    /swapfile                               file		4194300	43220	-1
    
    free -m
    
                 total       used       free     shared    buffers     cached
    Mem:           994        862        131          0         24        118
    -/+ buffers/cache:        718        275
    Swap:         4095         42       4053
```
####5.持久化swap
通过上面创建的交换空间当机器重启的系统不会自动挂载交换空间，我们需要更改fstab文件来自动挂载：  

```bash
    sudo nano /etc/fstab
    在文件最后一行输入：
    /swapfile   none    swap    sw    0   0
```
保存文件即可。  

####6.调节swap设置
注意：在设置交换空间的时候有一些参数影响到系统的性能，需要我们手动来调节。  
`swappiness`参数代表着系统使用交换空间的频率，位于0-100之间，0代表着内核尽可能减少向磁盘交换数据，除非RAM完全被耗光。100代表这尽可能使用交换空间，给RAM腾空间
我们可以看到： 

```bash
    cat /proc/sys/vm/swappiness
    60
```
对于PC机器，60应该是不错的配置，但是对于VPS来说，最好是接近于0，减少swap交互，除非RAM真正被耗光。因此通过增加下面几行来调整`swappiness`到10。  

```bash
    sudo sysctl vm.swappiness=10
    vm.swappiness = 10
```

这个值在重启之后会自动恢复为默认，因此我们需要设置为开机自动设置：  

```bash
    sudo nano /etc/sysctl.conf
    在末尾加上:
    vm.swappiness=10
````

保存文件退出。  
另一个比较重要的配置是`vfs_cache_pressure`,该文件表示内核回收用于directory和inode cache内存的倾向；缺省值100表示内核将根据pagecache和swapcache，
把directory和inode cache保持在一个合理的百分比；降低该值低于100，将导致内核倾向于保留directory和inode cache；增加该值超过100，将导致内核倾向于
回收directory和inode cache。  

```bash
    cat /proc/sys/vm/vfs_cache_pressure
    100
```

默认为100的值时候系统移除inode的频率过高，我们可以通过设置为50来调整频率：

```bash
    sudo sysctl vm.vfs_cache_pressure=50
    vm.vfs_cache_pressure = 50
```
同上，重启过后会自动恢复为默认值，因此需要设置为开机自动设置：

```bash
    sudo nano /etc/sysctl.conf
    vm.vfs_cache_pressure = 50
```
保存文件即可  


####7.总结
swap在我们遇到内存不足的时候是一个不错的选择，尤其像DO的$5套餐的机器，配置好swap可以灵活的帮助我们对机器的使用。