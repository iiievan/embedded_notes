
# ESP32 Quick Start
## Установить ESP-IDE с сайта [espressif](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/linux-setup.html) 
1. загрузить EIM и воспользоваться графической оболочкой для установки ESP-IDF(v6.0.2)
```bash
echo "deb [trusted=yes] https://dl.espressif.com/dl/eim/apt/ stable main" | sudo tee /etc/apt/sources.list.d/espressif.list
```
```bash
sudo apt update
```
```bash 
sudo apt install eim
```
2. далее воспользоваться графической оболочкоий EIM(в приложениях ubuntu) -> Easy Install 
3. зайти в директорию где стоит ESP-IDF
```bash 
cd $HOME/.espressif/v6.0.2/esp-idf
```
и выполнить установку
```bash
. ./install.sh
```
4. установить плагин ESP-IDE в VSCode
5. записать в переменные среды скрипт export.sh для использования в терминале idf.py
```bash
echo 'source /home/mbokastnev/.espressif/v6.0.2/esp-idf/export.sh' >> ~/.bashrc
```
```bash
source ~/.bashrc
```
выйти из системы и зайти заново или ребутнуться.

## настройка отладки через встроенный JTAG
установить openocd в систему.
```bash
sudo apt install openocd
```
```bash
sudo usermod -a -G dialout $USER
sudo nano /etc/udev/rules.d/50-esp32-jtag.rules
```
вписать в правила ID espressif
```bash
SUBSYSTEM=="usb", ATTR{idVendor}=="303a", ATTR{idProduct}=="1001", MODE="0666", GROUP="dialout"
```
Ctrl+O -> Ctrl+X

```bash
sudo udevadm control --reload-rules
```
```bash
sudo udevadm trigger
```
выйти из системы и зайти заново или ребутнуться. Отладка через openocd должна заработать в VSCode.
