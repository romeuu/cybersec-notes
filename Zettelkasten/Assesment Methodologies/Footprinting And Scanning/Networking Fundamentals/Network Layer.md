12-12-2024 19:34
Status: #idea
Tags: [[OSI Model]], [[Networking Fundamentals]]

# Network Layer

Esta capa es la encargada de hacer el direccionamiento lógico, routing, y envío de paquetes entre los diferentes dispositivos de la red.

Su principal objetivo es determinar el camino más óptimo, desde el origen hasta el receptor, por el que enviar el paquete, incluso si los dispositivos se encuentran en redes distintas.

## Protocolos

Tenemos varios protocolos en esta capa, entre ellos tenemos: 
- Internet Protocol (IP):
	- **IPv4** (Internet Protocol Version 4): Es el más usado, usando direcciones de 32 bits y proporcionando así los cimientos de la comunicación del Internet. **192.168.1.1**
	- **IPv6** (Internet Protocol Version 6): Desarrollado para afrontar las limitaciones del IPv4, ya que usa 128 bits y tiene espacio para muchas direcciones más. **2001:0db8:85a3:0000:0000:8a2e:0370:7334**
- Internet Control Message Protocol (ICMP):
	- Usado para el reporte de errores y diagnósticos, el ICMP incluye el ping (echo request y echo reply), traceroute, y otros varios mensajes de error.

## Funcionalidad

El protocolo IP nos permite funcionalidades como la fragmentación y reensamblaje 





---
# {{References}}