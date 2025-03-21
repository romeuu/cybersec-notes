21-03-2025 20:08
Status: #idea
Tags: [[Host & Network Penetration Testing]]

# Windows Privilege Escalation

La escalada de privilegios es el proceso que nos permite aumentar los permisos de un usuario a otro, normalmente de un usuario sin permisos o de bajo nivel, a uno de alto nivel como el root.

Es una parte vital de un ataque, ya que nos permitirá tener más control sobre lo que podemos hacer en nuestro objetivo.

Sin escalada de privilegios, puedes quedar bloqueado, y no poder avanzar más en una máquina o una red.

## Windows Kernel

El kernel es el núcleo de un sistema operativo, y tiene control absoluto a cada recurso y hardware de un sistema. Actúa como una capa de comunicación entre el hardware y el software.

El Kernel de Windows tiene dos modos de operación:

- **User Mode**: Acceso limitado a recursos y funcionalidades del sistema.
- **Kernel Mode**: Tiene acceso sin restringir a los recursos y funcionalidades del sistema.

### Kernel Exploitation

La explotación del kernel nos permitirá ejecutar comandos privilegiados en el sistema.

El proceso va a variar dependiendo de la versión de Windows que se esté usando. Los pasos comunes serían:

- Identificar las vulnerabilidades del kernel.
- Descargar, compilar y transferir exploits del kernel en el sistema objetivo.

#### Herramientas

- [**Windows-Exploit-Suggester**](https://github.com/AonCyberLabs/Windows-Exploit-Suggester): Esta herramienta nos permite comparar los parches que tiene el sistema instalados con la base de datos de vulnerabilidades de Microsoft, pudiendo así encontrar parches de seguridad que no se han instalado. También notificará al usuario si hay exploits públicos o módulos de Metasploit para estos parches.

- [**Windows-Kernel-Exploits**:](https://github.com/SecWiki/windows-kernel-exploits) Colección de exploits del Kernel de Windows ordenados por CVE.

- **post/multi/recon/local_exploit_suggester**: Este módulo de Metasploit nos permitirá escanear las vulnerabilidades para el objetivo, usando una sesión de meterpreter que tengamos abierta.

También se puede probar el comando **getsystem** en meterpreter, que intentará varias técnicas para elevar los permisos.

##### Windows-Exploit-Suggester

Para poder usarlo necesitaremos guardar la información de sistema en un fichero txt. Para esto, spawnearemos una shell en nuestra sesión de meterpreter y ejecutaremos el comando systeminfo:

```shell
shell
C:\Temp:> systeminfo
```

Copiaremos el resultado del comando, y lo pondremos en un fichero txt con cualquier editor de texto (nano, vim...).

Ahora solo necesitaremos pasarle esto a Windows-Exploit-Suggester:

```shell
./windows-exploit-suggester.py --update
./windows-exploit-suggester.py --database fichero_creado_con_update --syteminfo txt_de_informacion_sistema
```

Esto nos devolverá los exploits ordenados por fiabilidad, es decir, los que salen más arriba tienen más posibilidades de funcionar bien.

A partir de aquí, tocará investigar sobre los exploits, ver que PoC hay, e intentar subirlos al target para conseguir escalar los privilegios.



---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
