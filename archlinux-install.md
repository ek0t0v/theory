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
> - gnome-tweaks
> - nano 
> - git
> - docker
> - docker-compose
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
- `nano /etc/locale.gen` раскомментировать en_US.UTF-8 и ru_RU.UTF-8
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
- `timedatectl set-ntp 1`

## Активация сервисов

- `sudo systemctl enable docker`
- `sudo systemctl enable NetworkManager`
- `sudo systemctl enable gdm`

## yay

- `git clone https://aur.archlinux.org/yay-bin.git && cd yay-bin && makepkg -si`

## snap

- `yay -Ss snapd`
- `sudo systemctl enable snapd.socket`
- `sudo systemctl start snapd.socket`
- `sudo ln -s /var/lib/snapd/snap /snap`
- перезайти в систему

## Настройка принтера

- установить cups и сделать `systemctl enable cups`
- установить драйвер `yay -S epson-inkjet-printer-escpr`
- [настройка cups](https://rtfm.co.ua/arch-linux-cups-i-hplip-podklyuchenie-printera)
- сканирование - `sane-find-scanner` и `imagescan -L`, также нужно добавить `usb 0x04b8 0x1143` в `/etc/sane.d/epsonds.conf` и перезагрузиться

## Фикс кривых (отсутствующих) символов в терминале

- `yay -S ttf-symbola`

## .zshrc

```
autoload -U colors && colors

PROMPT='%B%F{white}%n@%m%f %F{green}%B%~%f ${vcs_info_msg_0_}%b'
RPROMPT='[%F{yellow}%?%f]'

# git
zstyle ':completion:*:*:git:*' script ~/.zsh/git-completion.bash
fpath=(~/.zsh/functions $fpath)
autoload -Uz compinit && compinit
autoload -Uz vcs_info
precmd() { vcs_info }
zstyle ':vcs_info:git:*' formats '%F{cyan}[%b]%f '
setopt PROMPT_SUBST

# autocomplete
autoload -U compinit
compinit
setopt completealiases # для алиасов

# разное
setopt HIST_IGNORE_DUPS # игнорирование повторяющихся строк в истории

# автозавершение с использованием клавиши-стрелки
zstyle ':completion:*' menu select

# цветной вывод команд
if [ -f /usr/bin/grc ]; then
 alias gcc="grc --colour=auto gcc"
 alias irclog="grc --colour=auto irclog"
 alias log="grc --colour=auto log"
 alias netstat="grc --colour=auto netstat"
 alias ping="grc --colour=auto ping"
 alias proftpd="grc --colour=auto proftpd"
 alias traceroute="grc --colour=auto traceroute"
fi
```

## Удаление лишнего софта

Вот что я удалял:

- gnome-books
- gnome-contacts
- gnome-font-viewer
- gnome-maps
- gnome-music
- gnome-photos
- gnome-software
- gnome-user-docs
- gnome-video-effects
- cheese
- gnome-weather
- epiphany
- gnome-boxes
- gnome-characters
- gnome-calculator
- totem

> Разобраться, как при установке избежать этих пакетов. Т.е. мне нужно ставить самый-самый базовый gnome, без этих программ.
