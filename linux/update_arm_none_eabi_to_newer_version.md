# Обновление arm-none-eabi до последней версии 
если команда sudo apt-get install binutils-arm-none-eabi обновляет не до последней. То проделываем следующие шаги
## 1. Удалить старый тулчейн в системе если он есть:
```
sudo apt remove gcc-arm-none-eabi
```
## 2. Устанавливаем новый тулчейн
создаем директорию для toolchain
```
mkdir -p ~/tools/arm-toolchain
```
```
cd ~/tools/arm-toolchain
```
качаем тулчайн XX.X.XXX заменить на свою верчсию 13.2.rel1, посмотреть последнюю версию можно по этой ссылке [arm-none-eabi](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)
```
wget https://developer.arm.com/-/media/Files/downloads/gnu/XX.X.XXX/binrel/arm-gnu-toolchain-XX.X.XXX-x86_64-arm-none-eabi.tar.xz
```
распаковать
``` 
tar -xf arm-gnu-toolchain-13.2.rel1-x86_64-arm-none-eabi.tar.xz
```
переименовать
``` 
sudo mv sudo mv arm-gnu-toolchain-13.2.rel1-x86_64-arm-none-eabi/ arm-none-eabi-13.2.rel1
```

прописать в PATH
``` 
echo 'export PATH="/home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin:$PATH"' >> ~/.bashrc
```
```
source ~/.bashrc
```

## 3. Переименовать все символические ссылки со старого тулчейна на новый:

### Основные компиляторы
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-gcc /usr/bin/arm-none-eabi-gcc
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-g++ /usr/bin/arm-none-eabi-g++
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-g++ /usr/bin/arm-none-eabi-c++
``` 


### Ассемблер
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-as /usr/bin/arm-none-eabi-as
```

### Другие важные утилиты
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-ld /usr/bin/arm-none-eabi-ld
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-objcopy /usr/bin/arm-none-eabi-objcopy
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-objdump /usr/bin/arm-none-eabi-objdump
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-size /usr/bin/arm-none-eabi-size
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-gdb /usr/bin/arm-none-eabi-gdb
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-nm /usr/bin/arm-none-eabi-nm
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-strip /usr/bin/arm-none-eabi-strip
``` 
```
sudo ln -s /home/mbokastnev/tools/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-ar /usr/bin/arm-none-eabi-ar
```
```
sudo ln -s /home/mbokastnev/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-ranlib /usr/bin/arm-none-eabi-ranlib
```
```
sudo ln -s /home/mbokastnev/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-strings /usr/bin/arm-none-eabi-strings
```
```
sudo ln -s /home/mbokastnev/arm-none-eabi-13.2.rel1/bin/arm-none-eabi-readelf /usr/bin/arm-none-eabi-readelf
```

## 4.Посмотреть что все применилось нормально:
```
echo $PATH
arm-none-eabi-gcc --version
```