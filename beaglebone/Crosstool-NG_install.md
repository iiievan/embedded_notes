# Install Crosstool-NG

## install dependencies and clone repository
```
sudo apt-get update
sudo apt-get upgrade  
sudo apt-get install 
sudo apt-get install automake 
sudo apt-get install bison 
sudo apt-get install chrpath 
sudo apt-get install flex 
sudo apt-get install g++ 
sudo apt-get install git 
sudo apt-get install gperf 
sudo apt-get install gawk 
sudo apt-get install libexpat1-dev 
sudo apt-get install libncurses5-dev 
sudo apt-get install libsdl1.2-dev 
sudo apt-get install libtool 
sudo apt-get install python2.7-dev 
```
if python2.7-dev has no installation candidate just install python3-dev 
```
sudo apt-get install texinfo 
sudo apt-get install help2man 
sudo apt-get install make 
sudo apt-get install libtool-bin 
sudo apt-get install libtool-doc
```
* clone the repo and config it
```
sudo mkdir x-tools
sudo chmod a+rwx x-tools
git clone https://github.com/crosstool-ng/crosstool-ng.git
```
```
cd crosstool-ng
git checkout crosstool-ng-1.24.0
```
## configure make
```
./bootstrap
./configure --enable-local
```
> if error:  
> configure: error: could not find bash >= 3.1  
> in configure.ac line 194:  
> ```
>        [[_BASH_ver=$($ac_path__BASH --version 2>&1 \
>                     |$EGREP '^GNU bash, version (3\.[1-9]|4)')
> ```
> change on:
> ```
>        [[_BASH_ver=$($ac_path__BASH --version 2>&1 \
>                     |$EGREP '^GNU bash, version (3\.[1-9]|4|5)')
> ```

next make it:
```
nproc
make -j20
sudo make install
```

## View the result
```
./ct-ng
```
view available examples for many platforms
```
./ct-ng list-samples
./ct-ng show-arm-cortex_a8-linux-gnueabi
```
> ...
> [L...]   arm-cortex_a8-linux-gnueabi  
>    Languages       : C,C++  
>    OS              : linux-4.20.8  
>    Binutils        : binutils-2.32  
>    Compiler        : gcc-8.3.0  
>    C library       : glibc-2.29  
>    Debug tools     : duma-2_5_15 gdb-8.2.1 ltrace-0.7.3 strace-4.26  
>    Companion libs  : expat-2.2.6 gettext-0.19.8.1 gmp-6.1.2 isl-0.20 libelf-0.8.13   
>                      libiconv-1.15 mpc-1.1.0 mpfr-4.0.2 ncurses-6.1 zlib-1.2.11  
>    Companion tools :  
>...

## select desired platform
```
./ct-ng arm-cortex_a8-linux-gnueabi
./ct-ng menuconfig
```
> for version 1.24.0 and beaglebone black
> - Paths and misc options, disable Render the toolchain read-only
> (CT_INSTALL_DIR_RO)
> - Paths and misc options| Try features marked as EXPERIMENTAL: ticked
> - In Target options | Target Architecture, select ARM вместо ALPHA
> - In Target options | Floating point, select hardware (FPU) (CT_ARCH_FLOAT_HW)
> - In Target options | Use specific FPU: vfp(neon-vfpv3?)
> - in C library | extra config write flag --enable-obsolete-rpc


build crosstool-ng:
```
./ct-ng build
```

To rebuild ct-ng with another config:
uset ${CC} variable if it setted:
```
unset CC
./ct-ng build
```

> if this error appears:   
> ``` 
> [EXTRA]    Retrieving 'isl-0.20'  
> [ERROR]    isl: download failed   
> ```
> do this:  
> ```
> cd .build/tarballs  
> wget https://libisl.sourceforge.io/isl-0.20.tar.gz    
> wget https://github.com/libexpat/libexpat/releases/download/R_2_2_6/expat-2.2.6.tar.bz2   
> cd ../..   
> ```

> if this error appears:  
> ``` 
> [EXTRA]    Configuring binutils  
> [EXTRA]    Building binutils  
> [ERROR]    /home/ishopov/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/src/binutils/gold/errors.h:87:50: error: 'string'   
> in namespace 'std' does not name a type
> ``` 
> then:  
> ```
> cd crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/src/binutils/gold
> ```
> ```
> sudo sublime  errors.h
> ```
> and add 
> ```
> #include <string>
> ```


rebuild if errors appears:
```
./ct-ng build
```
## EXPORT TO PATH

