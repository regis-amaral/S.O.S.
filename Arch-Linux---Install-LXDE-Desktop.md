### Aplicações Desktop Básicas
```
sudo pacman -Syu
sudo pacman -S xorg xorg-setxkbmap 
sudo pacman -S networkmanager lxde lxinput nm-connection-editor network-manager-applet iwd
sudo systemctl enable NetworkManager
sudo pacman -S xfce4-power-manager htop base-devel git wget pciutils usbutils 
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
sudo reboot
```

### Layout br-abnt2

```
sudo nano /etc/profile.d/setxkbmap.sh
```
Adicionar o conteúdo: 
```
setxkbmap -layout br -variant abnt2
```


### Instalar o Chrome
```
git clone https://aur.archlinux.org/google-chrome.git
cd google-chrome
makepkg -si
cd ..
rm -R google-chrome
```

### Login Automático (OPCIONAL)

No arquivo sudo nano /etc/lightdm/lightdm.conf, alterar as linhas para:
```
autologin-guest=false
autologin-user=regis
autologin-user-timeout=0
```
Adicionar o usuário no autologin group:

```
sudo groupadd -r autologin
sudo gpasswd -a regis autologin
```


### Áudio
```
sudo pacman -S pulseaudio pavucontrol dmidecode 
```

### Fontes básicas

```
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
```

### Fim

```
reboot
```

### Resultado após a instalação do ambiente gráfico

![Resultado após instalação do ambiente gráfico](https://github.com/regis-amaral/S.O.S./blob/293f204940da128954d134e9597b0caae019bf40/readme/Screenshot_ArchLinuxDesktop_2023-06-03_17%3A28%3A18.png)
