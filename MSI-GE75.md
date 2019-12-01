# This is a repo that holds my steps to install arch linux (MSI GE75 9SG) 

## prepare partitions
* The system is UEFI and has Windows on so the /boot is ready. Don't touch it.
* Create /, /opt, /home, swap 

You can use `fdisk -l` to find out our partitions nad `cfdisk`. The besic resizing and partitioning can by done also form windows. 

## Boot form flash
* download latest arch linux distor and flash it on linux. Make suer that the USB works!
* Create the flash with `sudo dd bs=4M if=arch-linux.iso   of=/dev/sda status=progress && sync`
* Afther booting make shure that you have internet connection. ping google.com if not run dhcpcd

### Mount partitions
```bash
mount /dev/sdaX /mnt
mkdir /mnt/opt
mount /dev/sdaX /mnt/opt
mkdir /mnt/opt/home
mount /dev/sdaX /mnt/home
mkdir /mnt/boot
mount /dev/sdaX /mnt/boot
```
### Base install
create base system. (boot partition needs to be mounted)
```
pacstrap /mnt base base-devel linux-lts linux-lts-headers linux-firmware vim dhcpcd
```
add also the appropraite micro code `intel-ucode`

save disk mounting info
```
genfstab /mnt >> /mnt/etc/fstab
```
if required add swap partition
```
/dev/sdaX   none    swap    defaults  0 0
```
you can use also a file based swap
```
/swapfile		none		swap		sw				0 0
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
setup loader
* go to the /boot/loader/loader.conf. Adjust it to
```
default arch
timeout 3
```
* add the arch conf into the entries subfolder (name is important). Template can be found in /user/share/systemd/bootctl/arch.conf
* copy the arch file and adjust the PARTUUID. 
```
blkid -dno PARTUUID /dev/... >> /boot/loader/entries/arch.conf
```
* edit the arch.conf file. MAKE NO TYPO IN PARTUUID!
* arch conf should look like
```
title	Arch Linux
linux	/vmlinuz-linux-lts
initrd  /intel-ucode.img
initrd	/initramfs-linux-lts.img
options	root=PARTUUID=XXXXXXX	rw
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
passwd ligasm
```
### enable sudo for him
edit /etc/sudoers amd uncomment the wheel ALL line

## Programs to be installed
```
pacman -S guake gedit meld vlc git openssh multitail wget zip tree rsync scp
```

## Wifi
Nerwork manager needs to be instaled and enabled
```
sudo pacman -S networkmanager
sudo systemctl enable NetworkManager
```

## enable gnome
```
systemctl enable gdm
```
### reboot

## Programs to be installed
```
pacman -S guake gedit meld vlc skype git openssh multitail npm wget zip tree rsync scp
```
### make external ntfs work
```
pacman -S ntfs-3g 
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
* restart and set language in gnome controle center (select both options: language nad formats). If there are no unicode chars in console, reseltect in gnome and log out-log in.

## VirtualBOx
```
pacman -S virtualbox 
```

## MySQL
install maria DB
```
sudo pacman -S  mariadb mariadb-clients
```

## Postgre SQL
install
```
sudo pacman -S  postgresql
sudo passwd postgres
su - postgres
initdb --locale en_GB.UTF-8 -E UTF8 -D '/var/lib/postgres/data'
```
create user and db
```
createuser --interactive
createdb liferay
```

## Printer
```
sudo pacman -S  cups ghostscript  gsfonts  avahi hp-setup 
sudo systemctl start cups-browsed.service
sudo systemctl enable cups-browsed.service
hp-setup -i

```
