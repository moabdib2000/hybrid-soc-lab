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

