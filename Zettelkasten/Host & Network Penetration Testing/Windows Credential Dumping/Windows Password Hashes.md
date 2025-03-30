30-03-2025 18:56
Status: #idea
Tags: [[Windows Credential Dumping]]

- [[#SAM Database|SAM Database]]
- [[#Buscando contraseñas en ficheros de configuración|Buscando contraseñas en ficheros de configuración]]

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

---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
