mkfs stands for Make a Linux filesystem. This command is a little different than most. There are a few options that can be used such as specifying the type, but an easier option is to call the specific subprogram associated with the file system you want to create. In the shell type ``mkfs`` and then press the tab key twice to show all the available file system programs.

```
root@pbs:~# mkfs.
mkfs.bfs     mkfs.btrfs   mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    mkfs.fat     mkfs.minix   mkfs.msdos   mkfs.vfat    mkfs.xfs 
```

The file system you want to use is usually available as a subprogram and with that you can specify additional options specifically related to that file system.