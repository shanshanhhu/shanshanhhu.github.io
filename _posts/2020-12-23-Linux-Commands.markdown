---
title: Linux Commands
date: 2021-01-05 10:08:22 +0800
categories: [linux, linux]
tags: [linux]
---
# Summary
[Linux Commands Summary from A to Z](https://www.geeksforgeeks.org/linux-commands/#H)

# [Linux run command in background][1]
1. **&**
> command &
注意：需要用户交互的命令不要放在后台执行，因为这样你的机器就会一直等待用户操作。
不过，作业在后台运行一样会将结果输出到屏幕上，干扰你的工作。如果放在后台运行的作业会产生大量的输出，
最好使用下面的方法把它的输出重定向到某个文件中
> command  >  out.file  2>&1  &
- command > out.file是将command的输出重定向到out.file文件，即输出内容不打印到屏幕上，而是输出到out.file文件中。
- 2>&1 是将标准出错重定向到标准输出，这里的标准输出已经重定向到了out.file文件，即将标准出错也输出到out.file文件中。最后一个&， 是让该命令在后台执行。
- 2与>结合代表错误重定向，而1则代表错误重定向到一个文件1，而不代表标准输出；换成2>&1，&与1结合就代表标准输出了，就变成错误重定向到标准输出.
2. **nohup**
使用&命令后，作业被提交到后台运行，当前控制台没有被占用，但是一但把当前控制台关掉(退出帐户时)，
作业就会停止运行。nohup命令可以在你退出帐户之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。
> nohup command &
如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：
> nohup command > myout.file 2>&1 &
3. **daemon**
> ./bin/kafka-server-start.sh  -daemon ./config/server.properties

[1]: https://blog.csdn.net/liuyanfeier/article/details/62422742

# grep多条件查询
1. AND 多条件与查询
> grep 'pattern1' filename | grep 'pattern2'
2. OR 多条件或查询
- 使用转义字符将管道符转为'或'符号。
> grep 'pattern1\|pattern2' filename
- 使用正则表达式选项。
> grep -E 'pattern1|pattern2' filename
- egrep相当于grep -E
> egrep 'pattern1|pattern2' filename
3. NOT 非查询
不满足当前条件的所有内容行
> grep -v 'pattern' filename

# 用一个命令杀死多个进程
`for pid in $(ps -ef | grep "some search" | awk '{print $2}'); do kill -9 $pid; done`
下面的命令更高效：
`for pid in $(ps -ef | awk '/some search/ {print $2}'); do kill -9 $pid; done`

# 修改hosts
> nano /etc/hosts

# 查看端口使用状态
> lsof -i -P -n
-t : Show only TCP sockets on Linux
-u : Display only UDP sockets on Linux
-l : Show listening sockets. For example, TCP port 22 is opened by SSHD server.
-p : List process name that opened sockets
-n : Don’t resolve service names i.e. don’t use DNS

# netcat
通过建立TCP/UDP连接传送数据---[Netcat (nc) Command with Examples](https://linuxize.com/post/netcat-nc-command-with-examples/)
> nc \[options] host port
1. The `-z` option will tell nc to only scan for open ports, without sending any data to them
2. The `-v` option to provide more verbose information.
3. Use the `-u` option to establish a UDP connection
## Port Scanning
> nc -z -v 10.10.8.8 20-80  // 20-80 is port range
## Sending Files through Netcat
> nc receiving.host.com 5555 < file_name
## Creating a Simple Chat Server
> nc -l 5555

# telnet
## how to exit telnet connection?
> Escape character is '^]'.
ctrl + ] --> (then enter telnet console) --> quit

# check os version in linux
Type any one of the following command to find os name and version in Linux:
> cat /etc/os-release
> lsb_release -a
> hostnamectl

Type the following command to find Linux kernel version:
> uname -r

# Yum Command
[Yum Command Cheat Sheet for Red Hat Enterprise Linux](https://access.redhat.com/articles/yum-cheat-sheet)
1. **Problem** :Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
**[Fix:](https://www.linuxnix.com/resolving-yum-repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast/)**
> yum clean all

# cp command
> cp Src_file Dest_file
> cp -r Src_directory Dest_directory   // copy recursively
> cp Src_file1 Src_file2 Src_file3 Dest_directory   // the destination directory with the same name, created if not existed but if already existed then it will be overwritten
**Options:**
1. `-b`(backup): With this option cp command creates the backup of the destination file in the same folder with the different name and in different format.
2. `-f`(force): If the system is unable to open destination file for writing
operation because the user doesn’t have writing permission for this file
then by using -f option with cp command, destination file is deleted first and then copying of content is done from source to destination file.
3. `-r/-R`: With this option cp command shows its recursive behavior by copying the entire directory structure recursively.
