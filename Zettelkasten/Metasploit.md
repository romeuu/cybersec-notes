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
	- [[#Enumeración de servicios#Apache|Apache]]
		- [[#Apache#Versión|Versión]]
		- [[#Apache#Robots.txt|Robots.txt]]
		- [[#Apache#Directorios|Directorios]]
		- [[#Apache#Ficheros|Ficheros]]
		- [[#Apache#Login|Login]]
		- [[#Apache#Usuarios|Usuarios]]
		- [[#Apache#Subir/borrar ficheros|Subir/borrar ficheros]]
	- [[#Enumeración de servicios#MySQL|MySQL]]
		- [[#MySQL#Versión|Versión]]
		- [[#MySQL#Bruteforce (Login)|Bruteforce (Login)]]
		- [[#MySQL#Enum|Enum]]
		- [[#MySQL#Queries|Queries]]
		- [[#MySQL#Hash Dump|Hash Dump]]
		- [[#MySQL#Directorios|Directorios]]
		- [[#MySQL#Acceso a servidor MySQL|Acceso a servidor MySQL]]
		- [[#MySQL#Schema|Schema]]
	- [[#Enumeración de servicios#SSH|SSH]]
		- [[#SSH#Versión|Versión]]
		- [[#SSH#Login (Bruteforcing)|Login (Bruteforcing)]]
		- [[#SSH#Enumeración de usuarios|Enumeración de usuarios]]
	- [[#Enumeración de servicios#SMTP|SMTP]]
		- [[#SMTP#Version|Version]]
		- [[#SMTP#User Enumeration|User Enumeration]]
		- [[#SMTP#Ejemplo práctico|Ejemplo práctico]]
			- [[#Ejemplo práctico#Verificar si un email en concreto existe|Verificar si un email en concreto existe]]
			- [[#Ejemplo práctico#Comandos posibles|Comandos posibles]]
			- [[#Ejemplo práctico#Enumeración de usuarios|Enumeración de usuarios]]
			- [[#Ejemplo práctico#Envío de mails|Envío de mails]]
- [[#Variables globales|Variables globales]]
- [[#Escaneo de vulnerabilidades|Escaneo de vulnerabilidades]]
	- [[#Escaneo de vulnerabilidades#Búsqueda de vulnerabilidades|Búsqueda de vulnerabilidades]]
		- [[#Búsqueda de vulnerabilidades#Servicio|Servicio]]
			- [[#Servicio#Searchsploit|Searchsploit]]
			- [[#Servicio#db_autopwn|db_autopwn]]
			- [[#Servicio#Analyze|Analyze]]
		- [[#Búsqueda de vulnerabilidades#CVEs|CVEs]]



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

También podremos listar las credenciales que hemos conseguido con diferentes scripts, con el comando **loot**, que nos mostrará en el path un fichero txt que contendrá estas credenciales.

Además, podremos usar el comando **creds**, para ver las credenciales que hemos conseguido, como por ejemplo, las hashes que se consiguen con módulos de enumeración de MySQL.

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

### Apache

Hay muchos módulos auxiliares que podremos usar para enumerar versiones e información extra de un servidor Apache, y otros servidores web.

#### Versión

Como en los otros servicios, podremos usar módulos para la enumeración de versiones, en este caso, podremos usar el módulo **scanner/http/http_version**. 

Si estamos ante una web con SSL válido (puerto 443), podremos poner las variables SSL a true y RPORT a 443.

Esto nos dará la versión de Apache, junto el sistema operativo que está usando el servidor.

Esto también lo podremos obtener con banner grabbing, o analizando las headers de una respuesta del servidor. Existe el módulo **scanner/http/http_header** para esto.

#### Robots.txt

Para analizar el robots.txt también podremos usar un módulo llamado **scanner/http/robots_txt**.

#### Directorios

También podremos hacer enumeración de directorios con el módulo **scanner/http/dir_scanner**.

#### Ficheros

Para encontrar ficheros podremos usar el módulo **scanner/http/files_dir**.

#### Login

Para intentar hacer bruteforcing de autenticación basic en html, podremos usar el módulo **scanner/http/http_login**, que con la ayuda de una wordlist (PASS_FILE y USER_FILE), intentará acceder al directorio protegido. 

Si estamos usando una PASS_FILE y USER_FILE, no necesitaremos el USERPASS_FILE, por lo que podremos hacer un unset de esta variable.

Si no se encuentra nada con las wordlists por defecto, puedes usar las wordlists del directorio **/usr/share/metasploit-framework/data/wordlists**, donde encontraremos **common_users.txt**, o **unix_passwords.txt**

Con este módulo tendremos que indicar la URL a la que se quiere atacar, siendo el parámetro AUTH_URI.

#### Usuarios

También podremos hacer enumeración de usuarios con el módulo **scanner/http/apache_userdir_enum**, lo que será útil para volver al módulo de bruteforcing e indicar la variable USER_FILE con un fichero con estos usuarios.

#### Subir/borrar ficheros

También existen módulos como **auxiliary/scanner/http/http_put**, que nos permitirán subir un fichero a un directorio, por ejemplo:

```
use auxiliary/scanner/http/http_put
set PATH /data
set FILENAME test.txt
set FILEDATA "Welcome To AttackDefense"
run
```

Esto nos permitiría subir test.txt al directorio data. 

### MySQL

MySQL es una base de datos relacional basada en SQL. Normalmente, se encuentra en el puerto 3306 por defecto, y podemos usar módulos auxiliares de Metasploit para enumerar este servicio.

#### Versión

Podremos usar el módulo **auxiliary/scanner/mysql/mysql_version**, que nos dará información también del sistema operativo, a parte de la versión de la instancia de MySQL.

#### Bruteforce (Login)

Podremos usar el módulo **auxiliary/scanner/mysql/mysql_login**, con las opciones comunes de PASS_FILE, USER_FILE, USERNAME, etc.

Por ejemplo, es bastante común encontrar un usuario llamado root, por lo tanto podríamos poner el USERNAME root y un PASS_FILE como **unix_passwords**.

#### Enum

Para enumerar más, usaremos el módulo **auxiliary/admin/mysql/mysql_enum**, que requiere de un usuario y una contraseña válida para poder hacer una enumeración completa de MySQL, dándonos información como el hostname, la versión, opciones de la base de datos, lista de hashes de las contraseñas, cuentas con permisos elevados, etc.

#### Queries

Este es un módulo que nos permite ejecutar queries SQL, se llama **auxiliary/admin/mysql/mysql_sql**.

En este tendremos que indicar el usuario y la contraseña, con los parámetros USERNAME y PASSWORD, y posteriormente modificar la opción SQL, que por defecto hace un select version().

Por ejemplo, podríamos ejecutar una query que liste las bases de datos, entrar en una, ver las tablas, etc, como si se tratase de queries normales.

#### Hash Dump

Si queremos hacer un dumpeo de hashes directamente, podremos usar el módulo **auxiliary/scanner/mysql/mysql_hashdump**, que nos dará todas las hashes de todos los usuarios.

#### Directorios

Si queremos ver que directorios tienen permisos de escritura, podremos usar el módulo **auxiliary/scanner/mysql/mysql_writable_dirs**, que con una wordlist que proporcionemos, intentará escribir un archivo de prueba, y ver si efectivamente hay permisos.

#### Acceso a servidor MySQL

El acceso a MySQL se hará desde fuera de metasploit con los siguientes comandos:

```shell
mysql -h 192.168.1.1 -u nombre_usuario -p
```

#### Schema

Hay un módulo también que nos permite ver el schema de una o varias bases de datos **auxiliary/admin/mysql/mysql_schemadump**, lo que nos permitiría ver las tablas de estas bases de datos.

### SSH

SSH es un protocolo de administración remota que ofrece comunicación encriptada, siendo así el sucesor de Telnet. 

El puerto donde se ubica normalmente es el 22. Así como en el resto de tecnologías, podremos usar los módulos auxiliares de Metasploit para hacer enumeración.

#### Versión

Podremos usar el módulo **auxiliary/scanner/ssh/ssh_version** para conseguir hacer enumeración de la versión del servicio, además del banner del sistema operativo también.

#### Login (Bruteforcing)

Para esto existe el módulo **auxiliary/scanner/ssh/ssh_login**. Como en todos los módulos de bruteforcing, necesitaremos indicar opciones como el USERNAME, o por el contrario, un USER_FILE, y una PASSWORD o PASS_FILE.

En caso de usar wordlists, podrías usar **common_users.txt** y **common_passwords.txt** o **common_unix.txt**.

Tan pronto encuentre credenciales válidas abrirá una shell, a la que nos podremos conectar y manipular el objetivo como queramos spawneando una bash con el comando `bin/bash -i`.

#### Enumeración de usuarios

Con el módulo **auxiliary/scanner/ssh/ssh_enumusers** podremos enumerar los usuarios proporcionando una USER_FILE (**common_users.txt**, por ejemplo).


### SMTP

El SMTP es un protocolo de comunicación usado para la transmisión de emails.

Usa el puerto 25 por defecto, y también se puede configurar en los puertos 465 y 587.

Podremos usar los módulos auxiliares para enumerar la versión y los usuarios en el objetivo.

#### Version

Podremos usar el módulo **auxiliary/scanner/smtp/smtp_version** para enumerar la versión de SMTP, y el dominio donde se encuentra este SMTP configurado.

#### User Enumeration

Para esto podremos usar el módulo **auxiliary/scanner/smtp/smtp_enum**, al que tendremos que proporcionarle un USER_FILE (**common_users.txt, o unix_users.txt**, por ejemplo).

Al terminar, nos dará una lista de que usuarios se han encontrado.

#### Ejemplo práctico

Tenemos un servidor SMTP en la IP: 192.172.212.3, con el módulo **smtp_version** podremos saber que tiene la versión: `SMTP 220 openmailbox.xyz ESMTP Postfix: Welcome to our mail server.\x0d\x0a`.

Posteriormente, podremos conectarnos con netcat (nc) al servidor SMTP de la siguiente manera:

```shell
nc demo.ine.local 25
```

Lo que nos dará el mismo mensaje que vimos antes con el banner que nos proporcionó el módulo de versión.

##### Verificar si un email en concreto existe

Si queremos verificar si existe un email, podremos usar la instrucción VRFY:

```shell
VRFY admin@openmailbox.xyz
```

Que nos devolverá un 252 en caso de que exista ese email, y un 550 en caso de que no.

##### Comandos posibles

Para verificar que acciones posibles hay en el servidor, podremos usar el comando EHLO, por ejemplo:

```shell
EHLO test.com
```

Podrás usar cualquier dominio, ya que normalmente no se verifica, y es meramente informativo.

Esto te devolverá una lista como esta:

```shell
250-smtp.example.com Hello fake.com [IP] 
250-PIPELINING 
250-SIZE 52428800 
250-STARTTLS 
250-AUTH LOGIN PLAIN 
250 HELP
```

##### Enumeración de usuarios

Para enumerar usuarios tendremos que usar el módulo **scanner/smtp/smtp_enum**, en este caso con la wordlist /usr/share/commix/src/txt/usernames.txt.

Al pasar un tiempo, nos devuelve estos resultados: `Users found: admin, administrator, mail, postmaster, sales, support, www-data`.

También podremos usar la herramienta smtp-user-enum de la siguiente manera:

```shell
smtp-user-enum -M VRFY -U users.txt -t 192.172.212.3
```

En este laboratorio fue más rápido el uso de smtp-user-enum, que el módulo de metasploit.

##### Envío de mails

Para enviar mails podremos hacerlo desde telnet de la siguiente manera:

```shell
telnet demo.ine.local 25
HELO attacker.xyz
MAIL FROM: admin@attacker.xyz
RCPT TO:root@openmailbox.xyz
DATA
Subject: Hi Root
Hello,
This is a fake mail sent using telnet command.
From,
Admin
.
```

También podremos usar la utilidad sendmail:

```shell
sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no
```

## Variables globales

Para poner una variable de manera global usaremos el siguiente comando, que nos permitirá hacer esto mismo y que en todos los módulos que se use RHOSTS se ponga la IP que especifiquemos:

```bash
setg RHOSTS 192.168.1.1
setg RHOST 192.168.1.1
```

## Escaneo de vulnerabilidades

Con Metasploit también tendremos la oportunidad de hacer escaneo de vulnerabilidades o de importar escaneos que realicemos con Nessus.

También, un punto de entrada sería usar db_nmap, ver que servicios está usando nuestro target, y a partir de ahí buscar exploits relativos a estos servicios.

> [!TIP] TIP
> Recuerda, para enumerar los servicios que enumeras en un escaneo de nmap, puedes usar el comando **services** en Metasploit.

### Búsqueda de vulnerabilidades

#### Servicio

Por ejemplo, si queremos buscar exploits para MySQL y sabemos que la versión es la 5.5, podríamos usar algo como:

```shell
search type:exploit MySQL 
```

Y posteriormente podremos ver si hay alguno, y si se adapta a nuestra versión, seleccionando el módulo del que queremos información, y usando el comando **info**.

##### Searchsploit

También podremos usar **searchsploit** en Kali, ya que viene en el sistema operativo por defecto.

Esta sintaxis sería si queremos enumerar los exploits para SMB y que se encuentren dentro de Metasploit. Si esto nos da igual, y queremos enumerar todos los exploits disponibles, sacaríamos el grep. 

```shell
searchsploit "Microsoft Windows SMB" | grep -e "Metasploit"
```

##### db_autopwn

Por otra parte, podremos hacer uso del plugin de db_autopwn en Metasploit, que nos permitirá hacer una búsqueda relativa a los servicios que hemos enumerado, o a puertos específicos que nos convengan.

El plugin se puede encontrar aquí: https://github.com/hahwul/metasploit-autopwn, y es un fichero ruby que hay que mover a la carpeta de plugins de metasploit, en principio: **/usr/share/metasploit-framework/plugins**. 

Una vez tengamos el plugin descargado usaremos este comando:

```shell
mv db_autopwn.rb /usr/share/metasploit-framework/plugins
```

Posteriormente, solo tendremos que entrar en Metasploit, y usar el comando **load db_autopwn**, que se encargará de cargar el plugin.

Uso de db_autopwn común, que se encargará de listar los exploits con los servicios enumerados:

```shell
db_autopwn -p -t
```

También podemos especificar los puertos con el comando:

```shell
db_autopwn -p -t -PI 445
```


##### Analyze

En Metasploit, el comando `analyze` se usa para **automatizar la detección de vulnerabilidades** en los hosts descubiertos. Es una funcionalidad del auxiliary/scanner que revisa los servicios detectados y sugiere módulos de explotación adecuados.

Posteriormente, podremos usar el comando `vulns` también.

#### CVEs

Para buscar en metasploit por CVE usaremos el siguiente comando:

```shell
search cve:CVE-XXXX
```

Esto nos dará una lista de exploits que podremos utilizar con referencia a ese CVE.

---
# References

https://fwhibbit.es/auditando-un-servidor-smtp