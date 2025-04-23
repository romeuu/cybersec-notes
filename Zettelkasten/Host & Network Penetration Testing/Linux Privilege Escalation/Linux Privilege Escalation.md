23-04-2025 18:24
Status: #idea
Tags: [[Linux Privilege Escalation]], [[Host & Network Penetration Testing]]

# Linux Privilege Escalation

- [[#Kernel Exploits|Kernel Exploits]]
	- [[#Kernel Exploits#Herramientas|Herramientas]]
	- [[#Kernel Exploits#Proceso|Proceso]]
- [[#Misconfigured Cron Jobs|Misconfigured Cron Jobs]]

En esta sección veremos como hacer escalada de privilegios en Linux.

## Kernel Exploits

Los exploits del kernel normalmente explotan vulnerabilidades en el kernel de Linux para conseguir ejecutar comandos con privilegios altos, o conseguir una shell en el sistema.

Este proceso variará dependiendo de la versión del kernel y la distribución que tenga el objetivo.

Normalmente la metodología es la siguiente:

- Identificamos las vulnerabilidades en el kernel.
- Descargamos, compilamos y transferimos exploits del kernel en el objetivo.

### Herramientas

En este caso usaremos [**Linux-Exploit-Suggester**](https://github.com/The-Z-Labs/linux-exploit-suggester), que nos permitirá detectar problemas en el kernel.

### Proceso

Tendremos que tener acceso al sistema objetivo (meterpreter o shell). Si tenemos un meterpreter haremos un upload del script de **LES (Linux-Exploit-Suggester)**. 

Lo ejecutaremos con un .sh normal:

```shell
./les.sh
```

Y esto nos dará una lista de vulnerabilidades, ordenadas por probabilidad de éxito.

En la lista veremos enlaces a PoC que podremos usar y modificar para escalar los privilegios. En este caso, se usa la vulnerabilidad dirtycow, y se cambiará el nombre de usuario del exploit.

## Misconfigured Cron Jobs

Los cronjobs los puede ejecutar cualquier usuario, pero deberíamos de centrarnos en los que se ejecutan como el usuario root.

Esto lo haremos ya que cualquier cronjob que se ejecute con el usuario root, nos dará permisos root.

Para listar los crontab que se ejecutan usaríamos el comando:

```shell
crontab -l
```

Si no tenemos editores de texto, podremos añadir la línea que nos permitirá meternos en el fichero sudoers para escalar los privilegios de la siguiente manera:

```shell
printf '#!/bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh
```

Si tenemos nano o vim, podríamos añadir esa línea directamente al cron.

Para comprobar que estamos en la lista de sudoers, podemos usar el comando: 

```shell
sudo -L
```

Si tenemos suerte, esto nos meterá en la lista.


---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
