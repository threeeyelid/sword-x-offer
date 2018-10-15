# ![BAT经典面试题 —— 面试中常见问题及考察点](./jpg.jpg)

## 1.笔 ![/面试风格及准备](./jpg.jpg)

### 1.1 实现memcpy函数

```cpp
```

## 2.C/C++基础(上)

## 3.C/C++基础(下)

## 4.智力题

## 5.概率题与操作系统题

## 6.面向对象及数据结构设计

## 7.大数据

### 58.问答题

设计qps (query per sec)函数，用它控制api调用，使得api n毫秒内只能被调用m次?

设计合理即可，下面是一个参考思路： 
1. 维护一个窗口，窗口有左右两个边界。窗口内为从最后一次访问开始向前n毫秒所有的访问；    
2. 当新来一个访问，更新窗口右边界，打新的时间戳。向右移动窗口左边界，将距当前n毫秒外的访问删除；   
3. 统计次数看是否满足<= m次。  若满足说明未达到调用m次的最大限制，否则已达到。  

### 59.问答题

如何设计一个短网址服务系统？

设计合理即可，实现思路：将url哈希到一个唯一的数值，将这个数值转化为一个字符串，另外还需要考虑系统负载等因素。

#### 原理解析

当我们在浏览器里输入 http://t.cn/RlB2PdD 时

1. DNS首先解析获得 http://t.cn 的 IP 地址；  
2. 当 DNS 获得 IP 地址以后（比如：74.125.225.72），会向这个地址发送 HTTP GET 请求，查询短码 RlB2PdD ， http://t.cn 服务器会通过短码 RlB2PdD 获取对应的长 URL；  
3. 请求通过 HTTP 301 转到对应的长 URL https://m.helijia.com 。  

这里有个小的知识点，为什么要用 301 跳转而不是 302 呐？

301 是永久重定向，302 是临时重定向。短地址一经生成就不会变化，所以用 301 是符合 http 语义的。同时对服务器压力也会有一定减少。
但是如果使用了 301，我们就无法统计到短地址被点击的次数了。而这个点击次数是一个非常有意思的大数据分析数据源。能够分析出的东西非常非常多。所以选择302虽然会增加服务器压力，但是我想是一个更好的选择。  
参考：来自知乎 iammutex 的答案https://www.zhihu.com/question/29270034/answer/46446911

#### 算法解析

网上比较流行的算法有两种 自增序列算法、 摘要算法

##### 算法一

自增序列算法，也叫永不重复算法。设置 id 自增，一个 10进制 id 对应一个 62进制的数值，1对1，也就不会出现重复的情况。这个利用的就是低进制转化为高进制时，字符数会减少的特性。

如十进制 10000，对应不同进制的字符表示，2进制最长，62和64进制最短，只有3位数。

短址的长度一般设为 6 位，而每一位是由 [a - z, A - Z, 0 - 9] 总共 62 个字母组成的，所以 6 位的话，总共会有 62^6 ~= 568亿种组合，基本上够用了。

哈哈，这里附上一个进制转换工具 http://tool.lu/hexconvert/ 上图的数据就是用这个工具生成的。

具体的算法实现，自行谷歌。

##### 算法二

1. 将长网址 md5 生成 32 位签名串,分为 4 段, 每段 8 个字节；  
2. 对这四段循环处理, 取 8 个字节, 将他看成 16 进制串与 0x3fffffff(30位1) 与操作, 即超过 30 位的忽略处理；  
3. 这 30 位分成 6 段, 每 5 位的数字作为字母表的索引取得特定字符, 依次进行获得 6 位字符串；  
4. 总的 md5 串可以获得 4 个 6 位串,取里面的任意一个就可作为这个长 url 的短 url 地址。  

这种算法，虽然会生成4个，但是仍然存在重复几率。

##### 两种算法对比

- 第一种算法的好处就是简单好理解，永不重复。但是短码的长度不固定，随着 id 变大从一位长度开始递增。如果非要让短码长度固定也可以就是让 id 从指定的数字开始递增就可以了。百度短网址用的这种算法。上文说的开源短网址项目 YOURLS 也是采用了这种算法。[源码学习](https://github.com/YOURLS/YOURLS/blob/master/includes/functions.php)  
- 第二种算法，存在碰撞（重复）的可能性，虽然几率很小。短码位数是比较固定的。不会从一位长度递增到多位的。据说微博使用的这种算法。

我使用的算法一。有一个不太好的地方就是出现的短码是有序的，可能会不安全。我的处理方式是构造 62 进制的字母不要按顺序排列。因为想实现自定义短码的功能，我又对算法一进行了优化，下文会介绍。

参考：https://hufangyun.com/2017/short-url/

### 60.问答题

如何设计一个网页爬虫系统？

设计合理即可，实现思路：  
- 使用bfs算法进行网站爬取；
- 使用master节点作为控制节点控制worker节点进行网站爬取；  
- 使用分布式队列做任务调度；  
- 使用key-value存储（如redis)做网页判重。

