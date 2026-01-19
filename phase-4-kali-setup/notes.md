# Fase 4: Setup Kali



## Objetivo
Instalacion de Kali 

## Configuración de la VM en Proxmox
Primero descargamos la ISO 
EN proxmox > local(proxmox) > iso images > download from url > direccion de la iso (yo en mi caso busqué la kali-linux-2024-2-installer-amd64.iso)

### ahora en la shell de proxmox metemos estos comandos
# Define las variables (ajustar según tus necesidades)

````
VMID=300  
NOMBRE="Kali-Linux-CLI"  
ISO_PATH="/var/lib/vz/template/iso/  kali-linux-2024-2-installer-amd64.iso"  
MEMORIA=1024  # 1GB RAM  
CORES=1  
DISCO="16G"     # 16GB disco (suficiente para Kali minimal)  
STORAGE="local-lvm"  # Ajusta a tu almacenamiento  
BRIDGE="vmbr0" 
````

##### Quiero comentaros que hace poco instalé una versión muy ligera de Kali en una raspberry zero 2w, claro que solo para trabajar con la terminal, a la raspberry luego le integré alimentación por USB mediante una propia caja que se vende,a la vista parece un USB un poco grandote, pero dentro lleva un pequeño monstruo, tiene un kali, con wifi, ssh, tailscale para acceso remoto, vamos una preciosidad para pentesting en el espacio de un USB


### Crear la VM con configuración básica
qm create 300 \
  --name "Kali-Linux-CLI" \
  --memory 1024 \
  --cores 1 \
  --net0 virtio,bridge=vmbr0 \
  --ostype l26

### AHORA añade el disco
qm set 300 --scsi0 local-lvm:16

### Añade la ISO al cdrom :D 
qm set 300 --ide2 local:iso/kali-linux-2024-2-installer-amd64.iso,media=cdrom

### Configura el resto
qm set 300 --boot order='scsi0;ide2;net0'
qm set 300 --onboot 1
qm set 300 --agent enabled=1

## · Arrancamos el servidor y a instalar kali :D 
![arranque kali]("https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-4-kali-setup/images/kali1.jpg")  

## Opcion 1 graphical install - esto es mucho más sencillo que windows11 - siguiente - siguiente - siguiente 

- Elegimos idioma, teclado, etc 
- El sistema carga los componentes basicos
- Creamos el usuario y la password
- Instalacion de disco, particion, todo perfecto ... 
- El sistema instala el sistema base 
- En la seleccion de programas dejamos todo por defecto  

![selección de programas]("https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-4-kali-setup/images/kali2.jpg")

- Instalacion de GRUB en unidad principal ? Sí, pero pulses en manual, elige la unidad sda
- Ahora hay que quitar el CD de arranque :D  

![quitamos el CD]("https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-4-kali-setup/images/kali3.jpg")  

- y reiniciamos  
 ![quitamos el CD]("https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-4-kali-setup/images/kali4.jpg")  

###  Ahora vamos a cambiar a una ip fija, asi podemos detectar en wahuz cuando estamos experimentando con kali 

sudo nano /etc/network/interfaces

````auto eth0
iface eth0 inet static
    address 192.168.1.30
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4   
    
````

sudo systemctl restart networking  
sudo ifdown eth0 && sudo ifup eth0

comprobamos con  

#### ip a 

todo correcto y funcionando con la nueva ip :D 
