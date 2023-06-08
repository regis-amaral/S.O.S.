Em uma máquina virtual com o Arch Linux alguns passos adicionais são necessários.

Instalar pacotes adicionais
```
sudo pacman -S qemu-guest-agent spice-vdagent
git clone https://aur.archlinux.org/xlayoutdisplay.git
cd xlayoutdisplay
makepkg -si
cd ..
rm xlayoutdisplay -Rf
```

Adicionar um gancho para atualizar a resolução das telas adicionando a linha abaixo no arquivo .config/lxsession/LXDE/autostart
```                       
@xlayoutdisplay -w 1
```

Em seguida ajustar a posição da tela principal e reiniciar a vm
