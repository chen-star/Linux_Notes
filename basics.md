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
      * 
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
* `/boot`: static boot files(eg. kernel program)
* `/opt`: optional packages (eg. homebrew)


* `/etc`: system-wide config files


* `/var`: writable counterpart to /usr(eg. log files would normally be written to /var/log)


* `/proc`: kernel & running process files
* `/run`: runtime state files
* `/srv`: data for services provided by the system


* `/dev`: device files
* `/mnt`: temporary mount points
* `/media`: contains subdirectories where removable media devices insert into the computer are mounted.


* `/lost+found`: recovered files
* `/tmp`: temporary files (delete whenever your system is restarted)


* `/home`: home folder for each user(eg. /home/alex)
* `/root`: home directory for the root user(not located at /home/root)


---

### Shell input / output redirection
* `command > file_1`
  * To send the stdout of command to file_1; if file_1 exists, erasing the original file first.
* `command >> file_1`
  * To append the stdout of command to file_1.
* `command_1 | command_2`
  * To send stdout of command_1 to the stdin of command_2.
* `command > file_1 2> file_2`
  * To send stdout of command to file_1, send stderr of command to file_2.
  * 2 here is the stream id of stderr. The stream id of stdout is 1. 
* `command > file_1 2>&1`
  * To send stdout and stderr of command to file_1.
* `command &`
  * Run command in background mode.