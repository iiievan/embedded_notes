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
$: sudo apt install
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
$: sudo apt install python-is-python3 l
$: sudo apt install ibncurses-dev
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
$: make 2>&1  build.log
```
настроить соединение по ethernet. соединить патчкордом beaglebone с ноутбуком:
![](.pictures/Image1.png)
