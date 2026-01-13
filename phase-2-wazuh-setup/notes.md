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
pct enter 100 <-- aqui entramos en la shell de wazuh sin claves ni nada, y como root .... 

apt update && apt upgrade -y
apt install curl gnupg -y

# Añadir repo Wazuh
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor | tee /usr/share/keyrings/wazuh.gpg > /dev/null

# Añadir repo
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list

# Instalar SOLO lo necesario
apt update 
apt install wazuh-manager -y

# Habilitar e iniciar el servicio
systemctl enable wazuh-manager
systemctl start wazuh-manager

# comprobamos el estado 
systemctl list-unit-files | grep wazuh
# nos debe salir algo como esto:
# wazuh-manager.service                  enabled         enabled

# dios mio, está enabled !!!! estupendo 


# Obtener token de autenticación
curl -k -u wazuh:wazuh -X POST https://localhost:55000/security/user/authenticate 2>/dev/null | python3 -m json.tool 2>/dev/null | grep token
# esto suelta un token de un monton de letras y numeros , es mio, asi que el tuyo será diferente 
"token": "XXXXXxxxxeyJhbGciOiJFUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ3YXp1aCIsImF1ZCI6IldhenVoIEFQSSBSRVNUIiwibmJmIjoxNzY3MDExOTQ3LCJleHAiOjE3NjcwMTI4NDcsInN1YiI6IndhenVoIiwicnVuX2FzIjpmYWxzZSwicmJhY19yb2xlcyI6WzFdLCJyYmFjX21vZGUiOiJ3aGl0ZSJ9.AfvO8zUfhhOBzOGbsgi13HGMbSVnMUWdIGytUonR9jXo4iyLDdwCRqB-TwK1I3vlm32pWcUFb2doaEMCGShH3EBzAA7yxw6k_9XcRtMOotgxiPeCmmu1XjhL7Mr5Zosu0o6d4tOW_8staaZS5a5JPUlTmGLxxEZULbngU0ox6UHRZ6lix"

# vale, lo guardamos en una variable
TOKEN="XXXXXxxxxeyJhbGciOiJFUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ3YXp1aCIsImF1ZCI6IldhenVoIEFQSSBSRVNUIiwibmJmIjoxNzY3MDExOTQ3LCJleHAiOjE3NjcwMTI4NDcsInN1YiI6IndhenVoIiwicnVuX2FzIjpmYWxzZSwicmJhY19yb2xlcyI6WzFdLCJyYmFjX21vZGUiOiJ3aGl0ZSJ9.AfvO8zUfhhOBzOGbsgi13HGMbSVnMUWdIGytUonR9jXo4iyLDdwCRqB-TwK1I3vlm32pWcUFb2doaEMCGShH3EBzAA7yxw6k_9XcRtMOotgxiPeCmmu1XjhL7Mr5Zosu0o6d4tOW_8staaZS5a5JPUlTmGLxxEZULbngU0ox6UHRZ6lix"

curl -k -H "Authorization: Bearer $TOKEN" https://localhost:55000/ 2>/dev/null | head -5
# asi venmos API funcionando correctamente

# Obtener IP del contenedor para acceso externo
ip a show eth0 | grep "inet " | awk '{print $2}' | cut -d/ -f1
# apunta la ip :D 

¡Perfecto! La API de Wazuh está funcionando correctamente y es accesible nuetsra red. 
Esto confirma que la instalación básica está completa.

Estado actual:
✅ Contenedor LXC con Debian 11
✅ Wazuh Manager 4.14.1 instalado
✅ API REST funcionando en https://192.168.1.xxx:55000
✅ Autenticación: usuario wazuh, contraseña wazuh
✅ Accesible desde tu red local (192.168.1.0/24)

Siguiente paso, vamos a ponerle una direccion IP estática 
Pulsamos sobre la maquina wazuh - Network - seleccionamos net 0 - editar - IPv4 estatica
IPv4/CIDR : 192.168.1.100/24
Gateway : 192.168.1.1

Pulsamos DNS
DNS domain : 192.168.1.1
DNS Servers : 8.8.8.8

![Configuracion red wazuh](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/1.jpg)

hacemos un 
> reboot

> pct enter 100
> ip a 

## Vamos a instalar el dashboard , la cara bonita :D 
> apt update
> apt install wazuh-dashboard
> nano /etc/wazuh-dashboard/opensearch_dashboards.yml
#### comprobamos que esto está activado
server.host: 0.0.0.0
opensearch.hosts: https://localhost:9200
opensearch.ssl.verificationMode: none
opensearch.username: "wazuh"
opensearch.password: "wazuh"
opensearch.requestHeadersAllowlist: ["securitytenant","Authorization"]
opensearch_security.multitenancy.enabled: false
opensearch_security.readonly_mode.roles: ["kibana_read_only"]
server.ssl.enabled: false
#server.ssl.key: "/etc/wazuh-dashboard/certs/dashboard-key.pem"
#server.ssl.certificate: "/etc/wazuh-dashboard/certs/dashboard.pem"
server.port: 5601                      
opensearch.ssl.certificateAuthorities: ["/etc/wazuh-dashboard/certs/root-ca.pem"]
uiSettings.overrides.defaultRoute: /app/wz-home
# Session expiration settings
opensearch_security.cookie.ttl: 900000
opensearch_security.session.ttl: 900000
opensearch_security.session.keepalive: true

### importante el espacio despues de username y password .... es muy puñetero :P , se me habia colgado el dashboard por esa tonteria!!! 

guardamos /etc/wazuh-dashboard/opensearch_dashboards.yml

> systemctl enable wazuh-dashboard
> systemctl start wazuh-dashboard
> systemctl status wazuh-dashboard

verificamos que esté en marcha
systemctl status wazuh-dashboard
● wazuh-dashboard.service - wazuh-dashboard
     Loaded: loaded (/etc/systemd/system/wazuh-dashboard.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2026-01-13 12:21:58 UTC; 128ms ago
   Main PID: 3023 (node)
      Tasks: 7 (limit: 9296)
     Memory: 19.4M
        CPU: 134ms
     CGroup: /system.slice/wazuh-dashboard.service
             └─3023 /usr/share/wazuh-dashboard/node/bin/node --no-warnings --max-http-header-size=65536 --unhandled-rejections=warn

pone running así que está funcionando 
