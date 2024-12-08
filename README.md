[合集 \- Linux\_note(1\)](https://github.com)1\.Linux查看进程所在目录12\-07收起
## Linux查看进程所在目录



> 通过ps 或 top 查看进程信息时，只能查到进程的相对路径，查不到进程的详细信息，如绝对路径等，我们可以通过下面的方法进行查询


### 1\. 通过`ll /proc/PID` 命令查看进程所在的目录位置


linux在启动一个进程时，系统会在/proc下创建一个以PID命名的文件夹，在该文件夹下会有我们的进程信息，其中包括一个名为exe的文件即记录了进程的绝对路径，通过ls \-la 即可查看


#### 查看进程pid



```
[root@hw_centos7 ~]# ps -ef |grep nginx |grep -v grep
root      9900     1  0 Dec05 ?        00:00:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
www       9901  9900  0 Dec05 ?        00:00:00 nginx: worker process
www       9902  9900  0 Dec05 ?        00:00:00 nginx: worker process

```

如上 获取到主进程PID 9900


#### 根据 PID 查看进程目录



```
[root@hw_centos7 ~]# ll -a /proc/9900
total 0
dr-xr-xr-x   9 root root 0 Dec  5 23:27 .
dr-xr-xr-x 120 root root 0 Nov  3 00:58 ..
dr-xr-xr-x   2 root root 0 Dec  6 18:00 attr
-rw-r--r--   1 root root 0 Dec  6 18:00 autogroup
-r--------   1 root root 0 Dec  6 18:00 auxv
-r--r--r--   1 root root 0 Dec  5 23:27 cgroup
--w-------   1 root root 0 Dec  6 18:00 clear_refs
-r--r--r--   1 root root 0 Dec  6 17:59 cmdline
-rw-r--r--   1 root root 0 Dec  6 18:00 comm
-rw-r--r--   1 root root 0 Dec  6 18:00 coredump_filter
-r--r--r--   1 root root 0 Dec  6 18:00 cpuset
lrwxrwxrwx   1 root root 0 Dec  6 18:00 cwd -> /
-r--------   1 root root 0 Dec  6 18:00 environ
lrwxrwxrwx   1 root root 0 Dec  6 18:00 exe -> /usr/sbin/nginx


```

如上 exe 后面显示了该进程所在的目录`/usr/sbin/nginx`



> cwd 符号链接的是进程运行目录
> exe 符号链接后面是执行程序的绝对路径
> cmdline 就是程序运行时输入的命令行命令
> environ 记录了进程运行时的环境变量
> fd目录下是进程打开或使用的文件的符号连接；


### 2\. 通过lsof 命令查看进程的当前目录



> **lsof是 “list of open files “的缩写，是一个命令行工具，用于显示系统中进程当前打开的文件信息。lsof可以显示进程的当前工作目录**



```
[root@hw_centos7 ~]# ps -ef|grep nginx|grep -v grep
root      9900     1  0 Dec05 ?        00:00:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
www       9901  9900  0 Dec05 ?        00:00:00 nginx: worker process
www       9902  9900  0 Dec05 ?        00:00:00 nginx: worker process


```

**根据进程号9900进行查找该进程所在的目录**



```
[root@hw_centos7 ~]# lsof -a -d cwd -p 9900
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   9900 root  cwd    DIR  253,1     4096    2 /

```

在你的命令行输入中：



```
[root@hw_centos7 ~]# lsof -a -d cwd -p 9900

```

你使用了 `lsof`（List Open Files）命令来查看与进程相关的文件信息。具体来说，你是在查看进程 ID 为 `9900` 的进程当前工作目录（cwd）。下面是对这个命令的参数和输出的解释：


### 解释命令参数


* **`lsof`**：这是一个用于列出当前系统打开文件的命令行工具。每一个进程打开的文件都可以通过 `lsof` 来查看，包括常规文件、目录、网络文件、设备等。
* **`-a`**：逻辑与。“and”选项，表示需要同时满足多个条件。这里要求满足 `-d` 和 `-p` 的同时条件。
* **`-d cwd`**：指定文件描述符类型为 `cwd`（Current Working Directory），表示显示当前工作目录。
* **`-p 9900`**：指定进程 ID 为 `9900`，表示只列出与此特定进程相关的打开文件。


### 输出解释



```
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   9900 root  cwd    DIR  253,1     4096    2 /

```

* **COMMAND**：`nginx` 是正在运行的进程的名称。
* **PID**：`9900` 是进程 ID。
* **USER**：`root` 是运行该进程的用户。
* **FD**：`cwd` 表示当前工作目录。
* **TYPE**：`DIR` 表示文件类型是一个目录。
* **DEVICE**：`253,1` 是设备号，表示文件所在的设备或分区。
* **SIZE/OFF**：`4096` 是目录的大小或偏移量。对于目录，通常表示目录的块大小。
* **NODE**：`2` 是文件系统节点号（inode）。
* **NAME**：`/` 是当前工作目录的路径。在这种情况下，表示进程 `9900` 的当前工作目录是根目录。


### 3\. 通过 pwdx 命令查看进程的当前工作目录


**执行pwdx PID命令，就得到进程的当前工作目录**



```
[root@hw_centos7 /]# pwdx 9900
9900: /

```

nginx的运行程序在/目录下


 本博客参考[slower加速器](https://jisuanqi.org)。转载请注明出处！
