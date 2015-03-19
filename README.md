### Steps

Main Reference: https://www.howtoforge.com/linux_lvm_p2

1. Tell what physical volumes exist in the volume group
```bash
$ sudo pvdisplay -C --separator '  |  ' -o pv_name,vg_name # ref: http://superuser.com/a/640914
```
2. If need to add more physical volumes, (assuming they are already formatted):
```bash
$ sudo vgextend VolGroup00 /dev/sdg1
$ sudo vgextend VolGroup00 /dev/sdb1
$ sudo vgextend VolGroup00 /dev/sdc1
# verfiy if it worked:
$ sudo vgdisplay # VG Size should be much higher
```
3. Create a new logical volume (now that we have all these physical volumes attached to the volume group)
```bash
$ sudo lvcreate --name <lvm name> --size 1500G VolGroup00
$ sudo lvdisplay # will show the newly created <lvm name>
```
4. Format the new volume with some filesystem
```bash
$ sudo mkfs.ext4 /dev/VolGroup00/<lvm name> # formats it into ext4
```
5. Mount the volume
```bash
$ sudo mkdir /media/mountpoint
$ sudo mount /dev/VolGroup00/<lvm name> /media/mountpoint
$ df -h # should show the newly added volume
```
