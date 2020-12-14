---
title: criu学习
date: 2020-10-09 08:43:41
categories: 工具
tags: 容器迁移
---
# 官网
```
https://criu.org/Checkpoint/Restore
```
主要依赖/proc 文件系统
从这里获得的数据
1 文件描述符信息（/proc/$pid/fd和/proc/$pid/fdinfo）
2 通道参数
3 内存映射 （/proc/$pid/maps和/proc/$pid/map_files）

## 进程备份的步骤
### 1 收集进程树并进行冻结
进程组的父节点的$pid 可以通过命令行（--option）参数获取，使用$pid,备份器能够遍历/proc/$pid/task目录，收集线程信息，遍历/proc/$pid/task/$tid/children迭代收集子进程的信息，最后进行冻结
### 2 收集任务的资源并备份
CRIU会读取所有与被收集的任务相关的信息并且把他们写到备份文件，资源的获取方式通过如下几种：
（1）虚拟存储区域通过/proc/$pid/smaps解析，并且映射文件通过 ／proc/$pid/map_files读取
（2）文件描述符的数量通过/proc/$pid/fd读取
（3）任务的内核参数通过ptrace接口并解析/proc/$pid/stat数据项来备份
CRIU通过ptrace接口给任务注入来parasitecode（寄生代码），这分为两个步骤：首先我们仅仅在任务所具有的 CS（代码段段地址）:IP（即将执行下一条指令的地址，偏移地址）给mmap系统调用注入少量字节，然后ptrace允许我们运行一个被注入的系统调用并且我们为寄生代码块分配来足够的内存来备份，此后寄生代码被拷贝到一个在被备份地址空间和CS:IP集中新的地方
### 3 清理备份文件
在所有文件信息都完成备份后（例如内存页面，这只能从内部被备份的地址空间写出），我们再次使用ptrace工具来恢复被备份的内容，通过丢弃掉寄生代码并恢复原始的代码，然后CRIU解除和任务的绑定，让他们恢复运行

## 恢复步骤
### 1解析共享资源
这个步骤,CRIU读取镜像文件并且找出哪个进程共享哪个资源，之后共享的资源通过某一个进程被恢复并且所有其他共享的进程在第二阶段（比如会话）继承或通过某种其他的方式获取，例如带有SCM_CREDS消息的共享文件通过unix sockets发送，共享内存区域通过memfd文件描述符恢复。
### 2创建进程树
在这个步骤，CRIU多次调用fork（）来重建需要恢复的进程组。注明：线程不是在这个步骤恢复的，而是在第四个步骤恢复

fork（）函数会在以后进行深入的研究
### 3恢复基础的任务资源
CRIU恢复所有的资源除了：
（1）内存映射准确位置
（2）时钟
（3）credentials（信任证书文件？）
（4）线程
以上四个类型的资源被延迟到最后一个步骤恢复，在这个阶段CRIU打开文件，准备namespaces，映射（带有数据的文件）私有内存区域，创建套接字，调用chdir（）和chroot（）并且处理一些其它事务。
### 4转换到被恢复的上下文环境，恢复步骤3中没有恢复的资源

## criu的命令行参数解析
同样，执行criu命令后进入主函数入口进行参数解析再执行具体的函数流程：

int main(int argc, char *argv[], char *envp[])｛｝//argc表示参数个数，argv存放所有参数数据

主函数实现如下几个步骤：
(1) 定义所有可选默认的参数选项结构体option＝｛｛”tree”,required_argument,0,’t’｝，｛”pid”,required_argument,0,’p’｝(这只是其中的两个例子，还有其它所有的默认可选参数) ｝

