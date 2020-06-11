每个用户默认使用的shell被记录在/etc/passwd中的最后一个字段。

bash会自动加载home目录下的.bashrc文件。一般个人的配置保存在这里。而公共的则放在/etc/bashrc。

PS1变量用来控制默认命令行提示符的格式（这里有一个很好的PS1生成页面：http://bashrcgenerator.com/ ），PS2用来控制后续命令提示符格式。

硬链接是在源文件的inode上ref++，软链接新建inode，可以跨文件系统。

ps aux中，STAT列的双字符含义：

* 第一列是ps -l的S列，0代表运行，S代表休眠，R代表可运行正在等待运行，Z代表僵尸进程（进程已经结束但是父进程不存在），T代表停止。

* 第二列：<表示进程运行于高优先级，N表示运行于低优先级，L表示有页面锁定在内存中，s表示控制进程，l表示多线程，+表示进程运行于前端。

printenv可以打印全部环境变量。

set显示某个特定进程的所有环境变量。unset可以删除某个环境变量。但是从父进程继承而来的环境变量回到父进程之后依旧存在。

bash自带的环境变量列表：http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html，3.2.4.2节。

bash login之后按照下面顺序处理初始的环境：/etc/profile，$HOME/.bash_profile，$HOME/.bash_login，$HOME/.profile。（PS：如果你的bash不是在登陆时启动的，而是通过bash命令启动的，/etc/profile文件不会被处理。）

chsh用来修改用户默认的shell。

lvm（logic volume manager）的层次结构：logic volume -> volume group -> physical volume

physical volume是物理磁盘的分区，volume group把多个物理分区组成一个“假”的磁盘，logic volume是在这个“假”磁盘上的分区。

lvm可以创建快照/镜像（对于逻辑卷而言），并且可以提供磁盘的并行读写。

lvm的创建，在fdisk创建分区的时候用8e，pvcreate创建一个physical volume，vgcreate创建一个volume group，lvcreate创建一个logic volume。（其他相关命令：vgchange，vgremove，vgextend，vgreduce，lvextend，lvreduce）

