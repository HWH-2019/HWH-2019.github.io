---
title: 【Linux系列】添加系统调用
author: HWH
tags:
  - Linux
  - 操作系统
categories:
  - - Tutorial
abbrlink: 6a08f7b
date: 2022-12-03 20:27:42
---


### 编译环境
- 虚拟机操作系统： `ubuntu-20.04.3-desktop-amd64`
- 原系统内核版本：`5.11.0-46-generic`
- 内核源码版本：`5.11.1`

### 修改任务

#### 基础任务
- 采用内核编译法增加两个系统调用
- 一个系统调用把用户输入的字符串保存到 **text.txt** 中
- 一个系统调用把 **text.txt** 中的字符串读出
- 写个主程序调用两个系统调用

#### 进阶任务
- 用户可以指定用户输入字符串保存的位置和文件名
- 程序可以将系统调用读取出的字符串打印到用户终端

### 修改准备
-  [下载内核源码](https://cdn.kernel.org/pub/linux/kernel/)
-  [掌握内核编译](https://blog.csdn.net/h2763246823/article/details/122522326)

### 修改源码
- 所需修改的文件(路径默认从源码目录开始)如下：

```powershell
arch/x86/entry/syscalls/syscall_64.tbl      //设置系统调用号，如果是32位就用syscall_32.tbl

include/linux/syscalls.h                     //系统调用的头文件

kernel/sys.c                                 //定义系统调用函数
```


#### 添加系统调用号
- 使用 **gedit** 打开 **syscall_64.tbl** 文件

```powershell
sudo gedit arch/x86/entry/syscalls/syscall_64.tbl
```
- 根据规则在后面添加自己的系统调用号和系统调用名，如下图所示

![](https://img-blog.csdnimg.cn/72bc1b5f98ea4fd48bbc83bdb75c0337.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 这里我添加了两个系统调用分别是读和写

#### 修改系统调用头文件
- 使用 **gedit** 打开 **syscalls.h** 文件

```powershell
sudo gedit include/linux/syscalls.h
```

- 添加对应系统调用的函数声明，如下图

```c
asmlinkage long sys_fwrite(int strcount,char __user* string);
asmlinkage long sys_fread(void);
```

![](https://img-blog.csdnimg.cn/22717168a74447a0a676db3750107a98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 修改系统调用函数定义
- 使用 **gedit** 打开 **sys.c** 文件

```powershell
sudo gedit kernel/sys.c
```

- 添加对应系统调用的函数定义，内容如下：

```c
//写文件系统调用
SYSCALL_DEFINE2(fwrite,int,strcount,char __user *,string)
{
        printk("fwrite executing");
        struct file *filp;
        char* filename="/usr/src/text.txt\0";


        printk("open file");

        filp = filp_open(filename, O_RDWR|O_TRUNC, 0755);
        if(IS_ERR(filp))
        {
                printk("open error...\n");
                return -1;
        }

        printk("writing\n");
        char* buf;
        buf = (char *)kmalloc(sizeof(char) * strcount, GFP_KERNEL);
        if(!copy_from_user(buf, string, strcount))
        {
                printk("input is %s \n", buf);
        }

        kernel_write(filp, buf, strlen(buf),&filp->f_pos);

        filp_close(filp,NULL);
        printk("fwrite end\n");
        return 0;

}
```

```c
// 读文件系统调用
SYSCALL_DEFINE0(fread)
{
        printk("fread executing");
        struct file *filp;
        struct inode *inode;
        const char* filename="/usr/src/text.txt\0";
        off_t fsize;
        char *buf;
        unsigned long magic;


        printk("start....\n");
        filp=filp_open(filename,O_RDONLY,0);
        inode=file_inode(filp);

        magic=inode->i_sb->s_magic;

        printk("file system magic:%li \n",magic);
        printk("super blocksize:%li \n",inode->i_sb->s_blocksize);
        printk("inode %li \n",inode->i_ino);

        fsize=inode->i_size;
        printk("file size:%i \n",(int)fsize);
        buf=(char *) kmalloc(fsize+1,GFP_ATOMIC);
        printk("buf correct\n");


        kernel_read(filp,buf,fsize,&(filp->f_pos));
        buf[fsize]='\0';
        printk("The File Content is:\n");
        printk("%s\n",buf);


        filp_close(filp,NULL);
        printk("fread end\n");
        return 0;
}
```

- 将两个函数定义写在 sys.c 文件的尾部

![](https://img-blog.csdnimg.cn/0423b3747a8b4b88964c631c5a901277.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 其中 **SYSCALL_DEFINE** 为系统定义，之后的数字代表系统调用参数的个数，其中第一个参数为系统调用函数的名称，需要与调用号定义(调用号定义第三个参)，声明(sys_xxx中xxx部分)一致。
- 注意系统调用定义时的参数书写格式。

### 编译安装内核

```powershell
#编译内核
sudo make -j4

#安装内核模块
sudo make modules_install

#安装内核
sudo make install

#重启
reboot
```

### 编写主程序
- 在指定目录下新建文件 **text.txt**

```c
sudo touch /usr/src/text.txt
```

- 新建 **readAndWrite.c** 文件，内容如下

```c
#include<unistd.h>
#include<stdio.h>
#include<string.h>
#include<sys/syscall.h>
#include<linux/kernel.h>
int main()
{
	long int a;

	printf("this is testwr execution\n");


	char string[120];
	scanf("%s",string);
	int length=strlen(string)+1;


	a = syscall(442,length,string);

	printf("a=%li\n",a);

	printf("testwr end\n");

	printf("this is testwr execution\n");

 	a = syscall(443);

	printf("a=%li\n",a);

	printf("testwr end\n");
	return 0;

}
```
- 编译

```c
gcc readAndWrite.c -o readAndWrite.o
```

- 执行

```c
sudo ./readAndWrite.o
```
- 执行结果，如下图
![](https://img-blog.csdnimg.cn/2d41e6988023425c867d67bd83cc9ab9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)![](https://img-blog.csdnimg.cn/13035f98526d4d9996e37ee96c83becf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 至此基础任务完成，接下来完成进阶任务

### 进阶任务
- 重新添加新的系统调用
- 添加系统调用号
![](https://img-blog.csdnimg.cn/4456233062114297ad8ee0e38ec9c4b8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

- 添加系统调用函数声明
![](https://img-blog.csdnimg.cn/1a4e3b42ae964cc89ad19590a8f4caaf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

```c
asmlinkage long sys_fsize(int pathcount,char __user * path);
asmlinkage long sys_fwrite_advance(int strcount,char __user * string,int pathcount,char __user * path);
asmlinkage long sys_fread_advance(char __user * res,int pathcount,char __user * path);
```


- 添加系统调用函数定义

```c
//获取文件大小系统调用
SYSCALL_DEFINE2(fsize,int,pathcount,char __user *,path)
{
        printk("fsize executing");
        struct file *filp;
        struct inode *inode;

        off_t fsize;
        unsigned long magic;

        char* pathbuf;
        pathbuf = (char *)kmalloc(sizeof(char) * pathcount, GFP_KERNEL);
        if(!copy_from_user(pathbuf, path, pathcount))
        {
                printk("input path is %s \n", pathbuf);
        }

        printk("start....\n");
        filp=filp_open(pathbuf,O_RDONLY,0);
        inode=file_inode(filp);

        magic=inode->i_sb->s_magic;

        printk("file system magic:%li \n",magic);
        printk("super blocksize:%li \n",inode->i_sb->s_blocksize);
        printk("inode %li \n",inode->i_ino);

        fsize=inode->i_size;
        printk("file size:%i \n",(int)fsize);

        filp_close(filp,NULL);
        printk("fsize end\n");
        return fsize+1;
}
```

```c
//写文件系统调用
SYSCALL_DEFINE4(fwrite_advance,int,strcount,char __user *,string,int,pathcount,char __user *,path)
{
        printk("fwrite_advance executing");
        struct file *filp;


        char* pathbuf;
        pathbuf = (char *)kmalloc(sizeof(char) * pathcount, GFP_KERNEL);
        if(!copy_from_user(pathbuf, path, pathcount))
        {
                printk("input path is %s \n", pathbuf);
        }

        printk("%s",pathbuf);
        printk("open file");

        filp = filp_open(pathbuf, O_RDWR|O_TRUNC, 0755);
        if(IS_ERR(filp))
        {
                printk("open error...\n");
                return -1;
        }

        printk("writing\n");
        char* buf;
        buf = (char *)kmalloc(sizeof(char) * strcount, GFP_KERNEL);
        if(!copy_from_user(buf, string, strcount))
        {
                printk("input is %s \n", buf);
        }

        kernel_write(filp, buf, strlen(buf),&filp->f_pos);
        filp_close(filp,NULL);
        printk("fwrite_advance end\n");
        return 0;

}
```

```c
//读文件系统调用
SYSCALL_DEFINE3(fread_advance,char __user *,res,int,pathcount,char __user *,path)
{
        printk("fread_advance executing");
        struct file *filp;
        struct inode *inode;

        off_t fsize;
        char *buf;
        unsigned long magic;

        char* pathbuf;
        pathbuf = (char *)kmalloc(sizeof(char) * pathcount, GFP_KERNEL);
        if(!copy_from_user(pathbuf, path, pathcount))
        {
                printk("input path is %s \n", pathbuf);
        }

        printk("start....\n");
        filp=filp_open(pathbuf,O_RDONLY,0);
        inode=file_inode(filp);

        magic=inode->i_sb->s_magic;

        printk("file system magic:%li \n",magic);
        printk("super blocksize:%li \n",inode->i_sb->s_blocksize);
        printk("inode %li \n",inode->i_ino);

        fsize=inode->i_size;
        printk("file size:%i \n",(int)fsize);
        buf=(char *) kmalloc(fsize+1,GFP_ATOMIC);
        printk("buf correct\n");




        kernel_read(filp,buf,fsize,&(filp->f_pos));
        buf[fsize]='\0';
        printk("The File Content is:\n");
        printk("%s\n",buf);

	if(!copy_to_user(res, buf, fsize+1))
        {
                printk("output success");
        }
        filp_close(filp,NULL);
        printk("fread_advance end\n");
        return 0;
}
```

- 编译安装内核
- 修改主程序

```c
#include<unistd.h>
#include <stdlib.h>
#include<stdio.h>
#include<string.h>
#include<sys/syscall.h>
#include<linux/kernel.h>
int main()
{
	long int a;

	printf("this is testwr execution\n");

	char *res;
	char string[120];
	char path[120];
	scanf("%s",string);
	scanf("%s",path);
	int length=strlen(string)+1;
	int plength=strlen(path)+1;


	a = syscall(444,length,string,plength,path);

	printf("a=%li\n",a);

	printf("testwr end\n");

	printf("this is testwr execution\n");
	a = syscall(446,plength,path);
	printf("a=%li\n",a);

	res = malloc(sizeof(char)*a);
 	a = syscall(445,res,plength,path);

 	printf("%s\n",res);

	printf("a=%li\n",a);

	printf("testwr end\n");
	return 0;

}
```
- 在所需目录下新建所需文件
- 编译运行，结果如下

![](https://img-blog.csdnimg.cn/87e8890d75744ed6a589a59e9253e2a8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

![](https://img-blog.csdnimg.cn/73d3143054214904848a7eb69f87852f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6Z6g5p2J,size_20,color_FFFFFF,t_70,g_se,x_16)

### 总结
- 增加系统调用主要是修改三个文件，分别添加系统调用号、系统调用函数声明和系统调用函数定义
- 增加文件读写系统调用的核心函数是：filp_open、filp_close、kernel_read、kernel_write、copy_from_user、copy_to_user
- 关于文件读写和系统调用传参最大且最容易出现的问题往往是**字符指针**和**字符数组**的问题
