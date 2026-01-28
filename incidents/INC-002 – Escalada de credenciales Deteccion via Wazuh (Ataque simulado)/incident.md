# El objetivo NO es comprometer, sino generar señales claras y detectables en Wazuh.

## Escenario del ataque (resumen) 
Atacante: Kali Linux  
Víctima: Windows 11 con agente Wazuh  
Detección: Wazuh  
Técnica: PowerShell sospechoso + intento de credential access  

> MITRE esperado:  
    T1059 – Command and Scripting Interpreter  
    T1082 – System Information Discovery  
    (posible) T1003 – Credential Access (aunque falle)  

### FASE 1 — Reconocimiento básico desde Kali (ruido bajo)
#### localizamos nuestra ip en kali y comprobamos conexion a la red
>ip a
#### la ip de mi windows es 120
>nmap -Pn 192.168.1.120

````va a tardar un ratito :D   

La opción -Pn en Nmap se utiliza para omitir la etapa de descubrimiento de hosts (ping) antes de realizar el escaneo de puertos.   

¿Qué hace exactamente?  
Por defecto, Nmap intenta verificar si un objetivo está "vivo" enviando paquetes ICMP, TCP ACK y otras sondas. Si el objetivo no responde a estas pruebas, Nmap asume que está caído y no lo escanea.
````

### FASE 2 — Preparar el payload (muy importante)
Vamos a usar PowerShell porque:  
Es realista, suele ser común en ataques , lo debería detectar wazuh y sobre todo no necesitaremos malware :D 

> nano recon.ps1  
> 
editamos este archico con el siguiente contenido ...

> Get-LocalUser  
> Get-Process  
> Get-WmiObject Win32_OperatingSystem  

grabamos el archivo , pero comentamos, que hace esto...   
Esto no roba nada, pero: 
Enumera usuarios  
Enumera procesos  
Usa WMI  
Todo esto es comportamiento sospechoso en contexto de ataque.  