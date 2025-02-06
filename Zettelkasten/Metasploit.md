 05-08-2024 20:46
Status: #idea
Tags: [[Exploiting]]

# Metasploit

- [[#Uso:|Uso:]]
- [[#Base de datos y nmap|Base de datos y nmap]]
- [[#Módulos y escaneo de puertos|Módulos y escaneo de puertos]]
	- [[#Módulos y escaneo de puertos#Ejemplo práctico|Ejemplo práctico]]
- [[#Enumeración de servicios|Enumeración de servicios]]
	- [[#Enumeración de servicios#FTP|FTP]]
		- [[#FTP#Versión|Versión]]
		- [[#FTP#Login|Login]]
		- [[#FTP#Login anónimo|Login anónimo]]
	- [[#Enumeración de servicios#SMB|SMB]]
		- [[#SMB#Versión|Versión]]
		- [[#SMB#Usuarios|Usuarios]]
		- [[#SMB#Shares (volúmenes)|Shares (volúmenes)]]
		- [[#SMB#Bruteforcing (Login)|Bruteforcing (Login)]]
- [[#Variables globales|Variables globales]]
- [[#Busca de vulnerabilidades por CVE|Busca de vulnerabilidades por CVE]]


Metasploit es un **framework de código abierto** utilizado para desarrollar, probar y ejecutar **exploits** contra sistemas vulnerables. Es una herramienta fundamental en el ámbito de la **ciberseguridad**, específicamente en **pentesting** (pruebas de penetración). Permite a los profesionales de seguridad identificar, explotar y evaluar vulnerabilidades en redes y aplicaciones.

Los **componentes** de metasploit son:

- **Framework**: El núcleo de Metasploit.
- **Exploits**: código que aprovecha las vulnerabilidades en sistemas.
- **Payloads**: carga útil enviada al objetivo después de una explotación exitosa.
- **Módulos** auxiliares: herramientas adicionales para tareas específicas.

## Uso:

Para abrir metasploit usamos o comando: msfconsole.

Comandos básicos para metasploit:

- search "module_name": Busca un módulo que teña de nombre "module_name".
- use (0-1-2-3...): Seleccionamos o módulo que máis nos conveña, indicando o seu número.
- set "option" "value": Esto sírvenos para poñer un valor do RHOSTS, por ejemplo set RHOSTS 1.2.3.4.5
- run: Ejecutar o módulo.
- back: Permítenos salir do módulo no que estamos e volver ao inicio.
- options: Listar as opcións e parámetros que teña o noso módulo.

## Base de datos y nmap

Para arrancar la base de datos de metasploit, haremos primero un **==msfdb start==**, y un **==msfdb init==**.

Para arrancar nmap en metasploit, usaremos el comando **==db_nmap 192.168.18.0/24==**.

Posteriormente, podremos visualizar los resultados de nmap, con el comando **==services==** en metasploit, mostrándonos los servicios por IP.

![[Pasted image 20241016204333.png]]

## Módulos y escaneo de puertos

Para escanear puertos podremos usar módulos como el **scanner/portscan/tcp**. 

Este módulo nos permitirá verificar que puertos están abiertos, sin la necesidad de usar nmap.

Para encontrar el módulo usaremos la función search de metasploit, seguido de **scanner portscan**.

Si por ejemplo, queremos encontrar como sería un escaneo SYN, o TCP, usaremos el siguiente comando, y veremos que es la opción 4 o 5. 

```shell
search portscan
```

Con show options veremos las opciones que son requeridas, en el caso de este módulo, el obligatorio sería RHOSTS (set RHOSTS xxx).

Una vez tengamos finalizado el escaneo de puertos, podremos hacer esto: **==search port:445==**, y nos buscará vulnerabilidades que hagan referencia a este puerto.

### Ejemplo práctico

Por ejemplo, en una práctica para el EJPT se determina con un escaneo TCP que el objetivo tiene una aplicación de XODA en el puerto 80.

A partir de aquí, podríamos usar **search xoda**, para encontrar un exploit que podamos usar.

Posteriormente, solo tendremos que configurar las opciones como RHOST, etc, y usar el comando **exploit**. Si funciona el exploit, nos abrirá una shell meterpreter, que nos permitirá interactuar con el objetivo. A partir de aquí, podremos escribir **shell**, para que nos abra una shell y seguido de **/bin/bash -i**, para que nos abra un bash dentro de la máquina afectada. 

Con esto, podríamos ver la IP interna con **ifconfig**, y hacer pivoting con 
**autoroute -s IP_MAQUINA**, que es un **módulo de Metasploit** que permite enrutar tráfico a través de una sesión comprometida (por ejemplo, una sesión de Meterpreter). Es útil cuando se ha comprometido una máquina dentro de una red interna y se quiere pivotar hacia otras máquinas dentro de esa misma red.

Ahora si por ejemplo, sabemos que la red interna le tiene asignado como IP **192.168.1.3**, podríamos probar a hacer un escaneo de puertos en máquinas como **192.168.1.4**, y ver que nos devuelve.

Para poder escanear con total certeza esta nueva máquina, tendremos que subir el ejecutable de nmap, o algún script en bash, y esto lo conseguiremos desde meterpreter con el comando upload. [El ejecutable de nmap lo podremos crear](https://nmap.org/book/inst-source.html), y un script en bash sencillo para escanear los 1000 primeros puertos sería este:

```shell
#!/bin/bash
for port in {1..1000}; do
 timeout 1 bash -c "echo >/dev/tcp/$1/$port" 2>/dev/null && echo "port $port is open"
done
```

El procedimiento para subir los ficheros sería indicar la ruta de origen y la de destino:

```bash
upload /root/port-scanner.sh /tmp/port-scanner.sh
```

También habrá que darles permisos de ejecución a estos ficheros con el siguiente comando:

```bash
chmod +x ./nmap ./bash-port-scanner.sh
```

> [!TIP] Recuerda el LHOST
> Recuerda configurar bien el LHOST y todos los parámetros referentes a la máquina en la que hagas el laboratorio, ya que si no no se abrirá la shell de meterpreter.

## Enumeración de servicios

Hay muchos módulos auxiliares que nos permitirán hacer enumeración de distintos servicios en nuestros objetivos.

### FTP

Para confirmar si un target está usando FTP, podremos hacer un escaneo con el módulo **scanner/portscan/tcp**, es decir, un escaneo TCP, y ver si el puerto 21 está abierto.

A partir del momento en el que sepamos que FTP está funcionando, podremos usar los módulos auxiliares de Metasploit para ayudarnos a determinar información la versión, si el login anónimo está permitido, etc.

Si queremos buscar solo módulos auxiliares de FTP, podremos hacerlo de la siguiente manera:

```bash
search type:auxiliary ftp
```

#### Versión

Para empezar, podremos usar el módulo auxiliar **auxiliary/scanner/ftp/ftp-version**, 
que nos permitirá detectar la versión del servicio.

#### Login

También hay módulos de bruteforcing, como **auxiliary/scanner/ftp/ftp_login**, al que le tendremos que mandar parámetros como PASS_FILE y USER_FILE, si queremos usar ficheros, si no, si queremos un usuario o contraseña en concreto podremos usar las options de USERNAME o PASSWORD.

Hay listas auxiliares de Metasploit en las rutas de **/usr/share/metasploit-framework/data/wordlists**, donde encontraremos **common_users.txt**, o **unix_passwords.txt**.

Después desde metasploit podremos acceder al servidor FTP:

```shell
ftp 192.168.1.3
```

Cabe destacar que puede ser que el servidor FTP no responda después de hacer el bruteforcing como mecanismo de defensa, simplemente tienes que esperar y usar las credenciales válidas cuando se resetee.

#### Login anónimo

Podremos probar el login anónimo como el módulo auxiliar 
**auxiliary/scanner/ftp/anonymous**, simplemente especificando el RHOSTS y usando run.

### SMB

SMB es un protocolo para compartir ficheros entre ordenadores en una red local.
Normalmente, se usa el puerto 445, y en versiones antiguas en el puerto 139.

La implementación de SMB en Linux se llama SAMBA, y permite que los equipos sean Windows o Linux, puedan compartir ficheros entre ellos.

Como vimos en FTP, podemos usar módulos auxiliares para recuperar las versiones, volúmenes, usuarios, y mucha más información.

Para encontrar los módulos auxiliares relevantes para esto podremos usar el siguiente comando:

```bash
search type:auxiliary smb
```

A parte de metasploit, también tenemos herramientas como **nmblookup**, que nos permitirán mapear IPs a nombres de NetBIOS.

```bash
nmblookup -A demo.ine.local
```
#### Versión

Para enumerar la versión podremos usar el módulo **scanner/smb/smb_version**. Esto nos devolverá la versión que se use, dándonos información como si es SMB o SAMBA, pudiendo así determinar si el target es Windows o Linux.

También podremos usar [[nmap]], con el script **smb-os-discovery.nse**.

#### Usuarios

Para esto podremos usar el módulo **scanner/smb/smb_enumusers**, que nos proporcionará los usuarios existentes.

#### Shares (volúmenes)

Usaremos el módulo **auxiliary/scanner/smb/smb_enumshares**, hay opciones interesantes como ShowFiles, que nos permitirá mostrar detalles de los ficheros mientras hace spidering.

#### Bruteforcing (Login)

También existen módulos auxiliares para el bruteforcing, como **auxiliary/scanner/smb/smb_login**, con opciones que deberemos poner como el SMBUser, el PASS_FILE o STOP_ON_SUCCESS, que nos permite parar la ejecución cuando encuentra unas credenciales válidas. 

Es muy similar al módulo auxiliar de FTP.

Para posteriormente, probar estas credenciales, podremos usar smbclient (fuera de Metasploit) y usar el siguiente comando para listar los shares disponibles:

```bash
smbclient -L \\\\192.168.1.1\\ -U nombre_usuario
```

Y finalmente, para entrar en un share:

```bash
smbclient \\\\192.168.1.1\\public -U nombre_usuario
```

También hay otras herramientas como rpcclient, que nos permitirán acceder con credenciales, o probar el login anónimo.

Este comando nos permitirá probar el login anónimo:

```bash
rpcclient -U "" -N 192.168.1.1
```

Y este nos permitirá acceder al servidor con las credenciales admin:admin

```bash
rpcclient -U "admin" -P "admin" 192.168.1.1
```

Posteriormente, podremos usar comandos como **enumdomusers**, o **netshareenum** para ver las shares.

## Variables globales

Para poner una variable de manera global usaremos el siguiente comando, que nos permitirá hacer esto mismo y que en todos los módulos que se use RHOSTS se ponga la IP que especifiquemos:

```bash
setg RHOSTS 192.168.1.1
```

## Busca de vulnerabilidades por CVE

Para buscar en metasploit por CVE usaremos el siguiente comando:

**==search cve:CVE-XXXX==**

Esto nos dará una lista de exploits que podremos utilizar con referencia a ese CVE.

---
# {{References}}