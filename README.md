# wdmycloud

Following instructions at https://drive.google.com/drive/folders/0B_6OlQ_H0PxVZTBtM3EyRVhDVms.  The difference is that I used my raspberrypi as my machine and as a result, my external hard drive was /dev/sda.

```
root@raspberrypi:/home/pi# parted /dev/sda
GNU Parted 3.2
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print                                                            
Error: /dev/sda: unrecognised disk label
Model: Innostor Ext. HDD (scsi)                                           
Disk /dev/sda: 4001GB
Sector size (logical/physical): 512B/512B
Partition Table: unknown
Disk Flags: 
(parted) mklabel gpt                                                      
(parted) mkpart primary 528M 2576M                                        
(parted) mkpart primary 2576M 4624M                                    
(parted) mkpart primary 16M 528M
(parted) mkpart primary 4828M 100%
(parted) mkpart primary 4624M 4724M
(parted) mkpart primary 4724M 4824M
(parted) mkpart primary 4824M 4826M
(parted) mkpart primary 4826M 4828M
(parted) set 1 raid on                                                    
(parted) set 2 raid on                                                    
(parted) ok                                                               
  align-check TYPE N                        check partition N for TYPE(min|opt)
        alignment
  help [COMMAND]                           print general help, or help on
        COMMAND
  mklabel,mktable LABEL-TYPE               create a new disklabel (partition
        table)
  mkpart PART-TYPE [FS-TYPE] START END     make a partition
  name NUMBER NAME                         name partition NUMBER as NAME
  print [devices|free|list,all|NUMBER]     display the partition table,
        available devices, free space, all found partitions, or a particular
        partition
  quit                                     exit program
  rescue START END                         rescue a lost partition near START
        and END
  resizepart NUMBER END                    resize partition NUMBER
  rm NUMBER                                delete partition NUMBER
  select DEVICE                            choose the device to edit
  disk_set FLAG STATE                      change the FLAG on selected device
  disk_toggle [FLAG]                       toggle the state of FLAG on selected
        device
  set NUMBER FLAG STATE                    change the FLAG on partition NUMBER
  toggle [NUMBER [FLAG]]                   toggle the state of FLAG on partition
        NUMBER
  unit UNIT                                set the default unit to UNIT
  version                                  display the version number and
        copyright information of GNU Parted
(parted) quit
Information: You may need to update /etc/fstab.

root@raspberrypi:/home/pi# mkfs -t ext4 /dev/sda4                         
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 975575808 4k blocks and 243900416 inodes
Filesystem UUID: d3a98296-8364-4485-a8b1-169c09971f00
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
	102400000, 214990848, 512000000, 550731776, 644972544

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks):   
done
Writing superblocks and filesystem accounting information: done       

root@raspberrypi:/home/pi# 
root@raspberrypi:/home/pi# mkswap /dev/sda3
Setting up swapspace version 1, size = 489 MiB (512749568 bytes)
no label, UUID=13414405-178f-4931-b05a-dccabf5902c6
root@raspberrypi:/home/pi# mdadm --stop /dev/md*
mdadm: error opening /dev/md*: No such file or directory
root@raspberrypi:/home/pi# mdadm -A /dev/md0 /dev/sda1 /dev/sda2
mdadm: no recogniseable superblock on /dev/sda1
mdadm: /dev/sda1 has no superblock - assembly aborted
root@raspberrypi:/home/pi# mdadm --create /dev/md0 --level=1 --metadata=0.9 --raid-devices=2 /dev/sda1 /dev/sda2
mdadm: array /dev/md0 started.
root@raspberrypi:/home/pi# watch cat /proc/mdstat
root@raspberrypi:/home/pi# mount -t ext4 /dev/sda4 /mnt
root@raspberrypi:/home/pi# cd /mnt
root@raspberrypi:/mnt# mv ~pi/Downloads/original_v04.01.02-417.tar.gz .
root@raspberrypi:/mnt# ls
lost+found  original_v04.01.02-417.tar.gz
root@raspberrypi:/mnt# tar xvfz original_v04.01.02-417.tar.gz 
./
./kernel.img
./rootfs.img
./rootfs.md5
./config.img
root@raspberrypi:/mnt# dd if=kernel.img of=/dev/sda5
dd: writing to '/dev/sda5': No space left on device
194561+0 records in
194560+0 records out
99614720 bytes (100 MB, 95 MiB) copied, 17.9891 s, 5.5 MB/s
root@raspberrypi:/mnt# dd if=kernel.img of=/dev/sda6
196608+0 records in
196608+0 records out
100663296 bytes (101 MB, 96 MiB) copied, 15.0532 s, 6.7 MB/s
root@raspberrypi:/mnt# dd if=config.img of=/dev/sda7
2048+0 records in
2048+0 records out
1048576 bytes (1.0 MB, 1.0 MiB) copied, 0.223012 s, 4.7 MB/s
root@raspberrypi:/mnt# dd if=config.img of=/dev/sda8
2048+0 records in
2048+0 records out
1048576 bytes (1.0 MB, 1.0 MiB) copied, 0.148875 s, 7.0 MB/s
root@raspberrypi:/mnt# dd if=rootfs.img of=/dev/md0
3999616+0 records in
3999616+0 records out
2047803392 bytes (2.0 GB, 1.9 GiB) copied, 401.548 s, 5.1 MB/s
root@raspberrypi:/mnt# mkdir /mnt/hdd
root@raspberrypi:/mnt# mount /dev/md0 /mnt/hdd
root@raspberrypi:/mnt# touch /mnt/hdd/etc/.fresh_install
root@raspberrypi:/mnt# umount /mnt/hdd
root@raspberrypi:/mnt# shutdown -H 0 
```

After that I was able to login to the machine at http://wdmycloud.local.   When I got into the dashboard, the diagnoistics was red.  I tried to do a firmware update but got Device does not have enough space for upgrade 370002. I did a full system reset.   That took about 15 minutes.  After that, diagnotics was green and I was able to upgrade to the latest firmware and complete the rest of the setup.
