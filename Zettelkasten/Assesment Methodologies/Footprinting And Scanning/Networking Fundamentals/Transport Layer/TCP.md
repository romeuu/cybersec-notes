16-01-2025 18:36
Status: #idea
Tags: [[Transport Layer]], [[OSI Model]], [[Networking Fundamentals]]

# TCP

Con este protocolo se busca una fiabilidad del 100%, es decir, se asegura de que los datos lleguen a su destino con total fiabilidad, y siguiendo el orden especificado. 

Se asegura de esto gracias a mecanismos como ACK y la retransmisión de paquetes perdidos. Si un fragmento de los datos que se están enviando no es confirmado por el dispositivo de destino, se volverá a enviar automáticamente.

### TCP 3-Way Handshake

El funcionamiento del TCP se basa en un 3-Way Handshake, que básicamente, es una serie de peticiones entre los dos dispositivos para conseguir establecer una conexión.

Sigue esta orden:

- **SYN**: Este mensaje inicial indica la intención de el cliente de establecer una conexión y este incluye una secuencia initial llamada ISN, que es un valor random.

- **SYN-ACK**: Recibiendo el SYN, el servidor o dispositivo, responde con un segmento TCP que tenga las flags SYN y ACK (Acknowledge). El ACK es un número se asignará al valor del SYN + 1. El servidor también generará su propio número secuencial.

- **ACK**: El cliente reconoce la respuesta del servidor enviando un segmento TCP con la flag ACK incluida. Este número será el número secuencial generado por el servidor más uno.

Una vez 




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
