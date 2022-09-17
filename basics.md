# Basics

### High Level

~~~
User Processes
    GUI, Servers, Shell

Linux Kernel
    System Calls, Process Management, Memory Management, Device Drivers

Hardware
    CPU, RAM, Disks, Network Ports
~~~

* **Kernel**
  * The kernel is in charge of managing tasks in 4 ares:
    * Process
      * Determine which process to run on CPU
    * Memory
      * Keep track of all memory - what is currently allocated to a particular process, what might be shared between processes, and what is free
    * Device drivers
      * Acts as an interface between hardware and processes
    * Sys calls and supports
      * Processes normally use sys calls to communicate with kernel


* **User Space**
  * The main memory that the kernel allocates for user process is called user space.

---

### Linux Directory Structure

![](img/linux_0.png)

* `/`: root dir


* `/bin`: essential ready-to-run programs(eg. /bin/bash)
* `/usr/bin`: user apps (/usr is read-only)
* `/sbin`: sys administration binaries
* `/lib`: essential shared libs needed by essential binaries in /bin and /sbin
* `/usr/lib`: libs needed by /usr/bin
* `/boot`: static boot files used by the bootloader(eg. kernel program)
* `/opt`: optional add-on packages (eg. homebrew)


* `/etc`: system-wide config files


* `/var`: writable counterpart to /usr(eg. log files would normally be written to /var/log)


* `/proc`: kernel & running process files(only exist in memory)
* `/run`: runtime state files
* `/srv`: data for services provided by the system


* `/dev`: device files (eg. disk, keyboard)
* `/mnt`: temporary mount points
* `/media`: contains subdirectories where removable media devices insert into the computer are mounted.


* `/lost+found`: recovered files
* `/tmp`: temporary files (delete whenever your system is restarted)


* `/home`: home folder for each user(eg. /home/alex)
* `/root`: home directory for the root user(not located at /home/root)


---

### Linux Command General

* **Shell input / output redirection**
  * 3 redirects:
    * stdin: file descriptor is 0
    * stdout: file descriptor is 1; default is terminal
    * stderr: file descriptor is 2


  * `command > file_1`
    * To override the stdout of command to file_1; if file_1 exists, erasing the original file first.
  * `command >> file_1`
    * To append the stdout of command to file_1.
  * `command < file_1`
    * To feed command with file_1's content.
  * `command_1 | command_2`
    * To send stdout of command_1 to the stdin of command_2.
  * `command_1 | tee (-a) file_1`
    * To both send stdout of command_1 to stdout(terminal) and override/(-a: append) file_1.
  * `command > file_1 2> file_2`
    * To send stdout of command to file_1, send stderr of command to file_2.
    * 2 here is the stream id of stderr. The stream id of stdout is 1. 
  * `command > file_1 2>&1`
    * To send stdout and stderr of command to file_1.
  * `command &`
    * Run command in background mode.




---

### Devices
* device files
  * devices are usually accessible as device files (under /dev). (Not all device has device files)


* sysfs
  * To provide a uniform view for attached devices based on their actual hardware attributes, the Linux kernel offers sysfs interface. 
  * in /sys/devices


* `dd`
  * is a useful command to read from an input file or stream, and write to a file or stream.
  * eg. `dd if=/dev/zero of=new_file bs=1024 count=1 skip=2`
    * if: input file
    * of: output file
    * bs: block size to read & write each time
      * ibs, obs
    * count: # of blocks to read
    * skip: skip first n blocks


---

### Disk & File system

* relationship
  * disk (physical device) -> 1+ partitions (continuous blocks on a disk) -> 1+ volume <-> 1 filesystem


* swap space
  * Not every partition on a disk contains a filesystem. It's also possible to augment the RAM on a machine with disk space. 
  * The disk space used to store memory pages is called swap space. 
  * use `free` to see current swap usage. 


* soft link & hard link
  * inode: pointer or id of a file on the hard disk
  * soft link: link --> file_node
    * link will be removed if file is removed or renamed
    * `ln -s`
  * hard link: link --> inode
    * deleting, renaming or moving the original file will not affect the hard link
    * `ln`


