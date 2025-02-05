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

Para poder escanear con total certeza esta nueva máquina, tendremos que subir el ejecutable de nmap, o algún script en bash, y esto lo conseguiremos desde meterpreter con el comando upload. [El ejecutable de nmap lo podremos crear](https://nmap.org/book/inst-source.html), y un script en bash sencillo para hacer esto sería este:

```shell
#!/bin/bash
for port in {1..1000}; do
 timeout 1 bash -c "echo >/dev/tcp/$1/$port" 2>/dev/null && echo "port $port is open"
done
```


> [!TIP] Recuerda el LHOST
> Recuerda configurar bien el LHOST y todos los parámetros referentes a la máquina en la que hagas el laboratorio, ya que si no no se abrirá la shell de meterpreter.

## Busca de vulnerabilidades por CVE

Para buscar en metasploit por CVE usaremos el siguiente comando:

**==search cve:CVE-XXXX==**

Esto nos dará una lista de exploits que podremos utilizar con referencia a ese CVE.

---
# {{References}}