# Install Arch Linux
## Install glow
- `git clone https://aur.archlinux.org/glow.git`
- `makepkg -si`
## Wifi connect
- you can use `iwdctl` or `wifi-menu`
## Partition
- use parted or fdisk
- after fdisk you should fix the filesystem for partition
- EFI partiton for 1GB fat32
- root partition ext4
- mount /dev/sda2 /mnt
- mount /dev/sda1 /mnt/boot/efi
## Packages 
- `reflector -c Germany --verbose --latest 5 --sort rate --save /etc/pacman.d/mirrorlist`
- `pacstrap /mnt/ base linux linux-firmware net-tools networkmanager openssh vim tmux`

## Create fstab
- `genfstab -U /mnt >> /mnt/etc/fstab`
-`cat /mnt/etc/fstab`

## Make Swap file
- `arch-chroot /mnt`
- `fallocate -l 2GB /swapfile`
- `chmod 600 /swapfile`
- `mkswap /swapfile`
- `swapon /swapfile`
- `vim /etc/fstab`
	- `/swapfile none swap defaults 0 0`

## Timezone & locale
**after chroot**
- `ln -sf /usr/share/zoneinfo/Asia/Tehran /etc/localtime`
- `hwclock --systohc`
- `timedatectl set-ntp true`
- `/etc/locale.gen`
	- uncomment `en_US.UTF-8 UTF-8` 
- `locale-gen`
- echo "LANG=en_us.UTF-8" >> /etc/locale.conf
- `hostnamectl set-hostname M3D-PC`
- hostnamectl
- vim `/etc/hosts`
	```
	127.0.0.1	localhost
	::1			localhost
	127.0.1.1	M3D-PC.localdomain	M3D-PC
	```
## Boot fix
- `pacman -S efibootmgr networkmanager network-manager-applet wireless_tools wpa_supplicant dialog os-prober mtools dosfstools base-devel linux-headers`
## Refind
- `refind-install --usedefault /dev/sda2 --alldrivers`
- `mkrlconf`
- vim `/boot/refind_linux.conf`
- `cd /boot/EFI/BOOT` 
- `vim refind.conf`
- find arch Linux
	- change **option root** to the root of your linux
### Systemd
- `bootctl install --esp-path=/boot/efi`
> if it bootctil install failed
> `efibootmgr --create --disk /dev/sdX --part Y --loader "\EFI\systemd\systemd-bootx64.efi" --label "Linux Boot Manager" --verbose`
- `bootctl update`
### Grub
- `pacman -S grub`
- `grub-install --efi--directory=/boot/efi`
- `grub-mkconfig -o /boot/grub/grub.cfg`
- `systemctl enable NetworkManager`
## Create User
- `useradd -mG wheel <username>`
- `passwd <username>`
- `visudo`
- `pacman -S openssh`
## End
- `exit`
- `umount -a`
