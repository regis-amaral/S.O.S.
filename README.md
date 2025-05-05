**S.O.S - Strategies in Operating Systems**


1. [Ajustar monitores virtuais em VM Arch Linux](https://github.com/regis-amaral/S.O.S./blob/main/Ajustar-monitores-virtuais-em-VM-Arch-Linux.md)
2. [Arch Linux - Instalação Básica](https://github.com/regis-amaral/S.O.S./blob/main/Arch-Linux---Basic-Install.md)
3. [Arch Linux - Instalação e Configuração do Desktop](https://github.com/regis-amaral/S.O.S./blob/main/Arch-Linux---Install-LXDE-Desktop.md)
4. [Backup / Restauração das Conexões do DBeaver](https://github.com/regis-amaral/S.O.S./blob/main/Connections-Backup-On-DBeaver.md)
5. [Pendrive com Arch Linux Persistente](https://github.com/regis-amaral/S.O.S./blob/main/Pendrive-com-Arch-Linux-Persistente.md)
6. [Softwares Básicos](https://github.com/regis-amaral/S.O.S./blob/main/Basic-Software.md)
7. Decriptar senhas DBeaver:
   ```bash
   openssl aes-128-cbc -d \
   -K babb4a9f774ab853c96c2d653dfe544a \
   -iv 00000000000000000000000000000000 \
   -in /home/regis/.local/share/DBeaverData/workspace6/General/.dbeaver/credentials-config.json | \
   dd bs=1 skip=16 2>/dev/null
   ```
  
