Em uma máquina virtual com o Arch Linux alguns passos adicionais são necessários quando se tem dois ou mais monitores.

Obs.: O problema do auto ajuste e exibição de uma vm em vários monitores śo acontece no lxde, substituí-lo pelo Lxqt ou outro ambiente desktop é uma boa alternativa.

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
