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




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
