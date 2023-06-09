# Sandboxing the Official Minecraft Launcher
### **For this to work you must have minecraft installed from the [official .deb package from minecraft.net](https://www.minecraft.net/en-us/download)**

In this tutorial the name "mineuser" is used for the user who then launches minecraft, but feel free to use any other name. However, if you decide to choose a different name, you will need to stick with it throughout the entire process and replace all the occurences of "mineuser" in this tutorial with your custom user name.

## *0) Remove others' rights to read users' home directories*
```bash
sudo find /home/ -mindepth 1 -maxdepth 1 -exec chmod o-xwr {} +
```
*(This step is not necessary, but without this step, the process won't be as effective, because minecraft mods will still be able to read users' personal files and possibly upload them on the internet)*

## 1) Add a user for launching the game
When setting the password for the user, set something easy, we will remove the password in the next step.
```bash
sudo adduser mineuser
```
Remove the password
```bash
sudo passwd -d mineuser
```
*(If you don't do this, you will be forced to enter the password every time you want to start minecraft)*

## 2) Hide the user in the login menu
This will hide mineuser in the login menu after starting the computer.<br>
Create a file in /var/lib/AccountsService/mineuser
```bash
sudo touch /var/lib/AccountsService/mineuser
```
Set mineuser to be a system account
```bash
sudo su -c "printf \"[User]\nSystemAccount=true\" > /var/lib/AccountsService/users/mineuser"
```
Restart accounts service
```bash
sudo systemctl restart accounts-daemon.service
```

## 3) Add yourself to the mineuser's group
This step will allow you to edit files in the future .minecraft folder
```bash
sudo usermod -a -G mineuser $USER
```

### **Log out and log back in to apply the group change!**

## 4) Check that you are in the group
After logging in, run
```bash
groups
```
 One of the items it prints out should be "mineuser". If it doesnt show up, restart your computer and check again.

## 5) Make a backup!
**This is the part of the tutorial where I ask you to do a backup of your .minecraft folder. If you mess some things up after this point, you can easilly lose all your minecraft saves!**

## 6) Move the .minecraft folder
```bash
sudo mv $HOME/.minecraft/ /home/mineuser/.minecraft/
```
Give the folder to mineuser
```bash
sudo chown -R mineuser:mineuser /home/mineuser/.minecraft/
```
Change the permissions so you Please make sure, you've done all the steps to sandbox the official minecraft launcher before continuing with this tutorial!can edit the folder too
```bash
sudo chmod -R g+xrw /home/mineuser/.minecraft/
```
Make a symlink
```bash
ln -s /home/mineuser/.minecraft/ $HOME/.minecraft/
```
*(This will make a shortcut to the .minecraft folder so you don't have to go to the mineusers home allways when you want to visit it)*

## 7) Create a launch script
```bash
touch /home/mineuser/.minecraft/launch.sh
```
Open it in some editor like [gedit](https://wiki.gnome.org/Apps/Gedit) or [nano](https://www.nano-editor.org/) and inside write:
```bash
#!/bin/bash/

# Enable mineuser's access to the display
xhost +si:localuser:mineuser

# Set the DISPLAY variable and start minecraft launcher
su - mineuser bash -c "export DISPLAY=$DISPLAY && minecraft-launcher"

# Disable mineuser's access to the display
xhost -si:localuser:mineuser
```
Make it executable
```bash
chmod g+x /home/mineuser/.minecraft/launch.sh
```

## 8) Start the minecraft launcher!
If you've done everything correctly, this should start the minecraft launcher!
```bash
/home/mineuser/.minecraft/launch.sh
```
If it works, you can close it for now.
Please make sure, you've done all the steps to sandbox the official minecraft launcher before continuing with this tutorial!
## 9) Edit the desktop shortcut
Open ```/usr/share/applications/minecraft-launcher.desktop``` in some editor like [gedit](https://wiki.gnome.org/Apps/Gedit) or [nano](https://www.nano-editor.org/).<br>
The inside should look something like this:
```
[Desktop Entry]
Type=Application
Version=1.0
Name=Minecraft Launcher
Comment=Official Minecraft Launcher
Exec=minecraft-launcher
Icon=minecraft-launcher
Terminal=false
Categories=Game;Application;
StartupWMClass=minecraft-launcher
```
Change ```Exec``` to ```/home/mineuser/.minecraft/launch.sh```<br>
So the file will end up looking something like this:
```
[Desktop Entry]
Type=Application
Version=1.0
Name=Minecraft Launcher
Comment=Official Minecraft Launcher
Exec=/home/mineuser/.minecraft/launch.sh
Icon=minecraft-launcher
Terminal=false
Categories=Game;Application;
StartupWMClass=minecraft-launcher
```
### **Log out and log back in to apply the change!**

## 10) Fix your game installations
You will need to fix any installations that have their game directory outside of .minecraft, as minecraft saves them in absolute paths.

Done! :)<br>If you want to sandbox Lunar Client too, you can continue [here](https://github.com/Mandlemankiller/Sandboxed-Minecraft/blob/main/lunar-client.md).