(2) 循环遍历输入命令行的参数并解析对应操作
```
 while (1) {
		idx = -1;
		opt = getopt_long(argc, argv, short_opts, long_opts, &idx);//详见http://blog.csdn.net/cashey1991/article/details/7942809解析参数，每次获取一个参数的信息opt，包括参数名称，参数值，索引等，这里每分析一个参数将会把argv中的命令名称（如dump，restore等）往数组末端后移两位，所以分析完所有参数后，命令名称位于argv数组的最后一个位置。
		if (opt == -1)//都到命令行末尾
			break;
		if (!opt)
			continue;
		switch (opt) {
		case 's':
			opts.final_state = TASK_STOPPED;
			break;
		case 'R':
			opts.final_state = TASK_ALIVE;
			break;}
			（...还有很多)
			}

```
通过循环解析参数来设置opts（包含所有可选参数的默认值），循环结束后，所有参数解析都已完成，且criu命令名称（dump，restore等）已经移到argv［］末位，所以直接分析argv［optind］即可以知道具体执行什么命令，如对dump命令的分析：
```
 if (!strcmp(argv[optind], "dump")) {//比较argv最后一个字符串是否匹配为dump，是则调用相关函数进行进程备份
		if (!tree_id)
			goto opt_pid_missing;
		return cr_dump_tasks(tree_id);//具体备份操作函数
	}
	
	if (!strcmp(argv[optind], "restore")) {//比较argv最后一个字符串是否匹配为restore，是则调用相关函数进行进程恢复
		if (tree_id)
			pr_warn("Using -t with criu restore is obsoleted\n");
		ret = cr_restore_tasks();//具体恢复操作函数
		if (ret == 0 && opts.exec_cmd) {
			close_pid_proc();
			execvp(opts.exec_cmd[0], opts.exec_cmd);
			pr_perror("Failed to exec command %s", opts.exec_cmd[0]);
			ret = 1;
		}
		return ret != 0;
	}
```

## criu dump 命令执行过程
当我们执行criu dump命令时，我们经过参数分析知道dump的执行函数为cr_dump_tasks（）
```
int cr_dump_tasks(pid_t pid)
{
	InventoryEntry he = INVENTORY_ENTRY__INIT;
	struct pstree_item *item;//定义进程树节点结构体
	int pre_dump_ret = 0;
	int ret = -1;
	pr_info("========================================\n");
	pr_info("Dumping processes (pid: %d)\n", pid);
	pr_info("========================================\n");
	root_item = alloc_pstree_item();//首先分配进程树根节点内存空间
	if (!root_item)
		goto err;
	root_item->pid->real = pid;
	pre_dump_ret = run_scripts(ACT_PRE_DUMP);//进行pre－dump
	if (pre_dump_ret != 0) {
		pr_err("Pre dump script failed with %d!\n", pre_dump_ret);
		goto err;
	}
	...
	}
```

进入dump备份的具体流程：

1 init_stats(DUMP_STATS)//初始化备份dump状态信息，分配内存空间存放dump_stats结构体信息：
```
struct dump_stats {
	struct timing	timings[DUMP_TIME_NR_STATS];//保存了备份各个步骤的开始时间和花费时间信息
	unsigned long	counts[DUMP_CNT_NR_STATS];
};
```
2 cr_plugins_init(cr_plugins_stage_dump)
初始化链表头->设置函数库目录opts.libdir->循环读取库目录readdir()函数读取目录的下一入口，循环读取并cr_lib_load()加载库函数（加载步骤：dlopen()打开动态链接库返回句柄h，dlsym(h,”CR_PLUGINS_DESC”)通过调用句柄可以找到函数库中指定函数名称的函数指针并返回调用）

