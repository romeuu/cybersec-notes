 05-08-2024 20:46
Status: #idea
Tags: [[Exploiting]]

# Metasploit

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

Para escanear puertos podremos usar módulos como el scanner/portscan/tcp. 

Este módulo nos permitirá verificar que puertos están abiertos, sin la necesidad de usar nmap.

Para encontrar el módulo usaremos la función search de metasploit, seguido de scanner portscan.

Con show options veremos las opciones que son requeridas, en el caso de este módulo, el obligatorio sería RHOSTS (set RHOSTS xxx).

Una vez tengamos finalizado el escaneo de puertos, podremos hacer esto: **==search port:445==**, y nos buscará vulnerabilidades que hagan referencia a este puerto.

## Busca de vulnerabilidades por CVE

Para buscar en metasploit por CVE usaremos el siguiente comando:

**==search cve:CVE-XXXX==**

Esto nos dará una lista de exploits que podremos utilizar con referencia a ese CVE.

---
# {{References}}