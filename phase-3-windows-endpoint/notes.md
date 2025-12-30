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
en local proxmox --- buscamos ISO images --- Download from URL
ponemos en la URL el enlace copiado 
ponemos un mobre WIN11.iso o lo que quieras, eso ya se queda en las isos instalables
ahora  esperamos a que se descargue :D 

En la misma ventana de "ISO Images":
Click en "Download from URL" otra vez
URL: https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso
Nombre: virtio-win.iso
Click "Download"

# vale ahora vamos a preparar el VM, en el shell de promox metemos esto, se puede hacer en el modo grafico de proxmox, pero por aqui vamos a tardar menos 

VM_ID="120"
VM_NAME="win11-endpoint"
STORAGE="local-lvm"

# Crear VM (si no existe)
qm create $VM_ID --name "$VM_NAME" --memory 3072 --cores 2 --net0 virtio,bridge=vmbr0

# Configurar UEFI con formato raw
qm set $VM_ID --machine q35 --bios ovmf
qm set $VM_ID --efidisk0 $STORAGE:4,format=raw,efitype=4m

# Disco principal (qcow2 sí funciona aquí)
qm set $VM_ID --scsi0 local-lvm:60,format=raw

# montamos las ISOs en nuestros CD-rom 😂  
qm set 120 --ide2 local:iso/win11.iso,media=cdrom
qm set 120 --ide3 local:iso/virtio-win.iso,media=cdrom

# configurar desde la interfaz web:
Ve a la VM 120 en Proxmox web
Click en "Options"
Click en "Boot Order"
Click "Edit" movemos el orden pulsando en las 3 rallitas, ponemos primero CD, y luego SCSI, y lo ponemos en "enabled"
Click en "Ok"


# -------vamos por aqui ---------
Inicia la VM con esta configuración.
Instala Windows 11 desde el primer ISO.
Cuando Windows pida drivers de almacenamiento, monta el ISO de VirtIO (tercera opción) sin reiniciar y selecciona la carpeta de drivers adecuada.
Una vez finalizada la instalación, apaga la VM, desmonta el ISO de Windows 11 (o deshabilítalo en el orden de arranque) y reinicia para que arranque desde el disco duro.