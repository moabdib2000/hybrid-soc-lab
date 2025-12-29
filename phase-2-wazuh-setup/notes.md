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

# abrimos shell en proxmox 
pct create 100 local:vztmpl/debian-11-standard_11.7-1_amd64.tar.zst \
  --hostname wazuh \
  --password wazuh123 \
  --storage local-lvm \
  --memory 2048 \
  --swap 512 \
  --cores 2 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp \
  --unprivileged 0 \
  --rootfs 20

# Iniciar
pct start 100
pct enter 100

apt update && apt upgrade -y
apt install curl gnupg -y

# Añadir repo Wazuh
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list

# Instalar SOLO lo necesario
apt update
apt install wazuh-manager wazuh-api -y

# Configurar contraseña API
cd /var/ossec/api/configuration/auth
node htpasswd -b -c user admin wazuh123

# Iniciar servicios
systemctl enable wazuh-manager wazuh-api
systemctl start wazuh-manager wazuh-api

