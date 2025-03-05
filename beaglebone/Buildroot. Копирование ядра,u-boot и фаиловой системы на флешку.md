# Buildroot. копирование ядра,u-boot и фаиловой системы на флешку.

1. Найти разделы на подключенной SD-карте:
```
$: cat /proc/partitions
```
или
```
$: lsblk
```
будут разделы типа mmcblk0 или sdb,sdc. Будь бдителен, /dev/sda  - это может быть раздел с системой .

2. Если есть разделы, их нужно отмонтировать(-f - принудительно).
```
$: sudo umount -f /dev/sdb1 
$: sudo umount -f /dev/sdb2
$: sudo umount -f /dev/mmcblk0p1
```
и так далее.
3. стираем начало SD карты.(испльзовать sdc или sdb вместо mmcblk0)
```
$: sudo dd if=/dev/zero of=/dev/mmcblk0 bs=1M count=16
```
4. создаем 2 раздела на карте:
```
$: sudo cfdisk /dev/mmcblk0
# Chose the dos partition table type
# Create a first small partition (128 MB), primary, with type e (W95 FAT16) and mark
# it bootable
# Create a second partition, also primary, with the rest of the available space, with
# type 83 (Linux).
# Save partition table(Write)
# Exit cfdisk(Quit)
```
5. отформатировать разделы:
```
$: sudo mkfs.vfat -F 32 -n BOOT /dev/sdb1
$: sudo mkfs.ext4 -L rootfs -E nodiscard /dev/sdb2
```
теперь можно ими пользоваться.

6. После сборки buildroot в папке output/images появятся нужные нам фаилы, скопируем их на загрузочный раздел флешки(128M)
```
$: sudo cp MLO u-boot.img zImage am335x-boneblack.dtb uEnv.txt /media/mbokastnev/BOOT
```
а фаиловую систему распакуем на второй раздел флешки:
```
$: sudo tar -C /media/mbokastnev/rootfs -xf rootfs.tar
```
7. подлючаем SD-карту, и подрубаем TTL конвертер с терминалом:
    чтобы посмотреть какие устройства ttyUSB* подключены
```
$: dmesg | grep tty
```
    запускаем терминал
```
$: sudo gtkterm -p /dev/ttyUSB0 -s 115200
```