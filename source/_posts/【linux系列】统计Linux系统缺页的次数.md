---
title: 【linux系列】统计Linux系统缺页的次数
author: HWH
math: true
mermaid: true
tags:
  - Linux
  - 操作系统
categories:
  - - Tutorial
abbrlink: fbd4b29
date: 2022-12-10 20:39:17
---



### 编译环境
- 虚拟机操作系统： `ubuntu-20.04.3-desktop-amd64`
- 原系统内核版本：`5.11.0-46-generic`
- 内核源码版本：`5.11.1`

### 修改任务
- 通过在Linux内核中自建变量，并利用 `/proc` 文件系统作为中介的方法，统计一段时间内系统缺页的次数。
- 在内核中实现缺页次数统计。
- 新建内核模块，编译并安装新内核。
- 使用定时器设置开始和结束时间，通过 `/proc` 在用户态下查看该时间段内的缺页次数。

### 修改准备
-  [下载内核源码](https://cdn.kernel.org/pub/linux/kernel/)

### 修改源码
- 需要修改的文件(默认从源码目录开始)如下：

```
arch/x86/mm/fault.c

include/linux/mm.h

kernel/kallsyms.c
```

#### 修改 arch/x86/mm/fault.c
- 定义 **pfcount** 变量
- 搜索 `trace_page_fault`，并插入如下代码：

```c
unsigned long volatile pfcount;
```

- 效果如下：

![](https://img-blog.csdnimg.cn/76a9a2504ce24e6ca2a4fc81b1cf372b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 搜索 `good_area:`，并添加如下代码

```c
pfcount++;
```

- 效果如下：

![](https://img-blog.csdnimg.cn/72f2a55af24f4e118769d5df0f89ea2d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 include/linux/mm.h
- 在 `extern int page_cluster;` 语句之后，添加如下代码：

```c
extern unsigned long volatile pfcount;
```

- 效果如下：

![](https://img-blog.csdnimg.cn/7775e4df98ec4a6b95963cada051ad11.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改 kernel/kallsyms.c
- 在最后一行添加如下代码：

```c
EXPORT_SYMBOL(pfcount);
```

- 效果如下：

![](https://img-blog.csdnimg.cn/a58f8ec248cc4078b1df04ba9e01e842.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)


### 编译安装
- 无需修改配置文件，直接编译即可
- 安装后重启进入新系统
- 内核编译相关问题可以参考我之前的文章，[【linux系列】内核编译最新教程+常见问题总结](https://blog.csdn.net/h2763246823/article/details/122522326)

### 编写模块
- 这里提供一份完整源码(readpfcount.c)：

```c
#include <linux/module.h>
#include <linux/sched.h>
#include <linux/uaccess.h>
#include <linux/proc_fs.h>
#include <linux/fs.h>
#include <linux/mm.h>
#include <linux/seq_file.h>
#include <linux/slab.h>
#include <linux/kernel.h>
#include <linux/timer.h>
#include <linux/jiffies.h>
#include <stdarg.h>

extern unsigned long volatile pfcount;
static struct timer_list test_timer;
static unsigned long pfcount_last;
static unsigned long pfcount_in_2;
static int count = 0;

MODULE_LICENSE("GPL");


/*5,实现show函数
  作用是将内核数据输出到用户空间
  将在proc file输出时被调用
  */
static int my_proc_show(struct seq_file *m, void *v)
{
    /*这里不能使用printfk之类的函数
      要使用seq_file输出的一组特殊函数
      */
	seq_printf(m, "[latest] Number of page fault interrupts in 2 seconds: %ld !\n", pfcount_in_2);
    return 0;
}

//定时器的回调函数
static void irq_test_timer_function(struct timer_list  *timer)
{

	printk("%d Number of page fault interrupts in 2 seconds: %ld\n",count,pfcount - pfcount_last);
	pfcount_in_2 = pfcount - pfcount_last;

	pfcount_last = pfcount;
	mod_timer(&test_timer, jiffies + 2 * HZ);
	count++;
}

static int my_proc_open(struct inode *inode, struct file *file)
{
    /*4,在open函数中调用single_open绑定seq_show函数指针*/
    return single_open(file, my_proc_show, NULL);
}

/*2,填充proc_create函数中调用的flie_operations结构体
  其中my开头的函数为自己实现的函数，
  seq和single开头为内核实现好的函数，直接填充上就行
  open为必须填充函数
  */
static struct proc_ops my_fops = {
  .proc_open = my_proc_open,
  .proc_read = seq_read,
  .proc_lseek = seq_lseek,
  .proc_release = single_release,
};

static int __init my_init(void)
{


    struct proc_dir_entry *file;
    //创建父级目录，第二个参数NULL表示在/proc下
    //这里用我的学号当做文件名
    struct proc_dir_entry *parent = proc_mkdir("3190608027",NULL);

    /*1,
      首先要调用创建proc文件的函数，需要绑定flie_operations
      参数1：要创建的文件
      参数2：权限设置
      参数3：父级目录，如果传NULL，则在/proc下
      参数4：绑定flie_operations
      */
    file = proc_create("readpfcount", 0644, parent, &my_fops);
    if(!file)
        return -ENOMEM;

    //创建定时器
    pfcount_last = pfcount;
	test_timer.expires  = jiffies + 2 * HZ;
	timer_setup(&test_timer, irq_test_timer_function, 0);

	add_timer(&test_timer);

	printk(KERN_INFO "already init and add timer\n");
    return 0;
}

/*6,删除proc文件*/
static void __exit my_exit(void)
{
  printk(KERN_INFO "exit timer drv\n");
  del_timer(&test_timer);
  //移除目录及文件
  remove_proc_entry("readpfcount", NULL);
}

module_init(my_init);
module_exit(my_exit);
```
- 对应的 **Makefile** 文件：

```Makefile
ifneq ($(KERNELRELEASE),)
	obj-m:=readpfcount.o
else
	KDIR:= /usr/src/linux-5.11.1/
	PWD:= $(shell pwd)

default:
	$(MAKE) -C $(KDIR) M=$(PWD) modules
clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean
endif
```


### 编译安装模块
- 根据对应的 **Makefile** 文件，可以直接使用 `sudo make` 进行编译
- 编译成功后会在当前文件夹下生成 `.ko` 文件
- 使用 `insmod` 安装模块
- 可以通过 `dmesg` 查看内核打印的信息
- 可以通过 `cat /proc/readpfcount` 命令，通过 `/proc` 在用户态下查看该时间段内的缺页次数。
- 效果如下图所示：

![](https://img-blog.csdnimg.cn/f3ab538c527b44d8aa39039a8a4526b4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)
