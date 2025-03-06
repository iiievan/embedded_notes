# u-boot build for beaglebone black

1. если еще не добавлена надо добавить префикс тулчейна в переменные среды для удобства:  
```
$ cd ~/
$ sudo sublime export_variable_forever.sh
```

его содержимое:
```
#!/bin/bash
echo "Enter variable name: "
read variable_name
echo "Enter variable value: "
read variable_value
echo "adding " $variable_name " to environment variables: " $variable_value
echo "export "$variable_name"="$variable_value>>~/.bashrc
echo $variable_name"="$variable_value>>~/.profile
echo $variable_name"="$variable_value>>/etc/environment
source ~/.bashrc
source ~/.profile
echo "do you want to restart your computer to apply changes in /etc/environment file? yes(y)no(n)"
read restart
case $restart in
    y) sudo shutdown -r 0;;
    n) echo "don't forget to restart your computer manually";;
esac
exit
```

Сохраните эти строки в .sh file, затем сделайте его исполняемым.
```
sudo chmod a+rwx export_variable_forever.sh
```

создаем переменную CC = arm-cortex_a8-linux-gnueabihf-
```
./export_variable_forever.sh
```

Ввести **CC** и **arm-cortex_a8-linux-gnueabihf-**  обратить внимание на черточку вконце.

```
sudo sublime /etc/environment
```

дописываем в конец строки двоеточие и после него путь к компилятору
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/bin"
```

**!!!после этого перезгаружаем систему чтобы изменения вступили в силу. Скрипт может не перезагрузить.**

2. клонируем u-boot репозиторий и собираем u-boot
```
git clone git://git.denx.de/u-boot.git
cd u-boot
```

посмотреть доступные тэги для переключения:
```
git tag
```

> ...  
> v2021.01  
> v2021.01-rc1  
> v2021.01-rc2  
> v2021.01-rc3  
> v2021.01-rc4  
> v2021.01-rc5  
> v2021.04  
> v2019.01-rc2  
> v2019.01-rc3  
> v2019.04  
> v2019.04-rc1  
> ...

> чтобы выйти из режима просмотра 'q' 

переключаемся на нужную ветку:
```
git checkout v2021.04
make CROSS_COMPILE=${CC} am335x_boneblack_defconfig
nproc
make CROSS_COMPILE=${CC} -j20
```
3. далее форматируем sd карту
```
lsblk
```
будут разделы типа mmcblk0 или sdb,sdc. Будь бдителен, /dev/sda  - может быть раздел с системой

> ...  
> loop25        7:25   0   568K  1 loop /snap/snapd-desktop-integration/253  
> sda           8:0    1   1.9G  0 disk   
> nvme1n1     259:0    0 465.8G  0 disk   
> ├─nvme1n1p1 259:1    0  93.2G  0 part /  
> ├─nvme1n1p2 259:2    0   122M  0 part /boot/efi  
> ├─nvme1n1p3 259:3    0 353.8G  0 part /home  
> └─nvme1n1p4 259:4    0  18.6G  0 part [SWAP]  
> nvme0n1     259:5    0 953.9G  0 disk   
> ├─nvme0n1p1 259:6    0   100M  0 part   
> ...  

Если есть разделы, их нужно отмонтировать(-f - принудительно).
```
sudo umount -f /dev/sda 
sudo umount -f /dev/sdb2
sudo umount -f /dev/mmcblk0p1
```
 и так далее.

Cтираем начало SD карты.(испльзовать sdc или sdb вместо mmcblk0)
```
sudo dd if=/dev/zero of=/dev/mmcblk0 bs=1M count=16
```
или можно просто использовать утилиту disks или diskpart

далее используем  скрипт из крисса симмондса, он лежит в этой папке.
```
./format-sdcard.sh sda
```
если все успешно то выдаст такую штуку:
> ...  
> Allocating group tables: done                              
> Writing inode tables: done                              
> Creating journal (8192 blocks): done  
> Writing superblocks and filesystem accounting information: done  
>   
> SUCCESS! Your microSD card has been formatted  
> ...  
и появится два раздела в наутилусе boot и rootfs.

теперь копируй uboot на флешку:
```
cd code/u-boot
cp MLO u-boot.img /media/mbokastnev/boot
```

4. запускаем терминал gtkterm
```
sudo apt install gtkterm
```

создаем скрипт для удобства запуска:
```
sudo sublime usbttl.sh
```
с содержимым:
```
#!/bin/bash
sudo gtkterm -p /dev/ttyUSB0 -s 115200
```
```
sudo chmod a+rwx usbttl.sh
```

вставляем переходник, запускем терминал
```
./usbttl.sh
```

зажимаем резет и подаем питание на плату, держим 5сек, отпусаем
должно появиться:
> $ U-Boot#  
