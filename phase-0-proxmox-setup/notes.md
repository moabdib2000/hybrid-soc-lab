# Notas de configuración de Proxmox
# Fase 0: Instalación y configuración de Proxmox VE

Estructura que vamos a seguir en proxmox 

| Tipo | Componente                     | Sistema operativo base recomendado     | Notas                                      |
|------|--------------------------------|----------------------------------------|--------------------------------------------|
| VM   | Windows 10/11 Endpoint         | Windows 10/11 Evaluation (180 días)    | Obligatorio VM                             |
| VM   | Kali Linux                     | Kali Linux 2025.x                      | Para comodidad GUI                         |
| VM   | MITRE CALDERA                  | Ubuntu Server 24.04 LTS                | Server + agente en Windows                 |
| LXC  | Wazuh Manager                  | Debian 12 (template Proxmox)           | Privilegiado + nested + Docker             |
| LXC  | DFIR-IRIS                      | Debian 12                              | Privilegiado + Docker                      |
| LXC  | Shuffle                        | Debian 12                              | Privilegiado + Docker                      |
| LXC  | OpenCTI o MISP (fase 3)        | Debian 12                              | Privilegiado + Docker                      |



## Decisión de versión
- Hardware: Mac Mini Late 2016 (Intel Core i5/i7 Skylake, 8 GB RAM, 512 GB SSD).
- Versión elegida: **Proxmox VE 7.4-3**.
- Razón: Máxima estabilidad reportada en Mac Mini Intel pre-T2. Evita posibles problemas menores con kernels más nuevos (Bluetooth freeze, GPU). Aunque EOL en julio 2024, para laboratorio personal aislado es ideal.

## Descarga
- ISO: proxmox-ve_7.4-3.iso
- Fuente: https://enterprise.proxmox.com/iso/proxmox-ve_7.4-3.iso

## Preparación del USB bootable
- Herramienta usada: balenaEtcher en macOS.
- USB: 8 GB Kingston.

## Instalación
- Boot desde USB (tecla Option al encender).
- Opciones seleccionadas:
  - Disco destino: SSD 512 GB
  - Filesystem: ZFS (RAID0)
  - IP estática: 192.168.1.100/24
  - Gateway: 192.168.1.1
  - DNS: 8.8.8.8

## Post-instalación
- Actualización completa:
  ```bash
  apt update && apt full-upgrade -y



