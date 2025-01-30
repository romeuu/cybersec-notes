01-08-2024 16:20
Status: #idea
Tags: [[Enumeration]], [[Host Discovery]]

- [[#Enumeración de hosts|Enumeración de hosts]]
	- [[#Enumeración de hosts#Bloqueo de peticiones ICMP Windows|Bloqueo de peticiones ICMP Windows]]
	- [[#Enumeración de hosts#Enumeración con TCP SYN|Enumeración con TCP SYN]]
		- [[#Enumeración con TCP SYN#Funcionamiento|Funcionamiento]]
		- [[#Enumeración con TCP SYN#Sintaxis básica|Sintaxis básica]]
	- [[#Enumeración de hosts#Comando recomendado para host discovery|Comando recomendado para host discovery]]
- [[#Escaneo de puertos|Escaneo de puertos]]
	- [[#Escaneo de puertos#Metodología de escaneo|Metodología de escaneo]]
	- [[#Escaneo de puertos#Versión de servicios y detección de OS|Versión de servicios y detección de OS]]
		- [[#Versión de servicios y detección de OS#Detección de versión de servicios|Detección de versión de servicios]]
		- [[#Versión de servicios y detección de OS#Detección de OS|Detección de OS]]
	- [[#Escaneo de puertos#Scripts|Scripts]]
	- [[#Escaneo de puertos#Combinado de OS, Servicios y Scripting|Combinado de OS, Servicios y Scripting]]
	- [[#Escaneo de puertos#Vulnerabilidades|Vulnerabilidades]]
	- [[#Escaneo de puertos#Comando base|Comando base]]
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
- **-PS21,22,25,80,445,3389,8080**: Hace uso de los paquetes SYN en los puertos correspondientes, ya que son puertos comunes para descubrir dispositivos tanto en Windows como en Linux.
- **-T4**: Nos permite ajustar la "agresividad" del escaneo, teniendo así más velocidad.
- **-PU137,138**: Para enumerar dispositivos que estén corriendo servicios en Windows como NetBIOS.

## Escaneo de puertos

Una vez que sepamos que nuestro host está online, podremos proceder a hacer un escaneo de puertos para ver que puertos están abiertos y que servicios están corriendo detrás de estos.

Cuando tratemos con objetivos que tengan Windows, una opción útil será la **-Pn**, que básicamente significa no ping, por lo tanto no hace host discovery, y simplemente haría el port scanning correspondiente.

Otras opciones interesantes, son:

- **-F**: Es la abreviatura de fast profile, que nos permitirá escanear los 100 puertos más comunes en vez de los 1000 por defecto. 
- **-p80,443**: Nos permite escanear los puertos que especifiquemos, en este caso, el 80 y 443.


> [!TIP] Puertos filtered
> Cuando un puerto sale en la columna de state a **filtered** en Windows, significa que hay un firewall restringiendo el acceso a este puerto, y nos está indicando que nmap no tiene claro si está abierto o cerrado.

### Metodología de escaneo

Una buena metodología de escaneo es crucial para conseguir los mejores resultados. Aquí voy a explicar los pasos a seguir para un escaneo correcto:

- **Empezar con un fast scan**: Es recomendado empezar con un fast scan para ver a lo que nos estamos enfrentando y ver los puertos más comunes que están abiertos. Por ejemplo, lo podríamos hacer de la siguiente manera:

````bash
nmap -Pn -sS -F 192.168.1.1
````

- Posteriormente, **escanear todos los puertos TCP** (65535): Esto lo podremos hacer con la opción **-p-**:

````bash
nmap -T4 -Pn -sS -p- 192.168.1.1
````

Se usa la flag **-sS**, que sirve para que se envíen paquetes SYN, si eres root se usará por defecto, y si no tendrás que especificarlo, pero es recomendable especificarlo siempre.

Si necesitamos precisión al 100%, y nos da igual que se nos detecte en la red, podremos usar la opción **-sT**, que buscará completar el 3 way handshake con un escaneo TCP. Generalmente, es más recomendado usar el escaneo SYN, con **-sS**.

También se pueden escanear **puertos UDP** con la flag **-sU**. Si vemos el estado open|filtered, podremos confirmar que estamos tratando con un sistema con firewall.

### Versión de servicios y detección de OS

#### Detección de versión de servicios

Por defecto, cuando se hace un escaneo de puertos contra un objetivo, ya se hace una mínima detección de servicios, que se puede ver en la columna service de un escaneo.

Pero, podemos hacer una detección de servicios más fuerte con la flag **-sV,** consiguiendo así una enumeración de estos dependiendo de los puertos en los que se estén corriendo.

Con esta flag, obtendremos resultados muchos más fiables, y podremos saber con más certeza que servicio está en cada puerto.

Para obtener resultados más fiables (pero con más agresividad), podríamos usar la flag **--version-intensity**, con un número del 1 al 10 después de **-sV**. Por ejemplo:

```bash
nmap -sS -sV --version-intensity 8 192.168.1.1 
```

#### Detección de OS

Para obtener el sistema operativo que está usando nuestro objetivo, usaremos la flag **-O**. Se pueden dar casos en el que nmap no sepa con certeza que OS se está usando, pero podemos ver la traza del TCP/IP, ya que se incluyen normalmente pistas.

Para hacer esta detección de manera más agresiva podremos usar la flag **--oscan-guess** después de -O. Esto hará que nmap intente determinar cual es el OS, mostrándonos un porcentaje de confianza. Por ejemplo:

```bash
nmap -sS -O --oscan-guess 192.168.1.1 
```

### Scripts

Nmap tiene una serie de scripts que podemos ejecutar que se encuentran en la carpeta **/usr/share/nmap/scripts**. 

Con la flag **-sC**, nmap nos permitirá ejecutar scripts relacionados a los servicios que el target esté usando.

Es importante hacer este **-sC** con un escaneo de puertos, ya que si no no funcionará.

```bash
nmap -sS -sV -sC -T4 192.168.1.1
```

Si queremos recibir ayuda sobre un script en concreto, podremos usar la flag
**--script-help=nombre_script**:

```bash
nmap --script-help=mongodb-databases
```

Para ejecutar un script en concreto, usaremos la flag **--script=nombre_script**, de la siguiente manera:

```bash
nmap -sS -sV --script=mongodb-databases -T4 192.168.1.1
```

Y también podremos indicar varios scripts seguidos de una coma, por ejemplo:

```bash
nmap -sS -sV --script=mongodb-databases,fpt-anon -T4 192.168.1.1
```

Si por ejemplo queremos usar todos los scripts de fpt, podríamos usar una wildcard de la siguiente manera:

```bash
nmap -sS -sV --script=fpt-* -T4 192.168.1.1
```

### Combinado de OS, Servicios y Scripting

Si queremos combinar todas estas opciones, existe la flag **-A**, que nos permitirá ejecutar todas esas flags, sin necesidad de escribirlas.

```bash
nmap -sS -A -T4 192.168.1.1
```

Cabe destacar que esta opción es pesada, si se quiere hacer poco ruido en la red no es recomendable.

### Vulnerabilidades

Con nmap podremos buscar vulnerabilidades con la opción --script vuln -pXXXX, siendo un comando completo con esta flag así:

**==nmap 192.168.18.239 --script vuln -p445==**

Este comando buscaría vulnerabilidades, dándonos los CVEs referentes al puerto 445, en la máquina con IP 192.168.18.239. 

Esta flag es compatible con cualquier de las flags que se han mencionado arriba.

Con estos CVEs podríamos ir a [[Metasploit]] y buscar por CVE.
### Comando base

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

ExplainShell: https://explainshell.com/explain?cmd=nmap+-sS