17-01-2025 17:51
Status: #idea
Tags: [[Footprinting And Scanning]], [[Network Mapping]]

# Network Mapping

Network Mapping es el proceso de descubrimiento e identificación de dispositivos, hosts y elementos de la infraestructura de la red de nuestro objetivo.

Es una etapa crucial para conseguir recuperar información de como está conectada la red, conocer su arquitectura, e identificar puntos de acceso para posteriormente explotarlos.

### Ejemplo práctico

Si se nos presenta el caso de que una compañía nos encarga un pentesting, y el siguiente bloque está en scope: 200.200.0.0/16, se nos estará indicando que habrá hasta 216 (65536) hosts con direcciones IP en el bloque 200.200.0.0 - 200.200.255.255.

El primer paso para un pentester sería cual de esas 65536 direcciones IP están asignadas a un host, y cuales de esos hosts están online/activos.

### Objetivos del Network Mapping

- **Descubrimiento de hosts live**: Identificar que hosts están vivos y online dentro de nuestra red. Esto requiere determinar que direcciones IP están en uso actualmente.

- **Identificación de puertos abiertos y servicios**: Determinar que puertos están abiertos en nuestros hosts e identificar los servicios que están siendo ejecutados nos permitirá entender que vulnerabilidades podremos explotar.

- **Topología de red**: Crear un mapa o diagrama de la topología de nuestra red, incluyendo routers, switches, firewalls y otra infraestructura de la red nos ayudará a avanzar en nuestro pentest.

- **Fingerprinting de sistemas operativos**: Determinar que sistemas operativos están corriendo en nuestros hosts nos ayudará a explotar vulnerabilidades específicas del OS.

- **Detección de versiones de servicios**: Identificar las versiones específicas de los servicios es crucial para saber que vulnerabilidades explotar y si hay una para esa versión en concreto.

- **Identificar medidas de seguridad**: Identificar medidas de seguridad como firewalls, sistemas de prevención y otras medidas de seguridad nos permitirá trazar un plan teniendo en cuenta la defensa que use el host.

### Herramientas a usar

Para realizar Network Mapping lo más común es usar **[[nmap]]**, ya que nos permitirá hacer [[host discovery]], escaneo de puertos, detección de servicios o fingerprinting del sistema operativo.



---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
