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
- [[#Detección de firewalls y evasión de IDS|Detección de firewalls y evasión de IDS]]
- [[#Optimización de escaneos|Optimización de escaneos]]
- [[#Formatos de output|Formatos de output]]
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

Para hacer esta detección de manera más agresiva podremos usar la flag **--osscan-guess** después de -O. Esto hará que nmap intente determinar cual es el OS, mostrándonos un porcentaje de confianza. Por ejemplo:

```bash
nmap -sS -O --osscan-guess 192.168.1.1 
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

```bash
nmap 192.168.18.239 --script vuln -p445
```

Este comando buscaría vulnerabilidades, dándonos los CVEs referentes al puerto 445, en la máquina con IP 192.168.18.239. 

Esta flag es compatible con cualquier de las flags que se han mencionado arriba.

Con estos CVEs podríamos ir a [[Metasploit]] y buscar por CVE.
### Comando base

nmap -sC -sV -Pn -n -p- --open $IP: Escaneo rápido.

## Detección de firewalls y evasión de IDS

Una manera de detectar si el objetivo está usando un firewall, es viendo como reacciona a un port scan, y si muestra puertos como "filtered". Para esto podremos usar la flag **-sA**, que es un TCP ACK scan, y nos indicará si los puertos están cerrados, y cuales filtered.

Para intentar saltarnos barreras de IDS, podremos usar la flag **-f**, que nos permitirá fragmentar los paquetes en datos más pequeños para que estos sistemas no detecten mucho tráfico.

Podremos establecer también decoy IP, que nos permite hacer un "señuelo", es decir, que las IP que especifiquemos van a simular que están escaneando la red también. Consiguiendo así que el IDS reporte, por ejemplo, 5 escaneos desde IP únicas. Esto lo conseguiremos con la flag **-D**, y poniendo las IP con comas. Puedes usar una ip de la red interna, o una externa.

```bash
nmap -sS -D 192.168.1.1,192.168.1.2
```

También podremos modificar el largo de nuestros paquetes, con la opción **--data-length numero**, por ejemplo, --data-length 200, nos permitiría añadir 200 bytes de datos aleatorios al paquete para que no coincidan con patrones conocidos de escaneo.

## Optimización de escaneos

Para acelerar escaneos de nmap podemos usar las plantillas de timing (**-T**), con un valor de 0 a 5, siendo 0 paranoico (lento), y 5 insane (muy agresivo).

Podemos también incluir delays con **--scan-delay**, si queremos no llamar la atención.

También podemos establecer un **--host-timeout 5s**, que hará que cuando se esté haciendo el host discovery, si el objetivo no responde en 5 segundos pasa al siguiente.

## Formatos de output

Siempre que estemos trabajando contra un objetivo, necesitaremos guardar los resultados que nos aporta nmap, ya que si no tendremos que realizar el escaneo otra vez.

Hay cuatro tipos de output distintos, cada uno con su correspondiente flag:

- **-oN**: Formato normal, tal y como lo vemos en la terminal. Normalmente se guarda en un archivo .txt.
- **-oX**: Formato XML. Es especialmente útil si queremos importarlo posteriormente en Metasploit.
- **-oG**: Formato grepable que nos permitirá hacer queries contra el. Es útil si estás haciendo automatización, y quieres pasar el resultado a otra utilidad.

Todas estas flags esperan el nombre del fichero, por ejemplo, si queremos guardar en formato normal en un txt, haríamos esto:

```bash
nmap -Pn -oN nmap_normal.txt
```

Si queremos usar XML e importarlo posteriormente a Metasploit tendríamos que usar la flag **-oX**, y ejecutar el siguiente comando dentro de msfconsole, asegurándonos de que la base de datos de postgresql esté activa:

```bash
db_import nmap_xml.xml
```

Posteriormente podremos listar los resultados de los escaneos con estos comandos:

```bash
hosts -> Lista los hosts
services -> Lista los servicios de los hosts
```

Si después queremos hacer escaneos directamente con metasploit y que se guarden en la base de datos automáticamente podremos usar db_nmap:

```bash
db_nmap -sS -D 192.168.1.1,192.168.1.2
```
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


## Prácticas

### SMB Nmap Scripts

Se nos proporciona un laboratorio que nos pide que se haga reconocimiento sobre una máquina Windows. Además, se nos indica que posiblemente podemos extraer datos con el servicio SMB.

Lo primero será verificar que el host está activo, que podremos hacer de la siguiente manera:

```bash
nmap -sn 192.168.1.1
```

Posteriormente, realizaremos un escaneo base en todos los puertos TCP, usando la enumeración de versiones de servicios, un escaneo SYN, y la ejecución de scripts comunes con el siguiente comando:

```bash
nmap -sS -sV -sC -p- 192.168.1.1
```

Lo que nos proporcionará un resultado como este:

```text
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2025-02-01T09:32:59+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WIN-OMCNBKR66MN
|   NetBIOS_Domain_Name: WIN-OMCNBKR66MN
|   NetBIOS_Computer_Name: WIN-OMCNBKR66MN
|   DNS_Domain_Name: WIN-OMCNBKR66MN
|   DNS_Computer_Name: WIN-OMCNBKR66MN
|   Product_Version: 6.3.9600
|_  System_Time: 2025-02-01T09:32:51+00:00
| ssl-cert: Subject: commonName=WIN-OMCNBKR66MN
| Not valid before: 2025-01-31T09:29:15
|_Not valid after:  2025-08-02T09:29:15
5985/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49166/tcp open  msrpc              Microsoft Windows RPC
49180/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-02-01T09:32:55
|_  start_date: 2025-02-01T09:27:51
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WIN-OMCNBKR66MN
|   NetBIOS computer name: WIN-OMCNBKR66MN\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-02-01T09:32:51+00:00
| smb2-security-mode: 
|   3:0:2: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.83 seconds
```

Como vemos, con el servicio de SMB en el puerto 445 podemos extraer información del objetivo. Por lo tanto es interesante usar scripts relacionados con SMB que nmap nos proporciona.

Podremos listarlos en la siguiente ruta: **/usr/share/nmap/scripts**.

La práctica nos indica que podríamos extraer sesiones activas, shares, usuarios de Windows, dominios, servicios, etc. Para esto podremos usar el siguiente comando que ejecuta varios scripts relacionado con estos requerimientos:

```bash
nmap -p445 --script=smb-enum-sessions,smb-enum-shares,smb-enum-users,smb-enum-domains,smb-enum-services 192.168.1.1
```

Algunos de ellos, como el de sesiones puede fallar o no mostrar todo el output ya que se requiere una cuenta de acceso para enumerar, por lo tanto podríamos hacer lo siguiente con cada uno de ellos, o con todos juntos, ya que la práctica nos proporciona credenciales:

```bash
nmap -p445 --script=smb-enum-sessions --script-args=smbusername=administrator,smbpassword=smbserver_771 192.168.1.1
```

```bash
nmap -p445 --script=smb-enum-sessions,smb-enum-shares,smb-enum-users,smb-enum-domains,smb-enum-services --script-args=smbusername=administrator,smbpassword=smbserver_771 192.168.1.1
```

Esto nos dará un output parecido a este, donde podremos ver:

```text
PORT    STATE SERVICE
445/tcp open  microsoft-ds
| smb-enum-services: 
|   AmazonSSMAgent: 
|     display_name: Amazon SSM Agent
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_CONTINUE
|   DiagTrack: 
|     display_name: Diagnostics Tracking Service
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_CONTINUE
|   Ec2Config: 
|     display_name: Ec2Config
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_CONTINUE
|   MSDTC: 
|     display_name: Distributed Transaction Coordinator
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_PARAMCHANGE
|       SERVICE_CONTROL_INTERROGATE
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_STOP
|       SERVICE_CONTROL_CONTINUE
|   Spooler: 
|     display_name: Print Spooler
|     state: 
|       SERVICE_CONTINUE_PENDING
|       SERVICE_RUNNING
|       SERVICE_PAUSED
|       SERVICE_PAUSE_PENDING
|     type: 
|       SERVICE_TYPE_WIN32_OWN_PROCESS
|       SERVICE_TYPE_WIN32
|     controls_accepted: 
|       SERVICE_CONTROL_NETBINDADD
|       SERVICE_CONTROL_NETBINDENABLE
|       SERVICE_CONTROL_STOP
|_      SERVICE_CONTROL_CONTINUE

Host script results:
| smb-enum-shares: 
|   account_used: administrator
|   \\10.2.20.1\ADMIN$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Remote Admin
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.2.20.1\C: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.2.20.1\C$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.2.20.1\D$: 
|     Type: STYPE_DISKTREE_HIDDEN
|     Comment: Default share
|     Users: 0
|     Max Users: <unlimited>
|     Path: D:\
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.2.20.1\Documents: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Documents
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.2.20.1\Downloads: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Users\Administrator\Downloads
|     Anonymous access: <none>
|     Current user access: READ
|   \\10.2.20.1\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: Remote IPC
|     Users: 1
|     Max Users: <unlimited>
|     Path: 
|     Anonymous access: <none>
|     Current user access: READ/WRITE
|   \\10.2.20.1\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\Windows\system32\spool\drivers
|     Anonymous access: <none>
|_    Current user access: READ/WRITE
| smb-enum-users: 
|   WIN-OMCNBKR66MN\Administrator (RID: 500)
|     Description: Built-in account for administering the computer/domain
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\bob (RID: 1010)
|     Flags:       Password does not expire, Normal user account
|   WIN-OMCNBKR66MN\Guest (RID: 501)
|     Description: Built-in account for guest access to the computer/domain
|_    Flags:       Password does not expire, Password not required, Normal user account
| smb-enum-domains: 
|   Builtin
|     Groups: Access Control Assistance Operators, Administrators, Backup Operators, Certificate Service DCOM Access, Cryptographic Operators, Distributed COM Users, Event Log Readers, Guests, Hyper-V Administrators, IIS_IUSRS, Network Configuration Operators, Performance Log Users, Performance Monitor Users, Power Users, Print Operators, RDS Endpoint Servers, RDS Management Servers, RDS Remote Access Servers, Remote Desktop Users, Remote Management Users, Replicator, Users
|     Users: n/a
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|     Account lockout disabled
|   WIN-OMCNBKR66MN
|     Groups: WinRMRemoteWMIUsers__
|     Users: Administrator, bob, Guest
|     Creation time: 2013-08-22T14:47:57
|     Passwords: min length: n/a; min age: n/a days; max age: 42 days; history: n/a passwords
|     Properties: Complexity requirements exist
|_    Account lockout disabled
| smb-enum-sessions: 
|   Users logged in
|     WIN-OMCNBKR66MN\bob since 2025-02-01T09:29:26
|   Active SMB sessions
|_    ADMINISTRATOR is connected from \\10.10.41.3 for 1s, idle for 1s
```

Sabiendo que tenemos este acceso, podríamos usar también el script **smb-ls**, que nos permite listar los ficheros de las diferentes shares que enumeremos, por ejemplo:

```bash
nmap -p445 --script=smb-enum-shares,smb-ls --script-args=smbusername=administrator,smbpassword=smbserver_771 192.168.1.1
```

También scripts interesantes como **smb-server-stats**, que nos proporciona estadísticas del servidor, como cuantos bytes enviados, cuantos recividos, logins fallidos, errores de permisos, etc.



---
# {{References}}

CSIRTCV MANUAL NMAP: https://www.csirtcv.gva.es/wp-content/uploads/2020/05/NMAP-6_-Listado-de-comandos.pdf

ExplainShell: https://explainshell.com/explain?cmd=nmap+-sS