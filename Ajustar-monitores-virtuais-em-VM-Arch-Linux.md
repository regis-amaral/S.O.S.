Em uma máquina virtual com o Arch Linux alguns passos adicionais são necessários.

Instalar pacotes adicionais
```
sudo pacman -S qemu-guest-agent spice-vdagent xlayoutdisplay
```

Adicionar um gancho para atualizar a resolução das telas adicionando a linha abaixo no arquivo .config/lxsession/LXDE/autostart
```                       
@xlayoutdisplay -w 1
```
