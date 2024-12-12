12-12-2024 19:34
Status: #idea
Tags: [[OSI Model]], [[Networking Fundamentals]]

# Network Layer

Esta capa es la encargada de hacer el direccionamiento lógico, routing, y envío de paquetes entre los diferentes dispositivos de la red.

Su principal objetivo es determinar el camino más óptimo, desde el origen hasta el receptor, por el que enviar el paquete, incluso si los dispositivos se encuentran en redes distintas.

## Protocolos

Tenemos varios protocolos en esta capa, entre ellos tenemos: 
- **Internet Protocol (IP)**:
	- **IPv4** (Internet Protocol Version 4): Es el más usado, usando direcciones de 32 bits y proporcionando así los cimientos de la comunicación del Internet. **192.168.1.1**
	- **IPv6** (Internet Protocol Version 6): Desarrollado para afrontar las limitaciones del IPv4, ya que usa 128 bits y tiene espacio para muchas direcciones más. **2001:0db8:85a3:0000:0000:8a2e:0370:7334**
- **Internet Control Message Protocol (ICMP)**:
	- Usado para el reporte de errores y diagnósticos, el ICMP incluye el ping (echo request y echo reply), traceroute, y otros varios mensajes de error.

### Internet Protocol (IP)
#### Funcionalidad IP

El protocolo IP nos permite funcionalidades como la fragmentación y el reensamblaje, lo que nos permitirá dividir paquetes en trozos más pequeños cuando se envíen paquetes grandes.

Las direcciones IP se pueden clasificar en tres tipos:
- **Unicast** (One to One).
- **Broadcast** (One to all dentro de una subnet).
- **Multicast** (One to many para un grupo seleccionado de dispositivos).

Además, también permite el subnetting, que nos permite segmentar una red muy grande, en redes más pequeñas para conseguir una mayor eficiencia y seguridad.

El protocolo IP también se usa con otros protocolos como el DHCP, que nos permite asignar direcciones IP a nuestros dispositivos de manera dinámica dentro de una red.

#### Formato de un paquete IP

El paquete IP tiene una header como ya habíamos visto en otros espacios del módulo, donde se especifican los siguientes detalles:

- **IP Source Address**: Origen del paquete.
- **IP Destination Address**: Destino del paquete.
- **Time-to-Live (TTL)**: Un valor de 8 bits que nos indica hasta cuando vivirá el paquete.
- **Type-of-Service (ToS)**: Contiene un valor de 8 bits que nos indica la prioridad de cada paquete.
- **Protocol**: Contiene un valor de 8 bits que nos indica que tipo de datos van en el payload del paquete. Por ejemplo, 6 sería TCP, 17 para UDP y 1 para ICMP.
- Y más detalles como el **Total Length, Identification, Flags**, etc.

![[Pasted image 20241212205342.png]]

#### Direcciones IPv4

Hay rangos de direcciones IPv4 que son reservadas, como por ejemplo:

- **0.0.0.0-0.255.255.255**: Representa a esta misma red.
- **127.0.0.0-127.255.255.255**: Representa al host local (tu ordenador).
- **192.168.0.0-192.168.255.255**: Representa redes privadas.



---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```