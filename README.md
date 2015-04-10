### Steps

Main Reference: https://www.howtoforge.com/linux_lvm_p2

#### Adding a new logical volume

- Tell what physical volumes exist in the volume group
```bash
$ sudo pvdisplay -C --separator '  |  ' -o pv_name,vg_name # ref: http://superuser.com/a/640914
```
- If need to add more physical volumes, (assuming they are already formatted):
```bash
$ sudo vgextend VolGroup00 /dev/sdg1
$ sudo vgextend VolGroup00 /dev/sdb1
$ sudo vgextend VolGroup00 /dev/sdc1
# verfiy if it worked:
$ sudo vgdisplay # VG Size should be much higher
```
- Create a new logical volume (now that we have all these physical volumes attached to the volume group)
```bash
$ sudo lvcreate --name <lvm name> --size 1500G VolGroup00
$ sudo lvdisplay # will show the newly created <lvm name>
```
- Format the new volume with some filesystem
```bash
$ sudo mkfs.ext4 /dev/VolGroup00/<lvm name> # formats it into ext4
```
- Mount the volume
```bash
$ sudo mkdir /media/mountpoint
$ sudo mount /dev/VolGroup00/<lvm name> /media/mountpoint
$ df -h # should show the newly added volume
```

#### Extending an existing logical volume

- Extend the amount of physical space available to the volume group by using the `vgextend` as above
- Extend the logical space to one of the logical partitions
```bash
$ sudo lvextend -L+400G /dev/VolGroup00/lvol0 # to extend by 400G
```
- Now extend the filesystem in that logical volume to the extended disk (Note this is for EXT4 filesystems that I typically use)
(for other fs, try http://tldp.org/HOWTO/LVM-HOWTO/extendlv.html)
```bash
# from https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Storage_Administration_Guide/ext4grow.html
# and http://www.microhowto.info/howto/increase_the_size_of_an_ext2_ext3_or_ext4_filesystem.html
$ sudo resize2fs /dev/mapper/VolGroup00-lvol0  # this can take several minutes
```

