# Sandboxing Lunar Client
### **Please make sure, you've done all the steps to sandbox the official minecraft launcher before continuing with this tutorial!**
If you haven't, you can start [here](https://github.com/Mandlemankiller/Sandboxed-Minecraft/blob/main/minecraft-launcher.md).

## 1) Move the .lunarclient folder
```bash
sudo mv $HOME/.lunarclient/ /home/mineuser/.lunarclient/
```
Give the folder to mineuser
```bash
sudo chown -R mineuser:mineuser /home/mineuser/.lunarclient/
```
Change the permissions so you can edit the folder too
```bash
sudo chmod -R g+xrw /home/mineuser/.lunarclient/
```
Make a symlink
```bash
ln -s /home/mineuser/.lunarclient/ $HOME/.lunarclient
```
## 2) Download the AppImage
[Download the Lunar Client AppImage](https://www.lunarclient.com/download/) and save it to ```/home/mineuser/.lunarclient/```<br>
Make it executable
```bash
chmod a+x /home/mineuser/.lunarclient/Lunar\ Client-<version>.AppImage
```
### **Dont forget to replace \<version> with your Lunar Client version!**

## 3) Create a launch script
```bash
touch /home/mineuser/.lunarclient/launch.sh
```
Open it in some editor like [gedit](https://wiki.gnome.org/Apps/Gedit) or [nano](https://www.nano-editor.org/) and inside write:
```bash
#!/bin/bash/

# Enable mineuser's access to the display
xhost +si:localuser:mineuser

# Set the DISPLAY variable and start Lunar Client
su - mineuser bash -c "export DISPLAY=$DISPLAY && /home/mineuser/.lunarclient/Lunar\ Client-<version>.AppImage"

# Disable mineuser's access to the display
xhost -si:localuser:mineuser
```
### **Dont forget to replace \<version> with your Lunar Client version!**
Make it executable
```bash
chmod g+x /home/mineuser/.lunarclient/launch.sh
```

## 4) Start the Lunar Client!
If you've done everything correctly, this should start the Lunar Client!
```bash
/home/mineuser/.lunarclient/launch.sh
```
Done! :)