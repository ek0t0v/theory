# Установка archlinux

## Форматирование и разметка

- `dd if=/dev/zero of=/dev/sda bs=2M count=10`
- `parted /dev/sda`
    - `mklabel gpt`
    - `unit MiB`
    - `mkpart esp fat32 1 513`
    - `mkpart primary ext2 513 82433`
    - `mkpart primary ext2 82433 100%`
    - `set 1 boot on`

> Внимание - swap-раздел здесь не создается (т.к. лично мне без надобности).

- `mkfs.vfat -F32 /dev/sda1 -n ESP`
- `mkfs.ext4 /dev/sda2 -L home`
- `mkfs.ext4 /dev/sda3 -L root`

## Монтирование разделов

- `mount /dev/sda3 /mnt`
- `mkdir -p /mnt/boot/esp && mount /dev/sda1 /mnt/boot/esp`
- `mkdir -p /mnt/home && mount /dev/sda2 /mnt/home`

## Установка

- `pacstrap /mnt base{,-devel} linux{,-firmware} efibootmgr ...`

> Мой список пакетов для установки:
> 
> - intel-ucode
> - netctl
> - dialog
> - dhcpcd
> - net-tools
> - wpa_supplicant
> - wireless_tools
> - networkmanager
> - nvidia
> - gnome
> - nano 
> - git
> - docker
> - docker-compose
> - ntfs-3g
> - zsh
> - zsh-completions
> - chromium
> - gnome-passwordsafe
> - ttf-fira-sans
> - ttf-fira-code
> - ttf-fira-mono

- `genfstab -t PARTUUID -p /mnt >> /mnt/etc/fstab`
- `arch-chroot /mnt`
- `setfont UniCyr_8x16`
- `nano /etc/locale.gen` раскомментировать ru_RU.UTF-8
- `locale-gen`
- `export LANG=ru_RU.UTF-8`
- `cat > /etc/vconsole.conf << EOF
  FONT=UniCyr_8x16
  KEYMAP=us
  TOGGLE_KEYMAP=ru
  EOF`
- `echo "LANG=ru_RU.UTF-8" > /etc/locale.conf`
- `ln -s /usr/share/zoneinfo/Europe/Moscow /etc/localtime`
- `hwclock --systohc --utc`
- `echo "pc" > /etc/hostname`
- `pacman -S grub`
- `grub-install --target=x86_64-efi --efi-directory=/boot/esp --recheck /dev/sda`
- `grub-mkconfig -o /boot/grub/grub.cfg`
- `nano /etc/sudoers` раскомментировать %wheel ALL=(ALL) ALL
- `passwd` задать пароль для root-пользователя
- `useradd -m -G wheel {user}`
- `passwd {user}`
- `exit`
- `reboot`

## После перезагрузки

- `sudo -i`
- `HISTFILE=/dev/null`
- `wifi-menu`
- `pacman-key --init`
- `pacman-key --populate archlinux`
- `sudo gpasswd -a {user} docker`
- `sudo systemctl enable docker`
- `sudo systemctl start docker`
- `sudo systemctl enable NetworkManager`
- `sudo systemctl enable gdm`
- `timedatectl set-ntp 1`

## Разное

- `git clone https://aur.archlinux.org/yay-bin.git && cd yay-bin && makepkg -si`

## Snap

- `yay -Ss snapd`
- `sudo systemctl enable snapd.socket`
- `sudo systemctl start snapd.socket`
- `sudo ln -s /var/lib/snapd/snap /snap`
- перезайти в систему

## Принтер

- установить cups и сделать `systemctl enable cups`
- установить драйвер `yay -S epson-inkjet-printer-escpr`
- [настройка cups](https://rtfm.co.ua/arch-linux-cups-i-hplip-podklyuchenie-printera)
- сканирование - `sane-find-scanner` и `imagescan -L`, также нужно добавить `usb 0x04b8 0x1143` в `/etc/sane.d/epsonds.conf` и перезагрузиться
