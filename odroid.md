* arch instalation instruction to get started https://archlinuxarm.org/platforms/armv8/amlogic/odroid-c2
* create user ```useradd -m  -s /bin/bash USER_NAME```
* change all passwords ```passwd```
* delete default user ```userdel --remove alarm```
* install software ```pacman -S vim apache mysql php php-apache```
* rename server ```/etc/hostname```
* configure IP tables. User atached script
* cut of root login ```vim /etc/ssh/sshd_config``` set ```PermitRootLogin no```



