01-08-2024 16:20
Status: #idea
Tags: [[Enumeration]]

# nmap

Nmap es una herramienta útil para realizar enumeración contra una red, o ip en particular. 

Aquí podemos ver los parámetros más comunes:

-**sP**: Nos permite hacer un barrido a toda la red especificando los 3 primeros bloques de la IP. Por ejemplo: nmap -sP 192.168.1.0/24
-**Pn** (no ping): No realiza ninguna técnica de descubrimiento. Pasa directamente al análisis de puertos.
-**sC**: Esta flag nos permite ejecutar los scripts más comunes de nmap, que nos dará información extra.
-**sL**: Solo lista los equipos. No envía ningún paquete a los objetivos.
-**PS**: Envía un SYN, por defecto al puerto 80, o puerto especificado.
-**PA**: Envía un ACK vacío, por defecto al puerto 80, o puerto especificado.
-**PU**: Envía un UDP vacío, por defecto al puerto 80, o puerto especificado.
-**sS**: Envía un SYN. Es la técnica usada por defecto. Rápida, fiable y relativamente sigilosa. También llamada half-open scan.
-**sX**: XMAS Scan, Envía TCP con los flags FIN, PSH y URG a 1.
-**sF**: Envía TCP con el flag FIN a 1.
-**sN**: Envía TCP con todos los flags a 0.
-**sV**: Interroga al conjunto de puertos abiertos detectados para tratar de descubrir servicios y versiones de estos servicios en puertos abiertos.
-**sT**: Esta flag nos permite hacer un escaneo de puertos TCP.
-**sU**: Esta flag nos permite hacer un escaneo de los puertos UDP.
**--all-ports**: Incluye todos los puertos en la fase de detección de versiones. Por defecto se excluyen algunos.
-**pXXX,XXX,XXX**: Escanea los puertos que se le pase, por ejemplo -p135,80,443.
-**n**: Hace que la DNS no se resuelva del equipo que se escanea.
**-p-**: Escanea todos los puertos, no solo los más comunes.
-**T{{1-5}}**: Agresividad con la que se escaneará la red, siendo 1 la más baja, y 5 la más alta.
-**iL ip.txt**: Nos permite pasarle un archivo de texto, por ejemplo, ip.txt para que nmap haga escaneos con cada IP que aparezca en el archivo.
-**O**: OS discovery, nos ayuda a saber que sistema operativo está usando el equipo.
-**A**: Esta flag es la combinación de -sV -O y -sC, es una opción bastante agresiva, y no se debe usar si no quieres llamar la atención.
-**stats-every 10s**: Esta flag nos permite saber el progreso del escaneo, cada 10s, los segundos son configurables.

## Vulnerabilidades:

Con nmap podremos buscar vulnerabilidades con la opción --script vuln -pXXXX, siendo un comando completo con esta flag así:

**==nmap 192.168.18.239 --script vuln -p445==**

Este comando buscaría vulnerabilidades, dándonos los CVEs referentes al puerto 445, en la máquina con IP 192.168.18.239. 

Esta flag es compatible con cualquier de las flags que se han mencionado arriba.

Con estos CVEs podríamos ir a [[Metasploit]] y buscar por CVE.


## Comando base:

nmap -sC -sV -Pn -n -p- --open $IP: Escaneo moi rápido.







---
# {{References}}

CSIRTCV MANUAL NMAP: https://www.csirtcv.gva.es/wp-content/uploads/2020/05/NMAP-6_-Listado-de-comandos.pdf