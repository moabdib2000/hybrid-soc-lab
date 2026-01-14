# Fase 2: Instalación de Wazuh
# --------------------- esto en el shell principal proxmox ------------------------------
# pulsamos proxmox y luego shell para entrar en el shell, esto hace falta porque wazuh lo necesita
---
cd /var/lib/vz/template/iso
wget https://packages.wazuh.com/4.x/vm/wazuh-4.14.1.ova
tar -xvf wazuh-4.14.1.ova

### le hemos dado la numeracion 200 a la id por ser la fase 2 
qm importdisk 200 wazuh-4.14.1-disk-1.vmdk local-lvm --format raw

# vale , vamos por pasos que va a ser muy facil 
![configuramos inicial de la maquina !!!](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/1.jpg)
![sin arranque en cd](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/2.jpg)
![sistema](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/3.jpg)
![discos](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/4.jpg)
![cpu](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/5.jpg)
![memoria](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/6.jpg)
![network](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/7.jpg)
![confirmar](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/8.jpg)
![despues en harware unnused disk pulsar esto](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/9.jpg)
![options boot order](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/10.jpg)

## Vale, ahora hacemos START en la VM , si pulsamos > console > noVNC arranca wazuh y queda esperando
> usuario: wazuh
> password: wazuh 

> ip a 
asi vemos la configuracion de red, en eth0 tendremos una direccion local 192.168.xxx.xxx
### si ahora cargamos en nuestro navegador ,ATENCION https , luego le damos a confiar etc etc 
usuario: admin
pass: admin

![wazuh ok](https://github.com/moabdib2000/hybrid-soc-lab/blob/main/phase-2-wazuh-setup/images/11.jpg)

## vamos a preparar una IP fija para wazuh, asi tendremos el sistema asentado


## ahora vamos a coger los datos para preparar el agente de w11
