# necessary notes for setup new linux workstation

1. install google-chrome
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i --force-depends google-chrome-stable_current_amd64.deb
```

2. install qbittorrent
```
sudo apt-get install qbittorrent
```
3. install gnome-tweaks
```
sudo apt-get install gnome-tweaks
```

4. install arm-none-eabi
```
sudo apt-get remove binutils-arm-none-eabi gcc-arm-none-eabi
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
sudo apt-get update
sudo apt-get install gcc-arm-none-eabi
sudo apt-get install gdb-arm-none-eabi
```

5. VScode column selection mode
> in menu bar **Selection->Switch to Ctr+Click for Multi-Cursor**
> **SHIFT + ALT + LEFT_Mouse** works for me

6. install cmake latest version
```
sudo apt remove --purge --auto-remove cmake

sudo apt update && \
sudo apt install -y software-properties-common lsb-release && \
sudo apt clean all

wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /etc/apt/trusted.gpg.d/kitware.gpg >/dev/null

sudo apt-add-repository "deb https://apt.kitware.com/ubuntu/ $(lsb_release -cs) main"

sudo apt update
sudo apt install kitware-archive-keyring
sudo rm /etc/apt/trusted.gpg.d/kitware.gpg
```
if sudo apt update gets the following error:
> Err:7 https://apt.kitware.com/ubuntu bionic InRelease  
> The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 6AF7F09730B3F0A4  
> Fetched 11.0 kB in 1s (7552 B/s)  
then:
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 6AF7F09730B3F0A4

sudo apt update
sudo apt install cmake
```
7. install jetbrains-toolbox
```
cd /opt/
sudo tar -xvzf ~/Downloads/jetbrains-toolbox-1.xx.xxxx.tar.gz
sudo mv jetbrains-toolbox-1.xx.xxxx jetbrains
jetbrains/jetbrains-toolbox
```
If you can't open the file type:
```
sudo apt install libfuse2
```

8. install recommended nvidia
```
ubuntu-drivers devices
sudo ubuntu-drivers list
sudo ubuntu-drivers install
sudo ubuntu-drivers install nvidia:535
sudo apt --purge remove '*nvidia*${DRIVER_BRANCH}*'
sudo apt autoremove
```
or you can download it from official [nvidia](https://www.nvidia.com/en-us/drivers/) site.

9. install anaconda3 and vscode
```
bash Anaconda3-2024.06-1-Linux-x86_64.sh
! pip install --user control
```

10. install sublime on linux
* write in /etc/hosts:
```
127.0.0.1 license.sublimehq.com
```
* copy arch and untar it
```
cp -a sublime_3176_x64.tar.bz2  /home/ishopov/
cd /home/ishopov/
tar vxjf sublime_3176_x64.tar.bz2
```
*  move to opt and rename
```
sudo mv sublime_text_3 /opt/
cd /opt
sudo mv sublime_text_3/ sublime_text
```
* create  menu reference and symbol reference and on sublime:
```
sudo cp sublime_text.desktop  /usr/share/applications/
sudo ln -s /opt/sublime_text/sublime_text /usr/bin/sublime
```
* open sublime_text.desktop in /usr/share/applications/ and change
```
Icon=/opt/sublime_text/Icon/48x48/sublime-text.png
```
instad of generic icon in favorities
* enter license and install package control, then enter in preferences:
```
{
    "Color_scheme": "Packages/Seti_UI/Scheme/Seti.tmThem",
    "Theme": "Seti.sublime-theme",
    "auto_indent": true,
    "color_scheme": "Packages/Color Scheme - Default/Solarized (Dark).tmTheme",
    "default_encoding": "Cyrillic (Windows 1251)",
    "detect_indentation": true,
    "fallback_encoding": "Cyrillic (Windows 1251)",
    "font_face": "Consolas",
    "font_size": 10,
    "ignored_packages":
    [
        "Vintage"
    ],
    "tab_size": 4,
    "theme": "Default.sublime-theme",
    "translate_tabs_to_spaces": true,
    "update_check": false,
    "use_tab_stops": true   
}
```
* enter license and preferences for sudo sublime:
```
----- BEGIN LICENSE -----
sgbteam
Single User License
EA7E-1153259
8891CBB9 F1513E4F 1A3405C1 A865D53F
115F202E 7B91AB2D 0D2A40ED 352B269B
76E84F0B CD69BFC7 59F2DFEF E267328F
215652A3 E88F9D8F 4C38E3BA 5B2DAAE4
969624E7 DC9CD4D5 717FB40C 1B9738CF
20B3C4F1 E917B5B3 87C38D9C ACCE7DD8
5F7EF854 86B9743C FADC04AA FB0DA5C0
F913BE58 42FEA319 F954EFDD AE881E0B
------ END LICENSE ------
```

11. install git
```
sudo apt install git-all
```

12. give all permissions to folder
```
sudo chmod a+rwx ~/code
```

13. working with gtkterm
```
sudo apt install gtkterm
```
* чтобы посмотреть какие устройства ttyUSB* подключены
```
dmesg | grep tty
```
* for run gtkterm create script:
```
sudo sublime usbttl.sh
```
with these content
```
#!/bin/bash
sudo gtkterm -p /dev/ttyUSB0 -s 115200
```
dont forget set all permissions:
```
sudo chmod a+rwx usbttl.sh
```
to run gtkterm
```
./usbttl.sh
```

14. reset clion script:
```
sudo sublime reset_clion.sh
```
```
#!/bin/bash
for product in IntelliJIdea WebStorm DataGrip PhpStorm CLion PyCharm GoLand RubyMine; do
    echo "[+] Resetting trial period for $product"

    echo "[+] Removing Evaluation Key..."
    rm -rf ~/.config/$product*/eval 2> /dev/null

    # Above path not working on latest version, Fixed below
    rm -rf ~/.config/JetBrains/$product*/eval 2> /dev/null

    echo "[+] Removing all evlsprt properties in options.xml..."
    sed -i 's/evlsprt//' ~/.config/$product*/options/other.xml 2> /dev/null

    # Above path not working on latest version, Fixed below
    sed -i 's/evlsprt//' ~/.config/JetBrains/$product*/options/other.xml 2> /dev/null

    echo
done

echo "Removing userPrefs files..."
rm -rf ~/.java/.userPrefs 2> /dev/null
```
dont forget set all permissions:
```
sudo chmod a+rwx reset_clion.sh
```
to reset clion
```
./reset_clion.sh
```

15. export variable forever
простой сценарий для этой процедуры, чтобы выполнить всю эту работу. Вам просто нужно установить имя и значение вашей переменной среды.
```
sudo sublime export_variable_forever.sh
```
fill with content:
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
```
sudo chmod a+rwx export_variable_forever.sh
```
using example:
```
./export_variable_forever.sh
```
```
...
Enter variable name: 
CC
Enter variable value: 
arm-cortex_a8-linux-gnueabihf-
adding  CC  to environment variables:  arm-cortex_a8-linux-gnueabihf-
./export_variable_forever.sh: line 9: /etc/environment: Permission denied
do you want to restart your computer to apply changes in /etc/environment file? yes(y)no(n)
yes
...
```
16. for view markdown(.md) file in preview in VSCode
```
CTRL+SHIFT+V
```


