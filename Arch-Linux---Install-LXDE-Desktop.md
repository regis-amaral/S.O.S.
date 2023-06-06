### Aplicações Desktop Básicas
```
sudo pacman -S xorg xorg-setxkbmap networkmanager lxde lxdm lxinput nm-connection-editor network-manager-applet iwd
sudo pacman -S xfce4-power-manager htop base-devel git wget pciutils usbutils 
sudo systemctl enable NetworkManager
sudo systemctl enable lxdm
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

### Login Automático (opcional)

No arquivo /etc/lxdm/lxdm.conf descomentar e edite a linha para ```autologin=regis```

### Áudio
```
sudo pacman -S pulseaudio pavucontrol dmidecode 
```

### Fim

```
reboot
```

### Resultado após a instalação do ambiente gráfico

![Resultado após instalação do ambiente gráfico](https://github.com/regis-amaral/S.O.S./blob/293f204940da128954d134e9597b0caae019bf40/readme/Screenshot_ArchLinuxDesktop_2023-06-03_17%3A28%3A18.png)
