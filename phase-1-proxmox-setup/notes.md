# Notas de configuración de Proxmox
# Fase 0: Instalación y configuración de Proxmox VE

Estructura que vamos a seguir en proxmox 

| Tipo | Componente                     | Sistema operativo base recomendado     | Notas                                      |
|------|--------------------------------|----------------------------------------|--------------------------------------------|
| VM   | Windows 10/11 Endpoint         | Windows 10/11 Evaluation (180 días)    | Obligatorio VM                             |
| VM   | Kali Linux                     | Kali Linux 2025.x                      | Para comodidad GUI                         |
| VM   | MITRE CALDERA                  | Ubuntu Server 24.04 LTS                | Server + agente en Windows                 |
| LXC  | Wazuh Manager                  | Debian 11            | Privilegiado + nested + Docker             |
| LXC  | DFIR-IRIS                      | Debian 11                              | Privilegiado + Docker                      |
| LXC  | Shuffle                        | Debian 11                              | Privilegiado + Docker                      |
| LXC  | OpenCTI o MISP (fase 3)        | Debian 11                              | Privilegiado + Docker                      |



## Decisión de versión
- Hardware: Mac Mini Late 2016 (Intel Core i5/i7 Skylake, 8 GB RAM, 512 GB SSD).
- Versión elegida: **Proxmox VE 7.4-3**.
- Razón: Máxima estabilidad reportada en Mac Mini Intel pre-T2. Evita posibles problemas menores con kernels más nuevos (Bluetooth freeze, GPU). Aunque EOL en julio 2024, para laboratorio personal aislado es ideal.

## Descarga
- ISO: proxmox-ve_7.4-3.iso
- Fuente: https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso/proxmox-ve-7-4-iso-installer

## Preparación del USB bootable
- Herramienta usada: balenaEtcher en macOS / Tambien se puede usar Rufus en windows
- USB: 8 GB Kingston minimo

## Instalación
- Boot desde USB (tecla Option al encender en mac y elegimos el origen del arranque)
- Opciones seleccionadas:
  - Disco destino: SSD 512 GB
  - Filesystem: ZFS (RAID0)
  - IP estática: 192.168.1.111/24
  - Gateway: 192.168.1.1
  - DNS: 8.8.8.8

## Post-instalación
- Actualización completa:
  ```bash
  apt update && apt full-upgrade -y

## Nuevo usuario administrativo
Vamos a crear un usuario con privilegios administrativos pero que no sea root por defecto y tenga que escalar privilegios explícitamente cuando sea necesario.
primero instalamos sudo porque no está en proxmox, ya me ha dado el problema al crear el usuario ::smile::   
```bash 
    apt update & upgrade
    apt install sudo -y
    adduser nombredeusuario 
    usermod -aG sudo nombredeusuario
    echo "nombredeusuario ALL=(ALL:ALL) ALL" >> /etc/sudoers.d/nombredeusuario
    su nombreusuario #aqui metemos la clave nuestra nueva claro
    sudo apt install zsh git curl fonts-powerline -y 
  ```
# OPCIONAL Instalar Oh My Zsh (para temas y plugins) y luego autosugerencias 
  ```bash
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
    chsh -s $(which zsh) #cambia el shell por defecto a zsh
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
    git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
    git clone https://github.com/agkozak/zsh-z ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-z
    
  ```
# OPCIONAL Editar configuración
  ``` bash 
  nano ~/.zshrc
  ```
En la carpeta phase-0-proxmox-setup he dejado la configuracion de prueba para esto
oh_my_zsh_configuration.txt

