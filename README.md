# This is a repo that holds my steps to install arch linux (dell XPS 15 9530) 

## prepare partitions
* The system is UEFI and has Windows on so the /boot is ready. Don't touch it.
* Create /, /opt, /home, swap

## Boot form flash
* Create the flash with ```dd if=/arch-dual.img on=/dev/sdX bs=4M; sync```
* Afther booting make shure that you have internet connection. ping google.com if not run dhcpcd

### Mount partitions
```bash
mount /dev/sdaX /mnt
mkdir /mnt/opt
mount /dev/sdaX /mnt/opt
mkdir /mnt/opt/home
mount /dev/sdaX /mnt/home
mount /dev/sdaX /mnt/boot
```
### Base install
create base system. (boot partition needs to be mounted)
```
pacstrap /mnt base base-devel
```
save disk mounting info
```
genfstab /mnt >> /mnt/etc/fstab
```
if required add swap partition
```
/dev/sdaX   none    swap    defaults  0 0
```

### Start new install
start the newly create install
```
arch-chroot /mnt
```
create root password 
```
passwd
```
initialize boot loader
```
bootctl install
```
set machine name
```
echo mir > /etc/hostname
```
exit 
```
exit
```
 
### unmount and reboot
unmount all partitions 
```bash  
umount /mnt/home
umount /mnt/opt
umount /mnt/boot
umount /mnt
reboot
```

### enable dhcpcd
afther rebooting log in as root with the set password and enable the dhcpcd service
```
systemctl enable dhcpcd
```
#### install vim 
```pacman -S vim```

### enable multilib repo for 32bit compatibility
Enable multilib repo for 32bit compatibility. Search for 32 in the file
```
vim /etc/pacman.conf
```

## handle video drivers
### install drivers
First, make it work in intel only mode. Install the intel driver and mesa.
```
pacman -S xf86-video-intel mesa-libgl lib32-mesa-libgl
```
### blacklist nouveau
make shure that nouveau wont load
```
echo blacklist nouveau >> /etc/modprobe.d/blacklist.conf
```

### instal xorg
```
pacman -S xorg-server xorg-server-utils xorg-xinit
```

### install gnome
```
pacman -S gnome
```
### test gnome
```
systemctl start gdm
```

## create user
### create user
```
useradd -m -G wheel -s /bin/bash ligasm
```
### enable sudo for him
edit /etc/sudoers amd uncomment the wheel ALL line

## enable gnome
```
systemctl enable gdm
```
### reboot

## Programs to be installed
```
pacman -S guake 
```

## Wifi
Nerwork manager needs to be instaled and enabled
```
sudo pacman -S networkmanager networkmanager-openvpn networkmanager-vpnc network-manager-applet
sudo systemctl enable NetworkManager
```

## touchpad
Install touchpad drivers and configure it in gnome.
```
sudo pacman -S xf86-input-synaptics
```

## google chrome
Chrom is not in repo needs to be recompiled. 
* Download the tar form https://aur.archlinux.org/packages/google-chrome/
* extract the bundle
* install deps sudo ```pacman -S libxss snappy ttf-liberation```
* run ```makepkg```
* install the package ```sudo pacman -U google-chrome-47.0.2526.106-1-x86_64.pkg.tar.xz```

## locale
add keyboards (gnome ui)
### add locale
* uncoment required locale in ```sudo vim /etc/locale.gen```
* run the locale generator ```sudo locale-gen```
* set the locale with ```localectl set-locale LANG=en_US.UTF-8```
* restart and set language in gnome controle center