чтобы экспортировать компилятор в перменную среды навсегда  
создадим скрипт экспорта
```
sudo sublime /usr/bin/export_variable_forever.sh
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
sudo chmod a+x export_variable_forever.sh
```

создаем переменную CC = arm-cortex_a8-linux-gnueabihf- 
```
./export_variable_forever.sh
```
Ввести 
```
CC
```
и 
```
arm-cortex_a8-linux-gnueabihf-
```

допишем путь к компилятору в переменную среды.
```
sudo sublime /etc/environment
```
дописываем в конец строки двоеточие и после него путь к компилятору 
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/bin"
```
**!!!после этого перезгаружаем систему чтобы изменения вступили в силу. Скрипт может не перезагрузить.**


## Compilation example

1. Узнать версию кросскомпилятора:
```
${CC}gcc --version
```
> arm-cortex_a8-linux-gnueabihf-gcc (crosstool-NG 1.24.0) 8.3.0  
> Copyright (C) 2018 Free Software Foundation, Inc.  
> This is free software; see the source for copying conditions.  There is NO  
> warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. 

2. Получить текущую конфигурацию кросскомпилятора:
```
${CC}gcc -v
```
> Using built-in specs.  
> COLLECT_GCC=arm-cortex_a8-linux-gnueabihf-gcc  
> COLLECT_LTO_WRAPPER=/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/libexec/gcc/arm-cortex_a8-linux-gnueabihf/8.3.0/lto-wrapper  
> Target: arm-cortex_a8-linux-gnueabihf  
> Configured with: /home/mbokastnev/x-tools/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/src/gcc/configure --build=x86_64-build_pc-linux-gnu   
> --static-libgcchost=x86_64-build_pc-linux-gnu   
> --target=arm-cortex_a8-linux-gnueabihf   
> --prefix=/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf   
> --with-sysroot=/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/arm-cortex_a8-linux-gnueabihf/sysroot --enable-languages=c,c++   
> --with-cpu=cortex-a8   
> --with-fpu=vfp   
> --with-float=hard   
> --with-pkgversion='crosstool-NG 1.24.0'   
> --enable-__cxa_atexit   
> --disable-libmudflap   
> --disable-libgomp   
> --disable-libssp   
> --disable-libquadmath   
> --disable-libquadmath-support   
> --disable-libsanitizer   
> --disable-libmpx   
> --with-gmp=/home/mbokastnev/x-tools/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/buildtools   
> --with-mpfr=/home/mbokastnev/x-tools/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/buildtools   
> --with-mpc=/home/mbokastnev/x-tools/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/buildtools 
> --with-isl=/home/mbokastnev/x-tools/crosstool-ng/.build/arm-cortex_a8-linux-gnueabihf/buildtools   
> --enable-lto   
> --with-host-libstdcxx='-static-libgcc -Wl,-Bstatic,-lstdc++,-Bdynamic -lm'   
> --enable-threads=posix   
> --enable-target-optspace   
> --enable-plugin   
> --enable-gold   
> --disable-nls   
> --disable-multilib   
> --with-local-prefix=/home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/arm-cortex_a8-linux-gnueabihf/sysroot   
> --enable-long-long  
> Thread model: posix  
> gcc version 8.3.0 (crosstool-NG 1.24.0)  


3. далее компилируем простенький кодец.
созадем фаил helloworld.c где нибудь такого содержания
```
sudo sublime helloworld.c
```
```
#include <stdio.h>
#include <stdlib.h>

int main (int argc, char *argv[])
{
    printf ("Hello, world!\n");
    return 0;
}
```

компилировать вот так:
```
${CC}gcc helloworld.c -o helloworld
```

проверить испольняемый фаил на содержимое:
```
file helloworld
```
должен выдать что то такое если все окей:
> helloworld: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked,  
> interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 4.20.8, with debug_info, **not stripped**

если возникает ошибка:  
> arm-cortex_a8-linux-gnueabihf-gcc helloworld.c -o helloworld  
> /home/mbokastnev/x-tools/arm-cortex_a8-linux-gnueabihf/lib/gcc/arm-cortex_a8-linux-gnueabihf/8.3.0/../../../..  
> /arm-cortex_a8-linux-gnueabihf/bin/ld.bfd: cannot open output file helloworld:  
> Отказано в доступе collect2: error: ld returned 1 exit status 

для папки code нужно поменять пользователя и его права:
```
whoami
sudo chown -R "mbokastnev" code
chmod -R 777 code
```
