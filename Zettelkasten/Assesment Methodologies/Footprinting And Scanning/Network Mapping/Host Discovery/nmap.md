01-08-2024 16:20
Status: #idea
Tags: [[Enumeration]], [[Host Discovery]]

- [[#Enumeración de hosts|Enumeración de hosts]]
	- [[#Enumeración de hosts#Bloqueo de peticiones ICMP Windows|Bloqueo de peticiones ICMP Windows]]
	- [[#Enumeración de hosts#Enumeración con TCP SYN|Enumeración con TCP SYN]]
		- [[#Enumeración con TCP SYN#Funcionamiento|Funcionamiento]]
		- [[#Enumeración con TCP SYN#Sintaxis básica|Sintaxis básica]]
- [[#Vulnerabilidades:|Vulnerabilidades:]]
- [[#Comando base:|Comando base:]]
- [[#Documentación|Documentación]]

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
-**p-1-10000**: Escaneará los puertos del 1 al 10000.
-**n**: Hace que la DNS no se resuelva del equipo que se escanea.
**-p-**: Escanea todos los puertos, no solo los más comunes.
-**T{{0-5}}**: Agresividad con la que se escaneará la red, siendo 0 la más baja, y 5 la más alta.
-**iL ip.txt**: Nos permite pasarle un archivo de texto, por ejemplo, ip.txt para que nmap haga escaneos con cada IP que aparezca en el archivo.
-**O**: OS discovery, nos ayuda a saber que sistema operativo está usando el equipo.
-**A**: Esta flag es la combinación de -sV -O y -sC, es una opción bastante agresiva, y no se debe usar si no quieres llamar la atención.
-**stats-every 10s**: Esta flag nos permite saber el progreso del escaneo, cada 10s, los segundos son configurables.
-**F**: Escanea los 100 puertos más comunes en el target.
-**v**: Nos permite activar el modo verbose.
-**oN/-oX/-oS/-oG**: Nos permite guardar el resultado del escaneo en formato normal, xml, script kiddie, o grepable.
-**oA**: Guarda el escaneo en los 3 formatos principales al mismo tiempo.

## Enumeración de hosts

Para saber que ip tenemos tendremos que usar el comando:

```shell
ip a s
```

Posteriormente, sabremos que IP tenemos y podremos saber en que segmento nos encontramos dentro de nuestra red.

Se dará el caso de que queramos identificar equipos que están conectados a nuestra red, y esto lo podremos conseguir con el parámetro -sn para que no haga port scanning (se necesita sudo):

```shell
sudo nmap -sn 192.168.2.0/24
```

Esto nos dará una lista de hosts que se encuentran en nuestra red.

Esto también se puede realizar con la herramienta [[Netdiscover]].

### Bloqueo de peticiones ICMP Windows

Se puede dar el caso de que nmap nos diga que el host está caído, que usemos la opción -Pn. Esto se debe a que podemos estar tratando con un objetivo Windows, que bloquea las peticiones ICMP por defecto. Lo que podremos hacer es pasar directamente al port scanning:

```shell
nmap -Pn 192.168.1.1
```

Esto nos dará directamente los puertos de nuestro objetivo en caso de que esté activo.

> [!TIP] Escaneo de IP múltiples
> Si quieres escanear varias direcciones IP, puedes hacerlo poniendo un espacio entre las IP que quieres escanear:
> ````
> nmap 10.10.10.2 10.10.10.3
> ````
> O también podremos hacer uso de un fichero que tenga las IPs escritas dentro, una en cada línea, con el siguiente comando:
> ````
> nmap -iL targets.txt
> ````

### Enumeración con TCP SYN

La enumeración con paquetes TCP SYN scan o "half-open scan" es una forma rápida y eficiente de determinar qué puertos están abiertos en un sistema.

Es más sigilosa, ya que no intenta completar la conexión TCP, por lo tanto es más probable que sistemas de detección de intrusión (IDS) o firewalls no lo registren.

#### Funcionamiento

1. Nmap envía un paquete **SYN** (solicitud de conexión) al puerto de destino.
2. Si el puerto está abierto, el objetivo responde con un paquete **SYN-ACK** (confirmación de conexión).
3. En lugar de completar la conexión enviando un **ACK**, Nmap finaliza la conexión enviando un **RST** (reset). Esto evita que se establezca una conexión completa, lo que hace que el escaneo sea más sigiloso.

#### Sintaxis básica

````bash
nmap -PS 80,443 192.168.1.1
````

O especificando un rango de puertos:

````bash
nmap -PS0-1000 192.168.1.1
````

Si no se envían puertos en el parámetro, se escanearán los puertos más comunes.

> [!TIP] Si se está haciendo host discovery...
> Si estás realizando host discovery es mejor incluir la opción -sn, ya que no hará port scanning y nos dará más velocidad en el reconocimiento.
> ````bash
nmap -sn -PS 80,443 192.168.1.1

### Comando recomendado para host discovery

Con lo que hemos estado viendo, podremos crear un comando base para hacer host discovery, que quedaría de la siguiente manera:

````bash
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 192.168.1.1
````

Siendo las flags usadas las siguientes:

- **-sn**: Indica a nmap que no se haga port scanning.
- **-P21,22,25,80,445,3389,8080**: Hace uso de los paquetes SYN en los puertos correspondientes, ya que son puertos comunes para descubrir dispositivos tanto en Windows como en Linux.
- **-T4**: Nos permite ajustar la "agresividad" del escaneo, teniendo así más velocidad.
- **-PU137,138**: Para enumerar dispositivos que estén corriendo servicios en Windows como NetBIOS.

## Vulnerabilidades:

Con nmap podremos buscar vulnerabilidades con la opción --script vuln -pXXXX, siendo un comando completo con esta flag así:

**==nmap 192.168.18.239 --script vuln -p445==**

Este comando buscaría vulnerabilidades, dándonos los CVEs referentes al puerto 445, en la máquina con IP 192.168.18.239. 

Esta flag es compatible con cualquier de las flags que se han mencionado arriba.

Con estos CVEs podríamos ir a [[Metasploit]] y buscar por CVE.
## Comando base:

nmap -sC -sV -Pn -n -p- --open $IP: Escaneo rápido.

## Documentación

Nmap tiene muy buena documentación a la que se puede acceder usando el comando man:

````shell
man nmap
````

Una vez dentro de ella, podremos usar el comando / para buscar algo en concreto, por ejemplo, si queremos buscar que es la flag -sn:

````shell
/-sn
````

Para saltar resultados, usaremos la tecla N.

---
# {{References}}

CSIRTCV MANUAL NMAP: https://www.csirtcv.gva.es/wp-content/uploads/2020/05/NMAP-6_-Listado-de-comandos.pdf