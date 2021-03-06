#### top
```bash
# CPU 平均负载表示
假设我们在一个单 CPU 系统上看到平均负载为 1.73，0.60，7.98，那么说明在过去 1 分钟内，系统有 73% 的超载，而在 15 分钟内，有 698% 的超载，从整体趋势来看，系统的负载在降低
```

#### CPU高，排查
```bash
一、什么是平均负载
正确定义：单位时间内，系统中处于可运行状态和不可中断状态的平均进程数。
错误定义：单位时间内的cpu使用率。
可运行状态的进程：正在使用cpu或者正在等待cpu的进程，即ps aux命令下STAT处于R状态的进程
不可中断状态的进程：处于内核态关键流程中的进程，且不可被打断，如等待硬件设备IO响应，ps命令D状态的进程
理想状态：每个cpu上都有一个活跃进程，即平均负载数等于cpu数
过载经验值：平均负载高于cpu数量70%的时候

二、相关命令
cpu核数: lscpu、 grep ‘model name’ /proc/cpuinfo | wc -l
显示平均负载：uptime、top，显示的顺序是最近1分钟、5分钟、15分钟，从此可以看出平均负载的趋势
watch -d uptime: -d会高亮显示变化的区域
strees: 压测命令，–cpu cpu压测选项，-i io压测选项，-c 进程数压测选项，–timeout 执行时间
mpstat: 多核cpu性能分析工具，-P ALL监视所有cpu
pidstat: 进程性能分析工具，-u 显示cpu利用率

三、平均负载与cpu使用率的区别
CPU使用率：单位时间内cpu繁忙情况的统计
情况1：CPU密集型进程，CPU使用率和平均负载基本一致
情况2：IO密集型进程，平均负载升高，CPU使用率不一定升高
情况3：大量等待CPU的进程调度，平均负载升高，CPU使用率也升高

四、平均负载过高时，如何调优
工具：stress、sysstat，yum即可安装

CPU密集型进程case：
mpstat -P ALL 5: -P ALL表示监控所有CPU，5表示每5秒刷新一次数据，观察是否有某个cpu的%usr会很高，但iowait应很低
pidstat -u 5 1：每5秒输出一组数据，观察哪个进程%cpu很高，但是%wait很低，极有可能就是这个进程导致cpu飚高
IO密集型进程case：
mpstat -P ALL 5: 观察是否有某个cpu的%iowait很高，同时%usr也较高
pidstat -u 5 1：观察哪个进程%wait较高，同时%CPU也较高
大量进程case：
pidstat -u 5 1：观察那些%wait较高的进程是否有很多
```

#### 路由
```powershell
@echo off
route add 10.123.0.0/16 192.168.2.126
route delete 0.0.0.0 192.168.2.126
```