3 kerndat_init()
加载缓存cache，读取内核缓存文件criu.kdat
预加载socket_module
预加载网络过滤器模块
读取proc目录下内存页面并检查pread(fd,&pfn,sizeof(pfn),offset)读取文件带有偏移量的指定大小的数据
kerndat_get_shmemdev()获取共享内存：首先mmap将文件映射到内存,返回映射内存地址，根据内存地址将内存写入maps对象，通过stat（maps,&buf）函数获取maps对象信息写至buf对象,设置dev＝buf.st_dev,最后mumap(start,length)关闭内存映射
kerndat_get_dirty_track():将文件映射到内存－>do_task_reset_dirty_track(pid)重置脏bit标示位->打开proc配置文件，读取相关内容－>取消内存映射。
init_zero_page_pfn()初始化零页面帧号
get_last_cap():定义了一个系统调用请求结构体req，根据req相关信息进行系统调用sysctl_op
kerndat_fdinfo_has_lock():打开proc目录下locks文件，在根据pid获取指定proc下锁文件内容
get_task_size():获取任务大小
get_ipv6():进入/proc/sys/net/ipv6目录获取ipv6地址
kerndat_loginuid():登录uid？
kerndat_iptables_has_xlocaks():调用cr_system（）对userns进行C/R?
kerndat_tcp_repair():修复内核tcp连接
kerndat_compat_restore():内核兼容性恢复
kerndat_has_memfd_create():创建内存文件描述符。
kerndat_lsm():内核安全模块
kerndat_mmap_min_addr():内存映射最小地址？
kerndat_save_cache():内核缓存保存至缓存文件

4 irmap_load_cache():这个步骤不能理解

5 cpu_init():初始化cpu信息

6 vdso_init():vdso全称：Virtual Dynamically-linked Shared Object用于将内核态的调用映射到用户态，便于Linux新特性的加入来兼容glibc各不同版本。

7.cgp_init():cgroup初始化

8.parse_cg_info():解析CGroup信息

9.prepare_inventory():准备镜像工程？

10.cpu_dump_cpuinfo():备份cpu信息

11.connect_to_page_server():连接到Page_Server

12.setup_alarm_handler():安装警报处理器

13.collect_pstree():收集进程树信息

14.collect_pstree_ids():收集进程树id

15.network_lock():冻结网络操作

16.collect_namespaces():收集命名空间信息

17.cr_glob_imgset_open():不能够理解

18.collect_seccomp_filters():收集安全计算过滤器信息

### 数据信息收集完毕，后面对收集的信息进行备份处理

19.for_each_pstree_item(item) :dump_one_task(item):对每个树单节点进行备份

20.dead_pid_conflict():检查死pid故障：当一个进程已经完成的时候，但它在／proc／PID下的文件被其它的进程打开，然后该pid被给到新的线程，出现这样的情况是无法备份的。

21.dump_mnt_namespaces:备份mnt_namespaces（）命名空间

22.dump_file_locks（）:备份文件锁

23.dump_verify_tty_sids():备份验证终端ttySID设备

24.dump_zombies():备份僵尸进程

25.dump_pstree(root_item):根据根节点备份进程树

26.dump_cgroups():备份CGROUP信息

27.cr_dump_shmem():备份共享内存

28.fix_external_unix_sockets():修复外部unix sockets

29.tty_post_actions():终端post操作？

30.write_img_inventory(&he):写入镜像工厂


## criu dump 重要步骤

### 信息收集的过程中：
1 进程树的收集
2 进程树id的收集
3 对网络的锁操作
4 命名空间信息的收集

### 信息备份的过程
1 对每个树节点的备份
2 mnt_namespace命名空间的备份
3 文件锁信息备份
4 根据根节点备份进程树
5 备份CGroup信息
6 备份共享内存
### 写入镜像工厂



# 安装




# 使用

## criu check 

looks good 表示已经正常安装，

但是在ubuntu系统下，使用sudo criu check命令仍有如下信息的报错
```
Warn  (criu/net.c:2840): Unable to get socket network namespace
Warn  (criu/net.c:2840): Unable to get tun network namespace
Warn  (criu/sk-unix.c:229): unix: Unable to open a socket file: Bad address
Warn  (criu/net.c:2840): Unable to get socket network namespace
Warn  (criu/autofs.c:79): Failed to find pipe_ino option (old kernel?)
Looks good.

```


# 