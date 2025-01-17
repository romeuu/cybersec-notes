17-01-2025 18:21
Status: #idea
Tags: [[Network Mapping]]

# Host Discovery

Es el proceso en el que se identifican los hosts que están activos o en línea en una red específica.

Se puede realizar a través de varias técnicas, y la elección de estas dependerá de factores como las características de la red, el nivel de sigilo que debamos seguir, o las metas del pentest.

### Técnicas

- **Ping Sweeps (ICMP Echo Requests)**: Enviar ICMP Echo Requests (ping) a un rango de direcciones IP nos permitirá identificar hosts activos. Es un método rápido y muy común.

- **ARP Scanning**: Usar requests ARP para identificar hosts en una misma red es útil si se encuentran en el mismo dominio del broadcast. Es decir, tendrás que estar dentro de la red, si no, no funcionará.

- **TCP SYN Ping (Half-Open Scan)**: Enviar paquetes TCP SYN a un puerto específico (normalmente 80) para verificar si el host está vivo. Si el host está activo, responderá con un TCP SYN ACK. Esta técnica es más sigilosa que el ICMP ping. 

- **UDP Ping**: Se utiliza para hosts que no responden ICMP o TCP probes, ya que envía un paquete UDP a un puerto específico para verificar si está activo.

- **TCP ACK Ping**: Envía paquetes TCP ACK a un puerto específico para descubrir si el host está activo. Esta técnica no espera una respuesta, pero si un paquete TCP RST (Reset) es recibido, indica que el host está vivo.

- **SYN-ACK Ping**: Envía paquetes TCP SYN-ACK a un puerto específico para descubrir si el host está activo. Como en el caso anterior, si recibe un TCP RST, indica que el host está activo.

Hay que tener en cuenta, que por ejemplo, el **firewall de Windows va a bloquear por defecto las peticiones ICMP**.

#### Ventajas y desventajas

- **ICMP Ping**:
	- **Pros**: Es ampliamente soportado y es un método rápido para identificar hosts vivos.
	- **Cons**: Algunos hosts o firewalls pueden estar configurados para bloquear tráfico ICMP, limitando su efectividad. Un ICMP Ping es detectado fácilmente.

- **TCP SYN Ping:** 
	- **Pros**: Es más sigiloso que un ICMP Ping y puede traspasar firewalls que permitan conexiones externas.
	- **Cons**: Hay hosts que no responden a este tipo de técnica, y los resultados pueden ser afectados por firewalls o utilidades de seguridad.


---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
