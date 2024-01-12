Este é um passo-a-passo para a instalação mínima do Arch Linux, somente o sistema básico e poucos aplicativos úteis serão instalados, não é instalado um ambiente gráfico ou programas adicionais.

### Configurar o teclado
```
loadkeys br-abtn2
```

### Conectar a um Wifi Oculto (notebook somente)
```
iwctl
station wlan0 connect-hidden NomeDaRede
```
ou
```
iwctl --passphrase SENHA station wlan0 connect-hidden NomeDaRede
```

#### Sem conexão? Tente o seguinte...
```
systemd-resolve --set-dns=1.1.1.1 --interface wlan0
ip link set wlan0 down
ip link set wlan0 up
```

### Atualizar chaves e lista de pacotes
```
pacman-key init
pacman -S archlinux-keyring
pacman -Syy
```

### Atualizar o relógio do sistema
```
timedatectl set-timezone "America/Sao_Paulo"
timedatectl
```

### Particionar o Disco

Descubra o disco a ser utilizado usando o comando ```fdisk -l``` e o utilize no lugar de /dev/sdX.
Utilize o fdisk para criar as partições necessárias.

```
fdisk /dev/sdX
```

### Formatar partições

Partição de Boot 
```
mkfs.fat -F 32 /dev/efi_system_partition
```

Partição Linux do tipo BTRFS
```
mkfs.btrfs -L sistema /dev/linux_partition
```

Partição Swap
```
mkswap /dev/swap_partition
```

### Montar o Disco

```
mount -o compress=zstd /dev/linux_partition /mnt/

mount --mkdir /dev/efi_system_partition /mnt/boot

swapon /dev/swap_partition
```

### Install Essential Pack

```
pacstrap -K /mnt linux linux-firmware base vim nano dhcpcd net-tools
```

### FSTAB

```
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot
```
arch-chroot /mnt
```

### Timezone

```
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
```

### Location

Descomente alinha #pt_BR.UTF-8 no arquivo /etc/locale.gen

Em seguida execute os comandos:
```
locale-gen
echo LANG=pt_BR.UTF-8 > /etc/locale.conf
export LANG=pt_BR.UTF-8
echo KEYMAP=br-abnt2 > /etc/vconsole.conf
```

### Configurar o nome da máquina
```
echo arch > /etc/hostname
```

### Hostfile
```
nano /etc/hosts
```
```
127.0.0.1  localhost
::1        localhost
127.0.1.1  arch.localdomain  arch
```
### Rede
Crie um arquivo de configuração networkd com o seguinte conteúdo para estabelecer conexões com fio automaticamente:

```
nano /etc/systemd/network/10-ethernet.network
```
```
[Match]
Name=en*
Name=eth*

[Network]
DHCP=yes
IPv6PrivacyExtensions=yes

[DHCPv4]
RouteMetric=10

[IPv6AcceptRA]
RouteMetric=10
```

### Habilitar rede:

```
systemctl enable systemd-networkd.service
systemctl enable dhcpcd
```

### Instale e ative o iwd para permitir o controle do usuário sobre as interfaces sem fio:

```
pacman -S iwd
systemctl enable iwd.service
```

### Crie um arquivo de configuração networkd com o seguinte conteúdo para conexões sem fio:

```
nano /etc/systemd/network/20-wifi.network
```
```
[Match]
Name=wl*

[Network]
DHCP=yes
IPv6PrivacyExtensions=yes

[DHCPv4]
RouteMetric=20

[IPv6AcceptRA]
RouteMetric=20
```

### Ativar resolved.service:

```
systemctl enable systemd-resolved.service
```

### Habilitar timesyncd:

```
systemctl enable systemd-timesyncd.service
```

### Initramfs
```
mkinitcpio -P
```

### Root password
```
passwd
```

### GRUB
```
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### Adicionar usuário
```
pacman -S sudo
useradd -m regis
passwd regis
usermod -aG wheel,audio,video,storage regis
```
Execute ```EDITOR=nano visudo``` e descomente a linha #%wheel ALL=(ALL:ALL) ALL

### Finalizar
```
exit
umount /mnt/boot
umount /mnt
shutdown -r now
```

### Resultado após instalação básica (+ neofetch)

![Resultado após instalação básica](https://github.com/regis-amaral/S.O.S./blob/efb7128ced461300c447f19c2ed651771496e4d9/readme/Screenshot_ArchLinuxBasic_2023-06-03_17%3A21%3A11.png)


### [Opcional] Instalando gerenciador de janela Openbox e Gerenciador de login LightDM
```
pacman -S openbox ttf-freefont ttf-dejavu ttf-liberation lightdm lightdm-gtk-greeter
systemctl enable lightdm
```

### [Adicionais]

```
pacman -S --needed libpulse pavucontrol-qt networkmanager
systemctl enable NetworkManager
```


