30-03-2025 18:56
Status: #idea
Tags: [[Windows Credential Dumping]]

# Windows Password Hashes

Windows guarda las hashes de las cuentas de usuarios localmente en la base de datos del SAM (Security Accounts Manager).

La autenticación y verificación de las credenciales de usuario son facilitadas por el Local Security Authority (LSA).

Las versiones de Windows mayores que Windows Server 2023, utilizan dos tipos de hashes: **LM y NTLM**. A partir de Windows Vista, se han desactivado las LM y solo se utiliza el NTLM hashing.

## SAM Database

Como hemos dicho anteriormente, las contraseñas hasheadas de todos los usuarios se guardan en esta base de datos.

Esta base de datos se puede copiar mientras el sistema operativo está funcionando.

El kernel mantiene bloqueada esta base de datos, por lo tanto los atacantes normalmente utilizan técnicas de memoria y herramientas para hacer dump desde el proceso LSASS.

En versiones recientes de Windows, la base de datos SAM está encriptada con una syskey.




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
