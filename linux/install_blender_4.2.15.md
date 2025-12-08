# Установка blender 4.2.15 на ubuntu

1. cкачай архив blender-4.2.15-linux-x64.tar.xz с официального сайта [prevous_blender_versions](https://download.blender.org/release/) 

2. распаковать и переместить в opt:
```
tar -vxjf blender-4.2.15-linux-x64.tar.xz
```
```
sudo mv blender-4.2.15-linux-x64 /opt
sudo mv /opt/blender-4.2.15-linux-x64 blender_4.2.15 
```
3. создаем символическую ссылку:
```
sudo ln -s /opt/blender_4.2.15/blender /usr/local/bin/blender
```
4. создаем ярлык в меню приложений:
```  
sudo nano /usr/share/applications/blender.desktop
```
добавить содержимое:
``` 
[Desktop Entry]
Name=Blender
Comment=3D modeling, animation, rendering and post-production
Exec=/opt/blender_4.2.15/blender
Icon=/opt/blender_4.2.15/blender.svg
Terminal=false
Type=Application
Categories=Graphics;3DGraphics;
Keywords=3d;cg;modeling;animation;painting;sculpting;texturing;video;editing;vfx;
StartupNotify=false
```