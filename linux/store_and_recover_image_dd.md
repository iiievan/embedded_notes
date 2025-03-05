# store and recover linux disk image

1. use disk free tool to see free and used space of partition to be imaged
the image size be average equal to used space of partition
```
df -hT
```
2. reboot and run live usb ubuntu.

3. list parttion for saving(recovery) and storage partitons
```
lsblk
```
```
...
nvme0n1     259:0    0 953.9G  0 disk 
├─nvme0n1p1 259:1    0   100M  0 part 
├─nvme0n1p2 259:2    0    16M  0 part 
├─nvme0n1p3 259:3    0 269.4G  0 part 
├─nvme0n1p4 259:4    0   764M  0 part 
└─nvme0n1p5 259:5    0 683.6G  0 part /mnt

nvme1n1     259:1    0 465.8G  0 disk 
├─nvme1n1p1 259:7    0  93.2G  0 part /
├─nvme1n1p2 259:8    0   122M  0 part /boot/efi
├─nvme1n1p3 259:9    0 353.8G  0 part /home
└─nvme1n1p4 259:10   0  18.6G  0 part [SWAP]
...
```
4. mount partition to store images of target disk partitions(another disk partitons)
```
sudo mount /dev/nvme0n1p5 /mnt
```
for ntfs partitions
```
sudo mkdir /mnt/ntfs1
sudo mount -t ntfs-3g /dev/nvme0n1p5 /mnt/ntfs1
```

5. to store partition to image and zip it to reduce space
```
sudo dd if=/dev/nvme1n1p1 conv=sync bs=64K status=progress | gzip -c  > /mnt/ntfs1/ubuntu_24_ddimg/root_93_2G.image
sudo dd if=/dev/nvme1n1p2 conv=sync bs=64K status=progress | gzip -c  > /mnt/ntfs1/ubuntu_24_ddimg/boot_112M.image
sudo dd if=/dev/nvme1n1p3 conv=sync bs=64K status=progress | gzip -c  > /mnt/ntfs1/ubuntu_24_ddimg/home_353_8G.image
```
6. for recover partition from zipped image
```
sudo bash
gunzip -c /mnt/ntfs1/ubuntu_24_ddimg/root_93_2G.image | dd of=/dev/nvme1n1p1 conv=sync status=progress
gunzip -c /mnt/ntfs1/ubuntu_24_ddimg/boot_112M.image | dd of=/dev/nvme1n1p2 conv=sync status=progress
gunzip -c /mnt/ntfs1/ubuntu_24_ddimg/home_353_8G.image | dd of=/dev/nvme1n1p3 conv=sync status=progress
```
