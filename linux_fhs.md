FHS refer: http://www.pathname.com/fhs/

存在的目的希望让用户可以比较容易的理解已经安装的软件包在哪些目录下，也希望各个发行版和软件厂商能够遵循这个标准。

FHS根据两个维度，把linux下的目录分为四个类型：

<table>
    <tr>
        <th></th>
        <th>可分享的（可以mount到其他OS，比如bin和邮件等）</th>
        <th> 不可分享的</th>
    </tr>
    <tr>
        <td>static（跟随发行版不懂的，比如函数库，manpage等）</td>
        <td>/usr（该发行版提供的软件放到此处）<br/>/opt（第三方软件放到此处）</td>
        <td>/ect（配置文件）<br/>/boot（开机和内核文件）</td>
    </tr>
    <tr>
        <td>variable</td>
        <td>/var/maail<br/>/var/spool/news</td>
        <td>/var/run（程序运行相关）<br/>/var/lock（程序运行相关） </td>
    </tr>
</table>
 
FHS建议，/目录所在分区比较小为好，越大越可能发生错误。其中，/etc、/bin、/dev、/lib和/sbin最好和/目录放在同一分区。

FHS定义，/目录下一般有这些目录：

* /bin：通常用来放置可执行的程序，而且在单用户维护模式下还可以被执行。比如cat，chmod，chown，date，mv等常用命令。

* /boot：包括内核、开机菜单和开机配置。可能还有grub。kernel文件的文件名通常是vmlinuz。
 
* /dev：放置设备。

* /etc：放配置，/etc/init.d放各种服务的默认启动脚本，/etc/xintd.d：super deamon管理的各项服务配置文件目录。

* /home：不做解释。

* /lib：lib放置的是开机会用到的库，以及/bin和/sbin下依赖的库。

* /media：放置可删除的设备，比如软盘，光盘，U盘。

* /mnt：临时挂载目录。

* /opt：第三方软件目录，比如一些软件不属于发行版（kde），可以放在这里。

* /root：root的根目录。

* /sbin：sbin下面放着开机过程中需要的各种命令，包括开机，修复和还原系统的命令。常见的有ifconfig，fdis，fsck，init和mkfs。类似的，自己安装的各种系统，有可能在/usr/sbin和/usr/local/sbin下。

* /usr（记住，usr=unix software source）：/usr/local放了本机自行安装的软件，比如发行版某个包的高级版本，建议放到/usr/local下；/usr/sbin放了非系统正常运行需要的系统命令，比如一些daemon。

* /var：/var/lib放了运行时的数据文件，/vaar/run放了pid文件
