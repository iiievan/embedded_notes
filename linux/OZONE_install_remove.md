# Установка
```bash
sudo dpkg -i Ozone_Linux_V350_x86_64.deb
```

# Удаление
```bash
sudo apt purge ozone
```
Найти оставшиеся пользовательские данные
```bash
find ~/.config ~/.local/share ~/.cache -maxdepth 5 -iname '*ozone*' -print 2>/dev/null
```
например выдаст что то вроде 
```bash
/home/mbokastnev/.config/SEGGER/Ozone.conf
/home/mbokastnev/.local/share/data/SEGGER/Ozone
```
удаляем их тоже
```bash
rm -f -- "$HOME/.config/SEGGER/Ozone.conf"
```
```bash
rm -rf -- "$HOME/.local/share/data/SEGGER/Ozone"
```
на этом все. Теперь можно ставить новую версию... 