* file system commands
  * `ls -l`
  ~~~
  Type         #of links  owner group   size  mon  day  year  name
  drwxr-xr-x.  2          root  root    6     Aug  9    2021  /var/log
  -rwxrwxrwx.  1          root  root    11    Mar  2    2022  file_1.txt
  lrwxrwxrwx.  1          root  root    10    Aug  9    2021  mail -> spool/mail
  ~~~
  file types:
  ~~~
  symbol    type
  -         regular file
  d         directory
  l         link
  c         character device file
  s         socket
  p         named pipe
  b         block device
  
  * b: block (eg. disk)
      * Kernel accesses data in fixed chunks.
      * total size if fixed.
  * c: character (eg. printer)
    * Kernel reads/writes data streams.
    * don't have a size.
  * p: pipe
    * like a character device, but at the other end is I/O stream instead of a driver.
  * s: socket
    * usually used for inter-process communication.
    * often found outside /dev.
  ~~~


  * display file
    * `cat file_1`
      * entire content
    * `more file_1`
      * one page at a time, can read next line or next page
    * `less file_1`
      * like more, one page at a time, can read backwards
    * `head -2 file_1`
      * the first 2 lines
    * `tail -3 file_1`
      * the last 3 lines


  * copy file/dir
    * file: `cp <src_file> <dest_file>`
    * dir: `cp -R <src_dir> <dest_dir>` (-R means recursively)


  * find file/dir
    * find: `find / -name "ifcfg-eth1"`
    * locate: `locate "ifcfg-eth1"`
    * diff
      * locate uses a prebuilt database, which should be regularly updated;(faster & inaccurate) while find iterates over a filesystem. (slower & accurate)
      * to update locate database, run updatedb


  * permission & ownership of file/dir
    * permission:
      * 3 types of permissions:
        * r
        * w
        * x: execute
          * for dir, can go (cd) into the dir
      * 3 levels for each type:
        * u: user
        * g: group
        * o: other
      * `chmod`: change permission
        * `chmod go-w file_1`: remove g,o's w permission
        * `chmod a-r file_2`: remove u,g,o r permission
      * using ACL
        * add permission:
          * `setfacl -m u:<user_name>:rwx file_1` / `setfacl -m g:<group_name>:rwx file_2` / `setfacl -rm g:<group_name>:rwx dir_1`
        * remove permission:
          * `setfacl -x u:<user_name>:wx file_1`
        * check ACL:
          * `getacl file_1`
    * ownership:
      * 3 owners of a file/dir:
        * u
        * g
      * change ownership: `chown <user_name> file_1`
      * change group ownership: `chgrp <group_name> file_1`

  
  * text processing
    * `cut`
      * for each line in the file, get certain chars
      * eg. 
        * `cut -c1-3,6-8 file_1`: for each line in file_1, get 1-3 chars and 6-8 chars
        * `cut -d: -f 6-7 file_1`: for each line in file_1, split by :, get fields 6-7
    * `awk`
      * for each line in the file or output, get certain columns (default separated by space)
      * eg.
        * `awk '{print $1, $3}' file_1`: print the file_1's 1st and 3rd field
        * `ls -l | awk '{print $NF}'`: print the last field of command output 
        * `awk -F: '/Alex/ {print $1}' file_1`: specify delimiter as :, only print the 1st field of the line contains Alex
        * `awk 'length($0) > 15' file_1`: print the lines whose length > 15
        * `awk '{if($6 == "alex") print $0;}'`: print the lines whose 6th field is alex
    * `grep/egrep`
      * search line by line
      * eg.
        * `grep -n <keyword> file_1`: search for lines with keyword in them, with line # printed
        * `grep -i <keyword> file_1`: case insensitive
        * `grep -c <keyword> file_1`: count # of lines matched
        * `grep -v <keyword> file_1`: print lines without keyword
      * egrep: seach 1+ keyword
        * `egrep -i "<keyword_1>|<keyword_2>" file_1`
    * `sort`
      * sort all lines in a file/output
      * `sort -k2 file_1`: sort by the 2nd field
      * `sort -r file_1`: sort in reverse order
    * `uniq`
      * remove duplicate lines
      * always sort first, then do uniq `sort file_1 | uniq -c`(also print repeating times)
    * `wc`
      * print #of lines(-l), #of words(-w), #of byte(-c)

    
  * compare files
    * `diff`: compare line by line
    * `cmp`: compare byte by byte

  
  * compress/uncompress file/dir
    * `tar`
      * take many files into one tar
    * `gzip`
    * `gzip -d` / `gunzip`


  * combine/split files
    * `cat file_1 file_2 > file_3`: combine
    * `split file_3`