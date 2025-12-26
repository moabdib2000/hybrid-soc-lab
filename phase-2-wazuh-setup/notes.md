# Fase 2: Instalación del Wazuh Manager

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
  - vCPU: 2 núcleos
  - RAM: 3072 MB (3 GB)
  - Swap: 1024 MB
  - Disco: 50 GB
- **Red**:
  - Bridge: vmbr0
  - IPv4 estática: `192.168.1.110/24` (ajustar según tu red)
  - Gateway: `192.168.1.1`
  - DNS: `8.8.8.8`
- **Opciones avanzadas**:
  - Privileged container: Sí
  - Nesting: Activado (necesario para Docker)

## Arrancamos el contenedor
Le damos al play cuando lo tengamos seleccionado, o con el boton secundario y el menu contextual START

luego cuando haya pasado un momento y vemos que ha arrancado pulsamos en >_ Console que tambien está por el menú contextual del boton secundario del raton 
arranca como y nos pide user "root" y password que hemos metido al crear el contenedor ...
nos abrirá un proxmox console, una ventana con una consola para trabajar ....

## Método de instalación elegido
- **All-in-One con Docker** (recomendado por Wazuh para laboratorios y entornos pequeños).
- Ventajas: instalación rápida, todo integrado (manager, indexer, dashboard), bajo mantenimiento.

## Pasos de instalación (ejecutados dentro del contenedor LXC)

```bash
# Actualización del sistema
apt update && apt upgrade -y

# aqui he visto que no funciona una leche, asi que hay que actualizar los dns
nano /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 1.1.1.1

# vuelvo a hacer y ahora va a toda leche 
apt update && apt upgrade -y

# Instalación de dependencias y Docker, instalamos docker porque el instalador de wazuh lo va a necesitar para contener cada parte de wazuh aislada en contenedores 
apt install sudo curl gnupg lsb-release ca-certificates -y
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian bullseye stable" > /etc/apt/sources.list.d/docker.list
apt update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Habilitar y arrancar Docker
systemctl enable --now docker

# Descarga de archivos oficiales de Wazuh - en este caso a fecha de hoy es la 4.7 porque es la mas estable en un debian 11
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.7/wazuh-install-files.yml
bash ./wazuh-install.sh -a <---- da fallo porque me dice que debian no esta entre los sistemas recomendados 

bash ./wazuh-install.sh -a -i # esto pone un ignore a las advertencias, venga tira !!!