# Fase 0.5: Endpoint Windows y escenarios de phishing

## Objetivo
Crear el endpoint corporativo que simulará el puesto de trabajo de un usuario normal.  
Este será el objetivo principal de las simulaciones de phishing y otras actividades sospechosas.  
Además, se instalará el agente Wazuh para que envíe logs al futuro Wazuh Manager.

## Configuración de la VM en Proxmox
- **Nombre**: `win11-endpoint`
- **OS**: Windows 11 Enterprise Evaluation (180 días renovables)
- **ISO usada**: Windows 11 Enterprise Evaluation (versión 24H2 o la disponible en diciembre 2025)
- **Recursos asignados**:
  - vCPU: 2 núcleos
  - RAM: 3072 MB (3 GB)
  - Disco: 60 GB (VirtIO SCSI, thin provision)
  - Red: VirtIO, puente vmbr0
- **Opciones especiales**:
  - Machine: q35
  - BIOS: OVMF (UEFI)
  - EFI Disk añadido
  - VirtIO drivers instalados durante/post instalación

## Instalación de Windows 11
- Proceso estándar de instalación desde ISO.
- Idioma y región: Español.
- Particionado automático del disco.
- Cuenta local creada: `usuario` (contraseña simple para lab).
- Desactivadas la mayoría de opciones de telemetría y privacidad.
- Windows actualizado completamente tras la instalación.

## Instalación de drivers VirtIO
- ISO VirtIO descargada: `virtio-win.iso` (versión estable)
- Montada como CD en la VM.
- Ejecutado el instalador para drivers de red, storage, balloon, etc.
- Resultado: Mejor rendimiento de disco y red.

## Instalación del agente Wazuh
- Versión del agente: **Wazuh agent 4.8.2** (compatible con manager futuro)
- Instalación vía PowerShell (como administrador):
  ```powershell
  Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.8.2-1.msi -OutFile wazuh-agent.msi
  msiexec /i wazuh-agent.msi /q WAZUH_MANAGER="IP_FUTURA_DEL_MANAGER"
  NET start WazuhSvc


  -----------------------------------------------
# 1. Crear directorio de ISOs si no existe
mkdir -p /var/lib/vz/template/iso

# 2. Descargar Windows 11 Enterprise Evaluation (24H2) desde Microsoft
cd /var/lib/vz/template/iso

vamos a https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise
y seleccionamos la ISO de nuestro idioma que no sea LTSC
y copiamos la direccion de enlace

problema : como pasamos la ISO a proxmox ?¿? 
en local proxmox --- buscamos ISO images --- Download from URL - nombramos como "win11.iso"
ponemos en la URL el enlace copiado 
ponemos un mobre WIN11.iso o lo que quieras, eso ya se queda en las isos instalables
ahora  esperamos a que se descargue :D 

En la misma ventana de "ISO Images":
Click en "Download from URL" otra vez
URL: https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso
Nombre: virtio-win.iso
Click "Download"

# vale ahora vamos a preparar el VM, en el shell de promox metemos esto, se puede hacer en el modo grafico de proxmox, pero por aqui vamos a tardar menos 


# Paso 1: Crear VM con configuración base
qm create 120 \
  --name "win11-endpoint" \
  --ostype win11 \
  --cpu host \
  --cores 2 \
  --memory 3072 \
  --bios ovmf \
  --machine q35 \
  --agent 1 \
  --scsihw virtio-scsi-pci

# Paso 2: Añadir discos (LVM-Thin usa RAW por defecto)
qm set 120 --efidisk0 local-lvm:4,efitype=4m  # Disco EFI 4MB
qm set 120 --scsi0 local-lvm:60               # Disco principal 60GB (RAW implícito)
qm set 120 --ide2 local:iso/win11.iso,media=cdrom

# Paso 3: Configurar red y video
qm set 120 --net0 virtio,bridge=vmbr0
qm set 120 --vga qxl,memory=16
qm set 120 --serial0 socket

# Paso 4: Configurar arranque y identificadores
qm set 120 --boot order=ide2
qm set 120 --smbios1 uuid=e4528bf5-b00d-42f1-80c4-9b477248ddec
qm set 120 --vmgenid e19068fe-0634-4f7e-8bc3-e714e82b856e

# montamos las ISOs en nuestros CD-rom 😂  
qm set 120 --ide2 local:iso/win11.iso,media=cdrom
# esperar ---- qm set 120 --ide3 local:iso/virtio-win.iso,media=cdrom


# configurar desde la interfaz web:
Ve a la VM 120 en Proxmox web
Click en "Options"
Click en "Boot Order"
Click "Edit" movemos el orden pulsando en las 3 rallitas, ponemos primero CD, y luego SCSI, y lo ponemos en "enabled" ambas
Click en "Ok"


# -------_2026-01-11_---------
Inicia la VM con esta configuración. - pulsamos boton derecho START

Ve a >console y abre noVNC , si te salen una linea de comandos (a mi me salió como esperando un comando), pon reset y cuando vuelva a arrancar te saldrá que si quieres iniciar desde CD pulses intro en una cuenta atrás, dale y empezamos a instalar windows 11

Elegimos el idioma y le damos a siguiente. 

Por cierto, es mas comodo trabajar desde una ventana dedicada, >console > noVNC

![pantalla en proxmox](./images/ventana noVNC proxmox.jpg)


que con la pantallita de >console que hay en proxmox en el listado de summary de la maquina, al ser una pantalla muy pequeña podemos perdernos informacion, incluso no pulsarla correctamente..... pero está muy bien esa pantalla si luego tenemos un linux corriendo y queremos echar un vistazo rápido ... 

![pantalla en proxmox](./images/pantalla en proxmox.jpg)
