#入门篇
3.1~3.5：https://asciinema.org/a/BjqjNV9MeDLq56k7H77Pu3XCS
3.5~4.2：https://asciinema.org/a/Gn5k8f3x1iusMhNG2nxFV9r5l
4.3~4.4：https://asciinema.org/a/SMheCQd3s4gS5a0Ob2A6hmf17
5.1~5.4：https://asciinema.org/a/OInaUDUZCfYI9669Dd7ndyB4r
6.1~6.7：https://asciinema.org/a/52PyddOcnwWOfWPN60yBFOfbP
7.1~7.7：https://asciinema.org/a/XFqrn8PGQugQ21Kl3QXxijReQ

附：在实际操作中，3.1的操作一直没有找到录屏的方法。这些操作大多与重启、关闭系统有关，由于会导致虚拟机关闭/重启，因此未能录制，除此以外，4.3操作未成功，后来重新补录了这一步的操作
4.3操作补丁：https://asciinema.org/a/aQb26Wq9YEwZ6dXqxcD23r6GF

#实战篇
https://asciinema.org/a/XLtvyhS2BG1XP3BQwKch4Z1Og

#自查清单
###如何添加一个用户并使其具备sudo执行程序的权限？
添加用户：sudo adduser [username]
使其具备sudo执行程序的权限：sudo usermod -G sudo [username]

###如何将一个用户添加到一个用户组？
usermod -G groupA [username]
usermod -a -G groupA [username]

###如何查看当前系统的分区表和文件系统详细信息？
查看当前系统的分区表：lsblk
查看文件系统详细信息：dumpe2fs

###如何实现开机自动挂载Virtualbox的共享目录分区？
首先，在Virtualbox的设置中添加所要挂载的物理机分区为共享目录。然后，在虚拟机的/mnt目录下新建一个共享文件的挂载目录，到时外部的驱动器根目录就直接挂载到这个目录下。在该目录下再次新建3个目录，用于挂载实际的3个共享目录，接下来，在虚拟机里修改/etc/fstab文件，增加如下的语句：
drv_d	/mnt/win10/drv_d	vboxsf 	rw,auto	0	0
drv_e	/mnt/win10/drv_e	vboxsf 	rw,auto	0	0
drv_o	/mnt/win10/drv_o	vboxsf 	rw,auto	0	0
重启之后，ubuntu便会自动将你所选定的所有目录自动挂载到指定的地址下。

###基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量？
动态扩容
1.当前卷组有剩余空间时，通过命令：
    lvextend -l +[添加的数量] [逻辑卷]将空间增加给逻辑卷
    然后通过：
    resize2fs [逻辑卷]修改文件系统大小
2.卷组空间不足时，只能添加新的物理卷
用vgextend [新的物理卷] [加入的卷组]
此时卷组有剩余空间，按照第一种方法操作即可

缩减容量
先通过命令umount [所在路径]卸载
用resize2fs [逻辑卷] [缩小后的大小]
lvreduce -L [缩小后的大小] [逻辑卷]
mount [逻辑卷] [所在路径]挂载
lvremove [逻辑卷]
vgremove [卷组]删除逻辑卷和卷组

###如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？
设置参数
want = network-online.target
After = network-online.target
可实现在网络连通时执行指定的脚本

在网络断开时的操作目前未解决qwq

###如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动？实现杀不死？
设置参数:
Restart = always:默认值为no，任何情况下都必须重启服务
StartLimitlnterval:无限次重启
RestartSec = 1：重启的等待时间为1s
参数设置成功后启动服务即可：
systemctl start [服务名]