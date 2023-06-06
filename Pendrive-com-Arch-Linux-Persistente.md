Este passo-a-passo é uma adaptação e complemento ao tutorial criado por Chris Magyar.

Créditos: [Arch Linux USB](https://mags.zone/help/arch-usb.html)

Testado em 02/06/2023

***

Está página explica como instalar o Arch Linux em uma unidade flash USB. O resultado final é uma instalação persistente idêntica à de um disco rígido normal, juntamente com várias otimizações de desempenho destinadas a executar o Linux em mídia flash removível. É compatível com os modos de inicialização BIOS e UEFI.

Os únicos pacotes explicitamente instalados além de linux , linux-firmware e base são: efibootmgr , grub , iwd , polkit , sudo e vim

Serviços básicos todos gerenciados pelo systemd.

### Configurar o teclado
```
loadkeys br-abtn2
```
### Instalar sistema básico

Determine o nome do dispositivo USB de destino:

```
lsblk
```
Se preferir utilize o comando ```fdisk -l``` para visualizar os discos com mais detalhes.

No restante deste guia, o nome do dispositivo será referido como /dev/sdX, sendo necessário trocar X pela letra do seu dispositivo USB. No meu caso, o pendrive aparece como **/dev/sda**.

### Limpar o dispositivo (opcional)

Obs.: É bem opcional mesmo, porque demora bastante! Meu pendrive de 16Gb levou mais de 3 horas.

Use dd para escrever o USB com todos os zeros, apagando permanentemente todos os dados:

```
dd if=/dev/zero of= /dev/sdX status=progress && sync
```

### Partição
Crie uma partição BIOS de 10M, uma partição EFI de 500M e uma partição Linux com o espaço restante:
```
sgdisk -o -n 1:0:+10M -t 1:EF02 -n 2:0:+500M -t 2:EF00 -n 3:0:0 -t 3:8300 /dev/sdX
```
Use ```fdisk /dev/sdX -l``` para visualizar as partições criadas

### Formatar
Não formate o bloco /dev/sdX1 . Esta é a partição BIOS/MBR.

Formate a partição do sistema EFI de 500 MB com um sistema de arquivos FAT32:

```
mkfs.fat -F32 /dev/sdX2
```

Formate a partição Linux com um sistema de arquivos ext4:

```
mkfs.ext4 /dev/sdX3
```

### Montar
Monte a partição formatada em ext4 como o sistema de arquivos raiz:

```
 mkdir -p /mnt/usb
 mount /dev/sdX3 /mnt/usb
```

Monte a partição EFI formatada em FAT32 em /boot:

```
mkdir /mnt/usb/boot
mount /dev/sdX2 /mnt/usb/boot
```

### Pacstrap
Baixe e instale os pacotes básicos do Arch Linux:

```
pacstrap /mnt/usb linux linux-firmware base vim nano
```

### fstab
Gere um novo /etc/fstab usando UUIDs como identificadores de origem:

```
genfstab -U /mnt/usb > /mnt/usb/etc/fstab
```

### Configurar sistema básico
Salvo indicação em contrário, toda a configuração é feita dentro de um chroot. Chroot no novo sistema:

```
arch-chroot /mnt/usb
```

Saia do ambiente chroot quando terminar digitando exit .

### localidade
Use a conclusão de tabulação para descobrir as entradas apropriadas para região e cidade :

```
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
```

Gerar /etc/adjtime :

```
hwclock --systohc
```

Edite /etc/locale.gen e remova o comentário do idioma desejado (para Português do Brasil, remova o comentário pt_BR.UTF-8 UTF-8 ):

```
nano /etc/locale.gen
```

Gere as informações de localidade:

```
locale-gen
```

Defina a variável LANG em /etc/locale.conf:

```
echo LANG=pt_BR.UTF-8 > /etc/locale.conf
```

### Hostname
Crie um arquivo /etc/hostname contendo o nome do host desejado em uma única linha:

```
echo archlinux > /etc/hostname
```
Substitua archlinux pelo nome que quer usar para o PC.

Edite /etc/hosts para conter apenas o seguinte conteúdo:

```
vim /etc/hosts
```
```
127.0.0.1  localhost
::1        localhost
127.0.1.1  archlinux.localdomain  archlinux
```

### Pasword
Defina a senha do root:

```
passwd
```

### Bootloader
Instale o grub e o efibootmgr :

```
pacman -S grub efibootmgr
```

Instale o GRUB para os modos de inicialização BIOS e UEFI:

```
grub-install --target=i386-pc --recheck /dev/sdX
grub-install --target=x86_64-efi --efi-directory /boot --recheck --removable
```

Gere uma configuração GRUB:

```
grub-mkconfig -o /boot/grub/grub.cfg
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
```

### Instale e ative o iwd para permitir o controle do usuário sobre as interfaces sem fio:

```
pacman -S iwd
systemctl enable iwd.service
```

### Crie um arquivo de configuração networkd com o seguinte conteúdo para conexões sem fio:

```
vim /etc/systemd/network/20-wifi.network
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

NOTA: este comando deve ser executado fora do ambiente chroot. Crie um link para /run/systmed/resolve/stub-resolv.conf :

```
ln -sf /run/systemd/resolve/stub-resolv.conf /mnt/usb/etc/resolv.conf
```

### Habilitar timesyncd:

```
systemctl enable systemd-timesyncd.service
```

### Usuário
Crie uma nova conta de usuário e defina sua senha, substitua "user" por um nome de usuário de sua escolha.

```
useradd -m user
passwd user
```

Certifique-se de que o grupo wheel exista e adicione o usuário a ele:

```
groupadd wheel
usermod -aG wheel user
```
Substitua "user" pelo nome de usuário que escolheu.

### sudo
Instale sudo:

```
pacman -S sudo
```

Ative o sudo para o grupo sudo criando uma regra em /etc/sudoers.d/ :

```
EDITOR=nano visudo /etc/sudoers.d/10-sudo
```
```
%sudo ALL=(ALL) ALL
```

Verifique se o grupo sudo existe e adicione o usuário a ele:

```
groupadd sudo
usermod -aG sudo user
```
Substitua "user" pelo nome de usuário que escolheu.


Instale o polkit para permitir que vários comandos ( reboot e shutdown , entre outros) sejam executados por usuários não root:

```
pacman -S polkit
```

### Noatime (opcional)
Diminua o excesso de gravações no USB garantindo que seus sistemas de arquivos sejam montados com a opção noatime . Abra /etc/fstab em um editor e altere cada opção relatime ou atime para noatime :

```
nano /etc/fstab
```
```
# /dev/sdX3
UUID=uuid1  /      ext4  rw,noatime      0 1

# /dev/sdX2
UUID=uuid2  /boot  vfat  rw,noatime,...  0 2
```

### Diário (opcional)
Evite que o serviço de diário systemd grave no USB, configurando-o para usar RAM. Crie um arquivo de configuração drop-in com o seguinte conteúdo:

```
mkdir -p /etc/systemd/journald.conf.d
nano /etc/systemd/journald.conf.d/10-volatile.conf
```
```
[Journal]
Storage=volatile
SystemMaxUse=16M
RuntimeMaxUse=32M
```

### Mkinitcpio (opcional)
Certifique-se de que os módulos necessários estejam sempre incluídos na imagem initcpio. Remova a detecção automática de HOOKS em /etc/mkinitcpio.conf :

```
nano /etc/mkinitcpio.conf
```
```
...
HOOKS=(base udev modconf kms keyboard keymap consolefont block filesystems fsck)
...
```

Desative a geração de imagem de fallback (é idêntica à imagem padrão sem o gancho de detecção automática ). Remova o fallback de PRESETS em /etc/mkinitcpio.d/linux.preset:

```
nano /etc/mkinitcpio.d/linux.preset
```
```
...
PRESETS=('default')
...
```

Remova a imagem substituta existente:

```
rm /boot/initramfs-linux-fallback.img
```

Gere uma nova imagem initcpio:

```
mkinitcpio -P
```

Gere uma nova configuração do GRUB:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

### Nomodeset (opcional)

Crie um item de menu GRUB com o parâmetro de kernel nomodeset . Use o vim para copiar a entrada de menu padrão de /boot/grub/grub.cfg para /etc/grub.d/40_custom e adicione nomodeset à sua linha de comando do kernel:

```
vim /etc/grub.d/40_custom
```
```
...
menuentry 'Arch Linux ... (nomodeset)' ...
...
linux /vmlinuz-linux ... nomodeset
...
```

Gere uma nova configuração do GRUB:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

### Microcódigo (opcional)
Habilitar atualizações de microcódigo. Instale amd-ucode e intel-ucode :

```
pacman -S amd-ucode intel-ucode
```

Adicione arquivos de imagem ucode a qualquer entrada personalizada desejada em /etc/grub.d/40_custom :

```
vim /etc/grub.d/40_custom
```
```
...
menuentry 'Arch Linux ...' ...
...
initrd /intel-ucode.img /amd-ucode.img ...
...
```

Gere uma nova configuração do GRUB:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

### Nomes de interface (opcional)
Assegure-se de que as principais interfaces ethernet e wi-fi sejam sempre denominadas eth0 e wlan0 . Reverta para a nomenclatura de dispositivo tradicional:

```
ln -s /dev/null /etc/udev/rules.d/80-net-setup-link.rules
```

### FIM!
Reinicie um computador dando boot pelo pendrive e a tela de login em modo texto irá aparecer.
Para instalar um ambiente grafico siga orientações da página do Arch Linux ou utilize minhas configurações abaixo para um ambiente básico e leve utilizando o lxde.

***

### Ambiente Gráfico LXDE

Instale seguindo este passo-a-passo: [Arch Linux Install LXDE Desktop](https://github.com/regis-amaral/S.O.S./wiki/Arch-Linux---Install-LXDE-Desktop)

### Resultado após instalação do ambiente gráfico e algumas personalizações

![Resultado após algumas personalizações](https://github.com/regis-amaral/S.O.S./blob/d8d3fb846393199391ef62f33b9fc7a567ca93f8/readme/Screenshot_archlinux_2023-06-02_15%3A29%3A48.png)