# Builldroot install

1. форматируем флешку
```
$: sudo apt install gparted
```
сделать необходимые операции в gparted
```
$: sudo mkdir -p /mnt/sdcard
$: lsblk
$: sudo mount /dev/mmcblk0p1 /mnt/sdcard
```
2. ставим **buildroot**
```
 X.git clone git://git.buildroot.net/buildroot -b 2021.08.1    // нет gdbserver
git clone git://git.buildroot.net/buildroot -b 2022.02
cd buildroot
$: sudo apt-get update
$: sudo apt-get upgrade
$: sudo apt install sed
$: sudo apt install make
$: sudo apt install binutils
$: sudo apt install build-essential
$: sudo apt install diffutils
$: sudo apt install gcc
$: sudo apt install g++
$: sudo apt install bash
$: sudo apt install patch
$: sudo apt install gzip
$: sudo apt install bzip2
$: sudo apt install perl
$: sudo apt install tar
$: sudo apt install cpio
$: sudo apt install unzip
$: sudo apt install rsync
$: sudo apt install file
$: sudo apt install bc
$: sudo apt install findutils
$: sudo apt install wget
$: sudo apt install python3
$: sudo apt install python-is-python3
$: sudo apt install libncurses-dev
```
Если официальный недоступен то можно скопировать с  github
```
git clone https://github.com/buildroot/buildroot.git
git tag
```
будет что-то типа:
```
...
2021.08.1
2021.08.2
2021.08.3
2021.11
2021.11-rc1
2021.11-rc2
2021.11-rc3
2021.11.1
2021.11.2
2021.11.3
2022.02
2022.02-rc1
2022.02-rc2
2022.02-rc3
2022.02.1
...
```
```
git checkout 2021.08.1
```
Проверить что в переменной PATH нет лишних пробелов и двоеточий:
```
$: echo $PATH
$: sudo sublime /etc/environment
$: sudo sublime ~/.bashrc
$: sudo sublime ~/.profile
```
выбираем конфигурацию beaglebone_defconfig
```
$: make list-defconfigs
$: make beaglebone_defconfig   
$: make menuconfig
```
настраиваем:
```
Target options -->
    Floating point strategy(VFPv3)       
...
Toolchain -->
    Toolchain type (External toolchain)  --->
    ...
    [*] Copy gdb server to the Target
    ...
    [*] Build cross gdb for the host                                  
    [ ]   TUI support                                                 
    [ ]   Python support                                               
    [ ]   Simulator support                                            
          GDB debugger Version (gdb 10.x)  --->
...
System configuration -->    
    /dev management (Dynamic using devtmpfs only)  --->
    ...
    [*] Use symlinks to /usr for /bin, /sbin and /lib                 
    [*] Enable root login with password                                
    (123456) Root password
    ...
    (board/beaglebone/overlay) Root filesystem overlay directories
...
Kernel -->    
    X.Kernel version (Custom version)  --->
    X.(5.15.35) Kernel version
    ...
    (am335x-evm am335x-boneblack) In-tree Device Tree
Target packages -->
    Debugging, profiling and benchmark -->
        ...        
        [*] gdb  
        -*-   gdbserver                                                        
        [ ]   full debugger
        ...
        [*] valgrind                                                       
        [*]   Memcheck: a memory error detector (NEW)                     
        [*]   Cachegrind: a cache and branch-prediction profiler (NEW)     
        [*]   Callgrind: a call-graph generating cache and branch predict  
        [*]   Helgrind: a thread error detector (NEW)                      
        [*]   DRD: a thread error detector (NEW)                           
        [*]   Massif: a heap profiler (NEW)                                
        [*]   DHAT: a dynamic heap analysis tool (NEW)                     
        [ ]   SGCheck: an experimental stack and global array overrun det  
        [ ]   BBV: an experimental basic block vector generation tool (NE 
        [ ]   Lackey: an example tool (NEW)                               
        [ ]   Nulgrind: the minimal Valgrind tool (NEW)
        ...
    Miscellaneous  --->
        ...
        [*]haveged // для ускорения загрузки random: crng init done и соответственно соединения по ssh
    Networking applications -->
        ...
        [*] dropbear                                                       
        [*]   client programs (NEW)                                       
        [ ]   disable reverse DNS lookups (NEW)                           
        [*]   optimize for size (NEW)                                      
        [ ]   log dropbear access to wtmp (NEW)                            
        [ ]   log dropbear access to lastlog (NEW)                         
        [ ]   enable legacy crypto (NEW)                                   
        ()    path to custom localoptions.h definitions file (NEW)
        ...
       [*] openssh                                                       
       [*]   client (NEW)                                                 
       [*]   server (NEW)                                                 
       [*]   key utilities (NEW)
       ...
```
проверить что rootfs стоит в режиме записи чтения.
```
System configuration -->
    ....
    [*] remount root filesystem read-write during boot
```
Создать оверлей для подключения по сети при соединении кабеля
```
$ cd ~/buildroot/board/beaglebone
$ sudo mkdir -p overlay/etc/network
$ cd overlay/etc/network
$ sudo sublime interfaces
```
создать в network фаил следующего содержания(interfaces):
```
auto eth0
iface eth0 inet static
address 192.168.1.112
netmask 255.255.255.0

auto lo
iface lo inet loopback
```
или через dhcp:
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
```
После этого запустить компиляцию:
```
$: make 2>&1
```
3. настроить соединение по ethernet. соединить патчкордом beaglebone с ноутбуком:

![img_1](./pictures/image1.png)

4. После того как компиляция завершится, разворачиваем образ на SD-карту(смотри заметку "Buildroot. копирование ядра,u-boot и фаиловой системы на флешку.")

5. Затем подключаем терминал для связи с платой через переходник USB-TTL. 
   Чтобы посмотреть какие устройства ttyUSB* подключены
```

$: dmesg | grep tty
$: sudo gtkterm -p /dev/ttyUSB0 -s 115200
```
6. Затем вставляем карточку в плату, зажимаем ресет, подаем питание через пять секунд отжимаем резет.

7. Вставляем BBB через порт Ehernet в роутер.

8. на хост-машине нужно запинговать BBB или проверить что он есть в сети утилитой nmap
```
$: nmap -sn 192.168.88.0/24
```
наш IP проверяется 
```
$: hostname -I
```
когда я дро загрузится входим запускаем сеть если она уже не запустилась, проверяем наш ip адрес
```
buildroot login:root
Passwword:123456
# ifup -a
# ifconfig
```
9. Затем можно подключиться к BBB через ssh:
```
$: ssh root@10.0.0.2
```