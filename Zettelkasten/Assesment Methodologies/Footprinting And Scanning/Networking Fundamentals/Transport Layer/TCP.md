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

Una vez este handshake se ha completado, ambos dispositivos podrán enviar datos en ambas direcciones. Los números secuenciales ACK serán enviados para confirmar la recepción de datos o el flow de la información.

### Composición

Un paquete TCP está compuesto por distinta información, como el origen, el destino, los números secuenciales, el número de acknowledgment (ACK), etc.

#### Headers

Hay dos headers muy importantes, que serían la **SRC** (Source) y **DST** (Destination), ambas de 16 bits, que identifican el puerto de origen, y el puerto de destino. 

#### Control flags

Estas flags de control nos permiten manejar distintas acciones dentro de la comunicación, como el establecimiento, el mantenimiento y la terminación de esta.

**Para establecer una conexión**:
- SYN (Set): Inicia una petición de conexión.
- ACK (Clear): No hay acknowledgment aún.
- FIN (Clear): No hay petición de finalización.

**Respuesta a esta petición de conexión**:
- SYN (Set): Reconoce la petición de conexión.
- ACK (Set): Reconoce la data recibida.
- FIN (Clear): No hay petición de finalización.

**Terminar la conexión**:
- SYN (Clear): No hay petición de conexión.
- ACK (Set): Reconoce la data recibida.
- FIN (Set): Inicia la finalización de la conexión.

### Rango de puertos para TCP

Los puertos son integer 16-bit unsigned, y son divididos en tres rangos. El puerto máximo que se puede usar es el 65535.

Hay recomendación de no pisar ciertos puertos, ya que están "reservados" para servicios comunes, como por ejemplo:

- HTTP: 80.
- HTTPS: 443.
- FTP: 21.
- SSH: 22.
- SMTP: 25.
- POP3: 110.

También hay puertos a partir del 1024 hasta el 49151, que no son estandarizados, pero siguen siendo usados por servicios comunes, como por ejemplo:

- RDP: 3389.
- MySQL Database: 3306.
- HTTP Alternative Port: 8080.
- MongoDB Database: 27017.

---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
