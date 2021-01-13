---
title: Linux Basics
date: 2021-01-12 14:40:22 +0800
categories: [linux, linux]
tags: [linux]
---

# The Linux Filesystem
![The Linux Filesystem Structure](../assets/img/sample/The-Linux-filesystem.png)
1. `/etc` Generally contains the Linux configuration files—files that control when and how programs start up
2. /mnt Where other filesystems are attached or mounted to the filesystem

# Linux Commands
## Basic Commands in Linux
1. `pwd` : present working directory
2. `whoami` : Checking Your Login
3. `cd` :
-	You would use `..` to move up one level.
-	You would use `.. ..` to move up two levels.
- You would use `.. .. ..` to move up three levels, and so on.
4. `ls`
- `-l` : it provides us with significantly more information,
such as whether an object is a file or directory, the number of links, the
owner, the group, its size, when it was created or modified, and its name.
- `-a` : To show hidden files
5. `--help` \ `-h` \ `-?` : to get help file. The convention in Linux is to use a double
dash (--) before word options, such as help, and a single dash (-) before
single-letter options, such as –h.
6. `man` : view referencing manual pages

## Finding Stuff
1. `locate` : search keywords
![locate aircrack-ng](../assets/img/sample/locate.png)
Sometimes the results of locate can be overwhelming, giving you too much information. Also, locate
uses a database that is usually only updated once a day, so if you just created
a file a few minutes or a few hours ago, it might not appear in this list until
the next day.
2. `whereis` : finding binaries
This command returns not only the location of the binary but also its source and man page if they are available.
3. `which` :  it only returns the location of the binaries in the PATH variable in Linux.
4. `find` : begin your search in any designated directory and looking for
a number of different parameters, including, of course, the filename but also the date of creation or modification, the owner, the group,
permissions, and the size.
> find directory options expression
> eg:
> find \ -type f -name apache
First I state the directory in which to start the search, in this case /
Then I specify which type of file to search for, in this case f for an ordinary file.
Last, I give the name of the file I’m searching for, in this case apache2.
![find command](../assets/img/sample/find.png)
**Wildcards**
Let’s say we’re doing a search on a directory that has the files cat, hat, what,
and bat. The `?` wildcard is used to represent a single character, so a search
for ?at would find hat, cat, and bat but not what, because at in this filename is
preceded by two letters. The \[] wildcard is used to match the characters that
appear inside the square brackets. For example, a search for \[c,b]at would
match cat and bat but not hat or what. Among the most widely used wildcards
is the asterisk (*), which matches any character(s) of any length, from none to
an unlimited number of characters. A search for *at, for example, would find
cat, hat, what, and bat.
5. `grep` : filter
> ps aux | grep apache2

## Modifying Files and Directories
1. Creating Files
- `cat`
> cat > filename   //Press ctrl-D to exit
To add, or append, more content to a file, use the cat command with a double redirect (>>)
> cat >> filename
If you want to overwrite the file with new information, you can simply use the
cat command with a single redirect **again**
- `touch`
> touch newfile
2. `mkdir` : Creating a Directory
> mkdir newdirectory
3. `cp` : Copying a File
> cp oldfile /root/newdirectory/newfile
4. `mv` :Renaming a File
> mv newfile newfile2
5. `rm` : Removing a File
> rm newfile2
6. `rmdir` : Removing a Directory(!!!rmdir will not remove a directory that is not
empty)
> rmdir newdirectory
> rm -r newdirectory  // removing a directory with contents in it

# Text Manipulation
## Viewing Files
1. `head` : Taking the file's **Head**
> head /etc/snort/snort.conf  //By default, this command displays the first 10 lines of a file.
> head -20 /etc/snort/snort.conf  // see the first 20 lines of the file
2. `tail` : Grabbing That **Tail**
> tail /etc/snort/snort.conf  // it’s used to view the last lines of a file.
> tail -20 /etc/snort/snort.conf
3. `nl` : Numbering the Lines
> nl /etc/snort/snort.conf   // display a file with line numbers
4. `grep` : Filtering Text
> cat /etc/snort/snort.conf | grep output
**Exercise:**
display the five lines immediately before a line that says # Step #6: Configure output plugins
> nl /etc/snort.conf | grep output  // firstly, find out the line number of the target line
> tail -n+507 /etc/snort/snort.conf | head -n 6   // -n+507 return file which first line starts at 507
5.


1. `sed` :  Find and Replace specific text
> sed s/mysql/MySQL/g /etc/snort/snort.conf > snort2.conf
The s command performs the search: you first give the term you
are searching for (mysql ) and then the term you want to replace it with
(MySQL), separated by a slash (/). The g command tells Linux that you
want the replacement performed globally. Then the result is saved to a
new file named snort2.conf.
> sed s/mysql/MySQL/ snort.conf > snort2.conf  //  replace only the first occurrence of the term mysql
> sed s/mysql/MySQL/2 snort.conf > snort2.conf  //  2 is the number of the occurrence


