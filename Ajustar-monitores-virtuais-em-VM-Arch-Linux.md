Instalar 
```
sudo pacman -S spice-vdagent xf86-video-qxl
```

Ajustar xrandr

```
xrandr --output Virtual-1 --primary --mode 1366x768 --pos 1920x0 --rotate normal --output Virtual-2 --mode 1920x1080 --pos 0x0 --rotate normal --output Virtual-3 --off --output Virtual-4 --off
```

