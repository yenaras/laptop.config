# Arch Linux Systemd Install
#### How to install Arch Linux with Systemd boot
---
The first thing you should do if you want to install 
Arch Linux is to make a bootable flash drive with a .iso file.  
You can download an iso file from [https://archlinux.org/download/](https://archlinux.org/download/).
Several tools are available to flash your live image, depending on your current operating system.
- [Rufus](https://rufus.ie/en/) if you are coming from Windows.
- [Balena-Etcher](https://www.balena.io/etcher/) should work for Windows, Mac, and Linux.
- [dd](https://wiki.archlinux.org/title/Dd) command-line tool should work for any Unix system, such as Linux or Mac.\
Replace *\*version\** with your arch iso file you downloaded and replace *sdx* with your flash drive.\
You can find the flash drive ID but typing ```lsblk``` with it unplugged and again with it plugged in.
```
dd if=~/Downloads/archlinux-*version*-x86_64.iso of=/dev/sdx status=progress
```
With your flash drive ready, you can boot to it from your BIOS or boot menu.
Each computer manufacturer will have a different key to get you to either BIOS or the boot menu.
You can find your way with a quick google search if you don't already know.
Once in the live image, pick the first option to start the Arch Linux install.

---
##### Connect to the Internet
---
If you connect through an ethernet cable, it should work automatically. 
To check if you have a connection to the internet, ping Google like this:
```
ping google.com
```
This checks for internet connection as well as domain resolution. Successful packet transfers mean you have an internet connection.
If it doesn't send and receive packets, you are not connected. If you are connected, you can stop the ping with ```ctrl+c```.
If you need to connect to your wifi, there are a few ways to do this. This guide will show you how to connect to wifi 
using ```iwctl``` from the ```iwd``` package. ```iwd``` is included in the installer. 
You can start IWD by typing IWCTL in the terminal.
```
iwctl
```
- Then, you need to find your wifi adapter name with:
```
device list
```
- The second column should be your adapter's name.  
From my experience, this has always been wlan0 in the live image.
- Then, you can start scanning for networks with:
```
station wlan0 scan  #replace wlan0 with your adapter.
```
- Now you can connect to your wifi with:
```
station wlan0 connect <SSID>   # replace <SSID> with your wifi name(no brackets)
```


Once you connect to the internet, you want to set up NTP time with:
```
timedatectl set-ntp true
```

---
##### Partition
---
We need to partition the hard drive that you will install Linux on.
This guide assumes you want to wipe your entire hard drive and replace it with Arch Linux, with systemd boot.
If you would like to partition a different way, check the [Installation Guide](https://wiki.archlinux.org/title/installation_guide)
from the ArchWiki.

---
- Check your current partitions with ```lsblk```.
- We can use a convenient graphical tool called ```cfdisk```.
```
cfdisk
```
- Delete all partitions listed.

- Make a new partition with 512M and make it EFI system type (top option under types)

- Make a second partition that will be your swap space. You can make this whatever you want, depending on your ram.
I have 8G of ram and made my swap space 16G because I don't need near the 2 TB of hard drive space that I have available.
You should change this type to Linux Swap, which should be just above the Linux Filesystem default option in the Type menu.

- Make a third partition using the rest of the hard drive. This should default to Linux Filesystem type. If not, change it to Linux Filesystem type.
The third partition will be your root partition.

- Format the root partition with ext4
```
mkfs.ext4 /dev/sda3
```
- Format the EFI partition to FAT32
```
mkfs.fat -F 32 /dev/sda1
```

- Mount the root partiton
```
mount /dev/sda3 /mnt
```
- Make the swap partition
```
mkswap /dev/sda2
swapon /dev/sda2
```
- Mount the EFI system
```
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

---
##### Pacstrap, fstab, and chroot
---
Use pacstrap to put some packages on the hard drive before chrooting into it.
I use neovim as my text editor, but you can use nano if you don't know how to use vim.
I highly recommend learning vim, because it can speed up your workflow a lot.  If you want to follow my set up, you should learn vim first.
Everything about my setup is designed to flow smoothly with vim-like keybinds.
```
pacstrap /mnt base base-devel linux linux-firmware neovim man-db man-pages texinfo
```
- Once this finishes, generate the fstab:
```
genfstab -U /mnt >> /mnt/etc/fstab
```
- Change root into your hard drive
```
arch-chroot /mnt
```
- Link your timezone.  I live in Indiana, so mine looks like this.  You can tab to complete the region.
```
ln -sf /usr/share/zoneinfo/America/Indiana/Indianapolis /etc/localtime
```
- Set the hardware clock
```
hwclock --systohc
```
- Edit /etc/locale.gen
- Uncomment the lines that contain your language. I will uncomment the en_US lines, so I will use nvim to search for them with /en_US, and use n to navigate down to them.
```
nvim /etc/locale.gen
```
- Generate locale from locale.gen
```
locale-gen
```
- Create the locale.conf file and export the LANG variable
```
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
```
- Configure hostname file.  You can make the hostname whatever you want, but I will use arch-pc for this.
```
echo arch-pc > /etc/hostname
```
- Configure hosts file.  You should use the same hostname you used in the hostname file.
```
nvim /etc/hosts
```
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch-pc
```
* Set root password
```
passwd
````
- Make your user.  **ATTN:** (**replace username with your username!**)
```
useradd -m -g users -G wheel,storage,power -s /bin/bash username
```
- Set the password for your username
```
passwd username
```
- Now you need to make your user a super-user(admin) and edit the visudo file.
You can uncomment the line in visudo that gives the wheel group root privileges.
```
EDITOR=nvim visudo
```
- In this file, you can type /wheel in neovim to search for the wheel group.
If you press enter to confirm your /wheel search, and then press ```n``` to go to the next find, 
you can uncomment this line to enable sudo privileges for the ```wheel``` group.
This line should read:
```
%wheel ALL=(ALL) ALL
```
---
#### Systemd-boot
---
Systemd-boot is a way of booting Unix systems. For more information, see [systemd-boot](https://wiki.archlinux.org/title/systemd-boot).
If you have a Legacy BIOS, you will not be able to boot this way, 
and should refer to the [Arch Linux Install Guide](https://wiki.archlinux.org/title/installation_guide).


- Install gdisk, which will let us change the flag for the EFI partition.
```
pacman -S gdisk
```

- Change the EFI partition flag to EF00
```
gdisk /dev/sda
t      <-- chose the type
1      <-- select the first partition
EF00   <-- make it this type
w      <-- write the change
y      <-- confirm the change
```

- Run bootctl installer
```
bootctl --path=/boot install

```
- Make the file /boot/loader/entries/arch.conf
```
touch /boot/loader/entries/arch.conf
```

- Append the PARTUUID
```
findmnt -fn -o PARTUUID /dev/sda3 >> /boot/loader/entries/arch.conf
```

- We need the PARTUUID of the root partition.
```
blkid /dev/sda3
```

- Make the arch.conf file in the boot loader
```
nvim /boot/loader/entries/arch.conf
```
- Write this in the file:
```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=PARTUUID=<part-hash-goes-here-without-the-angle-brackets> rw
```
- Make sure you used your root partitions PARTUUID in the above file, and don't forget the rw after it.

- Edit the loader.conf file
```
nvim /boot/loader/loader.conf
```
- Delete the comments, and put this in it:

```
default arch
timeout 4
console-mode max
editor no
```
- Install networkmanager and dhcpcd
```
pacman -S networkmanager dhcpcd
```
- Enable the daemons for networkmanager and dhcpcd
```
systemctl enable NetworkManager
systemctl enable dhcpcd
```
- Exit chroot and unmount the file system
```
exit
unmount -a
poweroff
```
- When the computer shuts down all the way, unplug the flash drive and turn the computer back on.
---
## Congratulations!

You just booted your Arch Linux PC with systemd-boot! Now you can choose what packages to install for the GUI you want.
This guide will focus on installing my Wayland Sway setup, but feel free to check the 
[Desktop environment](https://wiki.archlinux.org/title/desktop_environment) Arch Wiki page
for more options.

---
## What's next?
The next step I usually take is to remap my caps lock key, because I like using it for vim.
I think the caps lock key is not very useful, so I change it to be the ```esc``` key when pressed by itself,
and the ```ctrl``` key when pressed with another key.  This lets me easily use control keybinds and escape insert mode
without my hands leaving the home row of my keyboard. This is optional, and you can skip it if you want.

- [How to remap capslock in Wayland](remap_capslock.md)

If you don't want to remap caps lock, you can continue to the next page of my guide:
- [Setup](setup.md).
