# Fase 2: Instalación del Wazuh Manager

# --------------------- esto en el shell principal proxmox ------------------------------
# pulsamos proxmox y luego shell para entrar en el shell, esto hace falta porque wazuh lo necesita
# Aplica el cambio
sysctl -w vm.max_map_count=262144
# Hazlo permanente
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
# Verifica el cambio
sysctl vm.max_map_count
# Ajustar swappiness para mejor rendimiento
echo "vm.swappiness=1" >> /etc/sysctl.conf


# Descargar la OVA (si no lo has hecho)
cd /var/lib/vz/template/iso/
wget https://packages.wazuh.com/4.x/vm/wazuh-4.14.1.ova

# Extraer la OVA (es un archivo TAR)
tar -xvf wazuh-4.14.1.ova

# Instalar herramientas de conversión si no están
apt update
apt install qemu-utils -y
# da un error vamos a ver si seguimos el proceso

# Convertir VMDK a qcow2 (más eficiente) --- atencion a la version, yo he descargado wazuh 4.14.1, revisad el nombre del archivo 
qemu-img convert -f vmdk -O qcow2 wazuh-4.14.1-disk-1.vmdk wazuh-4.14.1.qcow2

# Verificar la conversión
qemu-img info wazuh-4.14.1.qcow2

# Crear VM con ID 110 (ajusta según tus necesidades)
qm create 110 --name "Wazuh-4.14.1" --memory 4096 --cores 2 --net0 virtio,bridge=vmbr0

# Importar disco convertido
qm importdisk 110 wazuh-4.14.1.qcow2 local-lvm

# Configurar disco importado
qm set 110 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-110-disk-0

# Configurar boot
qm set 110 --boot c --bootdisk scsi0

# Configurar pantalla (importante para OVA)
qm set 110 --serial0 socket --vga serial0

# Configurar memoria ballooning (para optimizar RAM)
qm set 110 --balloon 2048

# Opcional: Añadir CD-ROM para cloud-init si necesitas
qm set 110 --ide2 local-lvm:cloudinit

# Ver configuración
qm config 110





# --------------------- esto en el shell principal proxmox ------------------------------






Download the virtual appliance (OVA).
https://packages.wazuh.com/4.x/vm/wazuh-4.14.1.ova



https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html
at update && apt upgrade -y 


## Objetivo
Desplegar el **Wazuh Manager** como componente central de detección de amenazas (SIEM / XDR).  
Este será el primer pilar defensivo del laboratorio y permitirá recibir logs y generar alertas desde los endpoints.

## Templates en Proxmox
en la parte izquierda, en nodos, aparece local (proxmox), si lo seleccionamos , luego podemos ver 2 cosas interesantes:

CT templates 
ISO Images

En CT Templates podemos cargar remotamente cual es la imagen del contenedor que vamos a usar , yo por ejemplo he elegido debian 11 para este laboratorio, por estabilidad y recursos

en ISO Images por ejemplo he cargado, "subido" una Iso de kali, la 2024-02 que tambien es estable para este sistema de recursos o laboratorio ....

de esta forma no hay que andar con usb, isos, descargar, etc, le decimos al proxmox que todos los contenedores van a usar debian 11 y arreando :D 


## Creamos un LXC, un contenedor en proxmox
es el icono que pone Create CT
asignamos nombre, contraseas y en las pestañas está el resto de lo que debemos ir configurando 

## Configuración del contenedor LXC en Proxmox
- **ID del contenedor**: 101
- **Hostname**: `wazuh-manager`
- **Template**: Debian 11 
- **Recursos asignados**:
  - vCPU: 3 núcleos
  - RAM: 3072 MB (3 GB)
  - Swap: 1024 MB
  - Disco: 50 GB
- **Red**:
  - Bridge: vmbr0
  - IPv4 estática: `192.168.1.100/24` (ajustar según tu red)
  - Gateway: `192.168.1.1`
  - DNS: `192.168.1.1` y `8.8.8.8`
- **Opciones avanzadas**:
  - Privileged container: Sí <---- revisar
  - Nesting: Activado (necesario para Docker)

## Arrancamos el contenedor
Le damos al play cuando lo tengamos seleccionado, o con el boton secundario y el menu contextual START

luego cuando haya pasado un momento y vemos que ha arrancado pulsamos en >_ Console estando seleccionado el contenedor de wazuh .... tambien está por el menú contextual del boton secundario del raton 
arranca como y nos pide user "root" y password que hemos metido al crear el contenedor ...
nos abrirá un proxmox console, una ventana con una consola para trabajar ....

## Método de instalación elegido
- **All-in-One con Docker** (recomendado por Wazuh para laboratorios y entornos pequeños).
- Ventajas: instalación rápida, todo integrado (manager, indexer, dashboard), bajo mantenimiento.

## Pasos de instalación (ejecutados dentro del contenedor LXC)

```bash
# Actualización del sistema
apt update && apt upgrade -y
apt install curl apt-transport-https gnupg2 -y

# Descargar y ejecutar script de instalación
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.7/config.yml

# EDITAR config.yml ANTES de ejecutar (IMPORTANTE para LXC)
nano config.yml

# BEGIN copiamos esto en config.yml y eliminamos lo que tuviera ----------------

nodes:
  indexer:
    - name: node-1
      ip: 127.0.0.1
  server:
    - name: wazuh-1
      ip: 127.0.0.1
  dashboard:
    - name: dashboard
      ip: 127.0.0.1

# REDUCIR memoria para tu hardware limitado:
wazuh_indexer_heap_size: 1g  # En lugar de 2g
wazuh_memory: 1g  # Para el manager

# END copiamos esto en config.yml y eliminamos lo que tuviera ----------------

#-i para ignorar que no somos el sistema de su agrado :D 
bash wazuh-install.sh --generate-config-files -i 
bash wazuh-install.sh --wazuh-indexer node-1 -i
bash wazuh-install.sh --start-cluster -i 
bash wazuh-install.sh --wazuh-server wazuh-1 -i 
bash wazuh-install.sh --wazuh-dashboard dashboard -i

# O en un solo comando (menos control):
bash wazuh-install.sh --all-in-one