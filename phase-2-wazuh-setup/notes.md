# Fase 2: Instalación del Wazuh Manager

Download the virtual appliance (OVA).
https://documentation.wazuh.com/current/installation-guide/wazuh-server/step-by-step.html
at update && apt upgrade -y 
Install the following packages if missing.
apt-get install gnupg apt-transport-https
apt-get install curl <--- primero
Install the GPG key.
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
Add the repository.
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
Update the packages information
apt-get update
Install the Wazuh manager package
apt-get -y install wazuh-manager
Install the Filebeat package.
apt-get -y install filebeat
Download the preconfigured Filebeat configuration file.
curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.14/tpl/wazuh/filebeat/filebeat.yml
Edit the /etc/filebeat/filebeat.yml configuration file and replace the following value:
hosts: The list of Wazuh indexer nodes to connect to. You can use either IP addresses or hostnames. By default, the host is set to localhost hosts: ["192.168.1.100:9200"]. Replace your Wazuh indexer IP address accordingly.
Create a Filebeat keystore to securely store authentication credentials.
filebeat keystore create
Add the default username and password admin:admin to the secrets keystore.
echo admin | filebeat keystore add username --stdin --force
echo admin | filebeat keystore add password --stdin --force
Download the alerts template for the Wazuh indexer.


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

# Instalación de dependencias y Docker, instalamos docker porque el instalador de wazuh lo va a necesitar para contener cada parte de wazuh aislada en contenedores 
apt install sudo curl gnupg lsb-release ca-certificates -y
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian bullseye stable" > /etc/apt/sources.list.d/docker.list
apt update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Habilitar y arrancar Docker
systemctl enable --now docker

# --------------------- esto en el shell principal proxmox ------------------------------
# Aplica el cambio
sysctl -w vm.max_map_count=262144
# Hazlo permanente
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
# Verifica el cambio
sysctl vm.max_map_count
# --------------------- esto en el shell principal proxmox ------------------------------


# Preparamos todo para Wazuh para docker
git clone https://github.com/wazuh/wazuh-docker.git -b v4.9.2 # si no da fallo intentando bajar la 5 y luego no se monta bien 

cd ~/wazuh-docker/single-node
docker compose up -d