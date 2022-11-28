# Arch Linux

<p align="center">
	<img src="assets/banner.png" alt="banner.png" width="200">
</p>

## Distribución del teclado
```bash
loadkeys es
```
## Conectarse a Internet

- Conexión inalámbrica
```bash
iwctl
[iwd] device list
[iwd] station wlan0 scan
[iwd] station wlan0 get-networks
[iwd] station wlan0 connect <AP Name>
[iwd] exit
```
**rfkill unblock all**

- Comprobar conexión a Internet
```bash
ping -c 4 archlinux.org
```

## Actualizar reloj del sistema
```bash
timedatectl set-ntp true
```

## Particionar disco
- EFI
```bash
cfdisk /dev/<disk>
	# Tabla de particiones
	# /dev/sda1        EFI        512M
	# /dev/sda2        /          100GB
	# /dev/sda3        SWAP       4GB
```

## Formatear particiones
- EFI
```bash
mkfs.vfat -F 32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3
```

## Montar sistema de archivos
- EFI
```bash
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

## Instalar paquete esenciales
```bash
pacstrap /mnt linux linux-firmware base base-devel
```

# Configuración del sistema
---
- FSTAB
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
- CHROOT
```bash
arch-chroot /mnt
```
- Zona horaria
```bash
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```
- Idioma del sistema
```bash
pacman -S nano
nano /etc/locale.gen
	# Descomentar las siguientes líneas
	# en_US.UTF-8 UTF-8
	# es_ES.UTF-8 UTF-8
locale-gen
```
- Seleccionar idioma del sistema
```bash
echo "LANG=es_ES.UTF-8" > /etc/locale.conf
echo "KEYMAP=es" > /etc/vconsole.conf
```
- Configurar la red
```bash
echo "arch" > /etc/hostname
nano /etc/hosts
	# 127.0.0.1        localhost
	# ::1              localhost
	# 127.0.1.1        arch.localhost arch
pacman -S networkmanager wpa_supplicant
systemctl enable NetworkManager
systemctl enable wpa_supplicant
```
- Contraseña de root
```bash
passwd
```
- Creación de usuario
```bash
useradd -m <User Name>
passwd <User Name>
usermod -aG wheel,storage,audio,video <User Name>
nano /etc/sudoers
	# Descomentar la línea 82 
```
- Instalar gestor de arranque
- EFI
```bash
pacman -S grub efibootmgr
grub-install --targe=x86_64-efi --efi-directory=/boot
echo "GRUB_DISABLE_OS_PROBER=false" >> /etc/default/grub
grub-mkconfig -o /boot/grub/grub.cfg
```
- Paquetes necesarios
```bash
pacman -S git zip unzip pulseaudio pipewire-pulse alsa-oss alsa-utils pipewire pavucontrol
```
- Finalizar instalación
```bash
exit
umount -R /mnt
shutdown now
# Quitar unidad de instalación (USB Booteable)
# Encender el equipo
```