### 61.问答题

- 给一个超过100G大小的log file, log中存着IP地址, 设计算法找到出现次数最多的IP地址？  
- 与上题条件相同，如何找到top K的IP？如何直接用Linux系统命令实现？  

Hash分桶法：   
1. 使用Hash分桶法把数据分发到不同文件。将100G文件分成1000份，将每个IP地址映射到相应文件中：file_id = hash(ip) % 1000 。或者直接对行号进行mod；  
2. 各个文件分别统计top K。在每个文件中分别求出最高频的IP；  
3. 合并 Hash分桶法的结果，得到Top K汇总。 

Linux命令，假设top 10，这里给出两种方法：
1. `sort log_file | uniq -c | sort -nr k1,1 | head -10`  
2. `cat logfile | sort -r | uniq | awk NR==排行数`  

其中，`sort log_file | uniq -c`可以统计`log_file`文件中每行先排序，再统计每行出现的次数。

### 62.问答题

给定100亿个整数，设计算法找到只出现一次的整数

- 如果是有符号整数的话，范围为`-2147483648~2147483647`，无符号整数为`0~4294967296`。有符号的使用两个bitset，一个存放正数，一个负数。   
- 每个数使用两个位来判断其出现几次。00表示出现0词，01出现1次，10出现大于一次。  

比如说存放整数100，就将bitset的第`100*2`位设置为+1，当所有数放完之后，对每两位进行测试看其值为多少？若是第i为与i+1为的值为01，则这个整数：`i*2`，在集合中只出现了1次。需要总共用`bitnum=(2^31*2)`个位表示，因为是位，1字节占8位，所以总共有`2^31*2/8`字节，从B到KB到MB，总共需空间为`int[bitnum]`，即512M。

#### 补充: Bloom filter  

Bloom filter 是由 Howard Bloom 在 1970 年提出的二进制向量数据结构，它具有很好的空间和时间效率，被用来检测一个元素是不是集合中的一个成员。

##### 计算方法

如需要判断一个元素是不是在一个集合中，我们通常做法是把所有元素保存下来，然后通过比较知道它是不是在集合内，链表、树都是基于这种思路，当集合内元素个数的变大，我们需要的空间和时间都线性变大，检索速度也越来越慢。

Bloom filter 采用的是哈希函数的方法，将一个元素映射到一个 m 长度的阵列上的一个点，当这个点是 1 时，那么这个元素在集合内，反之则不在集合内。这个方法的缺点就是当检测的元素很多的时候可能有冲突，解决方法就是使用 k 个哈希 函数对应 k 个点，如果所有点都是 1 的话，那么元素在集合内，如果有 0 的话，元素则不在集合内。

##### 优缺点

- Bloom filter 优点就是它的插入和查询时间都是常数，另外它查询元素却不保存元素本身，具有良好的安全性。  
- 它的缺点也是显而易见的，当插入的元素越多，错判“在集合内”的概率就越大了，另外 Bloom filter 也不能删除一个元素，因为多个元素哈希的结果可能在 Bloom filter 结构中占用的是同一个位，如果删除了一个比特位，可能会影响多个元素的检测。

##### 简单例子

下面是一个简单的 Bloom filter 结构，开始时集合内没有元素

![bloom_filter](./assets/bloom_filter_01.jpg)

当来了一个元素 a，进行判断，这里哈希函数有两个，计算出对应的比特位上为 0 ，即是 a 不在集合内，将 a 添加进去：

![bloom_filter](./assets/bloom_filter_02.jpg)

之后的元素，要判断是不是在集合内，也是同 a 一样的方法，只有对元素哈希后对应位置上都是 1 才认为这个元素在集合内（虽然这样可能会误判）：

![bloom_filter](./assets/bloom_filter_03.jpg)

随着元素的插入，Bloom filter 中修改的值变多，出现误判的几率也随之变大，当新来一个元素时，满足其在集合内的条件，即所有对应位都是 1 ，这样就可能有两种情况，一是这个元素就在集合内，没有发生误判；还有一种情况就是发生误判，出现了哈希碰撞，这个元素本不在集合内。

![bloom_filter](./assets/bloom_filter_04.jpg)

参考：bloom filter_百度百科  
- https://baike.baidu.com/item/bloom%20filter/6630926