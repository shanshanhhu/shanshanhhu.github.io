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

# lsof
[How to Use the Linux lsof Command](https://www.howtogeek.com/426031/how-to-use-the-linux-lsof-command/)
```shell script
# See Processes That Have Opened a File
lsof /var/log/kern.log
# See All Files Opened from a Directory
lsof +D /var/log
# List Files Opened By a Process, you can provide more than one search item.
# -c (command) option
lsof -c ssh -c init
# See Files Opened By a User
lsof -u shanshanhu
# Excluding FIles Opened by a User
lsof -u ^shanshanhu
# List FIles Opened by a Process
lsof -p 4610  # 4610 is the process ID
# Listing Process IDs That Have Opened a FIle
lsof -t /usr/share/mime/mime.cache
# Use AND and OR Searches
# OR
lsof -u mary -c ssh
# AND
lsof -u mary -c ssh -a
# Automatically Refreshing The Display
# +|-r(repeat) option
lsof -u mary -c ssh -a -r5  # It waits for the number of seconds provided on the command line and then refreshes the display with a new set of results.
# Displaying Files Associated with Internet Connections
lsof -i
# Displaying Files Associated with Internet Connections by Process ID
lsof -i -a -p 606
# Displaying Files Associated with Internet Connections and Ports
lsof -i :22
```
- lsof -u shanshanhu -a +D /usr | less
![lsof](/assets/img/sample/lsof_command.png)

# less
[how-to-use-the-less-command-on-linux](https://www.howtogeek.com/444233/how-to-use-the-less-command-on-linux/)
[less-command-in-linux](https://linuxize.com/post/less-command-in-linux/#:~:text=To%20quit%20less%20and%20go%20back%20to%20the,less%20to%20watch%20the%20file%20contents%20for%20changes.)
```shell script
# shows line numbers
less -N filename
# Searching. press "/" and then type your search phrase.
# To find the next matching item, press “n”. To search for the previous matching item, press “N”.
/pattern
# Searching backward
?pattern
# -p (pattern) option. It will then display the page with the matching search item in it.
less -pPattern filename

# By default, when less exits, the file contents will be cleared from the screen. To leave file contents on screen, use the -X option:
less -X filename
# same as 'tail -f'
less +F /var/log/messages
```
## Navigating in Less
- Move forward one line: Down Arrow, Enter, e, or j
- Move backward one line: Up Arrow, y, or k
- Move forward one page: Space bar or Page Down
- Move backward one page: Page Up or b
- Scroll to the right: Right Arrow
- Scroll to the left: Left Arrow
- Jump to the top of the file: Home or g
- Jump to the end of the file: End or G
- Jump to a specific line: Type the line number  and then hit “g”
- Jump to a percentage way through the file: Type the percentage and then hit “p” or “%.” (You can even enter decimal values, so to jump to the point 27.2 percent through the file, type “27.2” and then hit “p” or “%.”  Why would you want to use decimals? I honestly have no idea.)
- Search forward: Hit “/” and type your search, like “/Jekyll”, and press Enter
- Search backward: Hit “?” and type your search, like “/Hyde”, and press Enter
- Next matching search item: n
- previous matching search item: N
- Quit: q
### Viewing Multiple Files
> less file1.txt file2.txt
- To view the next file, press “:” and then hit “n”.
- To move to the previous file, type “:” and then hit “p.”
![less-multiple-files](/assets/img/sample/less-multiple-files.png)
### Using Marks
To drop a mark on the top-most displayed line, press “m” and then hit the letter you wish to use, such as “a”.
From any other location within the file, you can easily return to a mark by pressing the apostrophe (or single quote) “‘” and then pressing the letter of the mark you wish to return to.


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

# APT-GET Package Management Command
[25 Useful Basic Commands of APT-GET and APT-CACHE for Package Management](https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/)

# Add Software repository in Debian
add repository links to `/etc/apt/sources.list`. Debian [SourceList](https://wiki.debian.org/SourcesList#Commonly_used_package_sources)
> deb  http://deb.debian.org/debian  stretch main
> deb-src  http://deb.debian.org/debian  stretch main
> //Save and close the file. Then update the system packages list using the command below.
> apt update

# disk usage
df -hl 查看磁盘剩余空间
df -h 查看每个根路径的分区大小
du -sh \[目录名] 返回该目录的大小
du -sm \[文件夹] 返回该文件夹总M数

# change login account
`su` : The su (short for substitute or switch user) utility allows you to run commands with another user’s privileges, by default the root user.
Using su is the simplest way to switch to the administrative account in the current login session.
> su username

### ./ and ../
`./config` means you're calling something in the current working directory.
`../config` would be used if the config executable were in the parent of the current working directory.
