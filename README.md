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
