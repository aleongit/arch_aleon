# Arch Linux
https://archlinux.org/
https://wiki.archlinux.org/title/Installation_guide

## Notes
* Date: Nov 2022
* System: VMware Workstation 16 Player
* Hardware: 4 GB RAM / 20 GB HDD / 2 CPU
* Bios Mode: BIOS (not UEFI)

## Dowload ISO
* archlinux-2022.11.01-x86_64.iso

## Boot ISO
```
root@archiso ~ #
```

## Set the console keyboard layout
```
loadkeys es
```

## Verify the boot mode
```
ls /sys/firmware/efi/efivars
> No such file or directory -> Bios Mode (not UEFI)
```

## Connect to the internet
```
ip link
> ens33 (dhcp)
ip a
ping archlinux.org
```

## Update the system clock
```
timedatectl status
timedatectl set-ntp true
```

## Partition the disks
```
BIOS with MBR
[SWAP]	    /dev/swap_partition	Linux swap	    More than 512 MiB
/mnt	    /dev/root_partition	Linux	        Remainder of the device
```

```
fdisk -l
> /dev/sda  20 GB

fdisk /dev/sda
>
/dev/sda1   1G  Linux swap
/dev/sda2   19G Linux
```

## Format the partitions
```
mkswap /dev/sda1
mkfs.ext4 /dev/sda2
```

## Mount the file systems
```
swapon /dev/sda1
mount /dev/sda2 /mnt
```

## Install essential packages
```
pacstrap -K /mnt base linux linux-firmware
pacstrap -K /mnt sudo nano networkmanager
```

## Generate fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

## Change root into the new system
```
arch-chroot /mnt
```

## Set the time zone
```
ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
hwclock --systohc
```

## Set Local
```
pacman -S nano

nano /etc/locale.conf
LANG=en_US.UTF-8

nano /etc/vconsole.conf
KEYMAP=es
```

## Hostname and hosts
```
nano /etc/hostname
arch

nano /etc/hosts
127.0.0.1	localhost
```

## Root password
```
passwd
```

## Boot loader
```
pacman -S grub efibootmgr

grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

## Reboot
```
exit
umount -R /mnt
reboot
> Booting new Arch!
```

## Network (with networkmanager)
```
systemctl enable NetworkManager.service
systemctl start NetworkManager.service
ping google.com

nmcli connection
> Wired connection 1

nmcli connection modify 'Wired connection 1' ipv4.address 192.168.1.50/24
nmcli device reapply ens33
nmcli connection reload

nano /etc/NetworkManager/system-connections/Wired\ connection\ 1.nmconnection
[ipv4]
address1=192.168.1.50/24,192.168.1.1
dns=8.8.8.8
dns-search=
method=manual
```

## New user
```
useradd -m -G wheel -s /bin/bash aleon
passwd aleon

pacman -S sudo

nano /etc/sudoers
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL

exit
loggin as aleon

```

## Update system
```
sudo pacman -Syu
```

## SSH
```
sudo pacman -S openssh
sudo systemctl enable sshd
sudo systemctl start sshd
sudo systemctl status sshd
```

## Utils
```
pacman -S neofetch
```

## Installing X server, Desktop Environment and Display Manager
```
sudo pacman -S xorg xorg-server
sudo pacman -S gnome
sudo systemctl enable gdm.service
sudo systemctl start gdm.service
sudo pacman -S arc-gtk-theme
sudo pacman -S archlinux-wallpaper
sudo reboot
```

## VmWare Tools
```
sudo pacman -Su xf86-input-vmmouse xf86-video-vmware mesa gtk2 gtkmm
```