16-01-2025 19:00
Status: #idea
Tags: [[Transport Layer]], [[OSI Model]], [[Networking Fundamentals]]

# UDP

UDP es un protocolo que no necesita establecer una conexión estricta como TCP, ya que no tiene el mismo grado de fiabilidad o ratio de entrega que TCP. Se centra en ser simple y eficiente, haciendo que sea ideal para cierto tipo de aplicaciones.

### Características

- **No necesita de conexión**: Cada paquete (datagram) es tratado de manera independiente, y no hay un estado persistente entre el que envía y el que recibe.

- **No es tan fiable**: No garantiza que se entreguen todos los paquetes, y no hay un mecanismo para recuperar paquetes perdidos. Esta característica hace que UDP sea mucho más rápido, pero menos útil para aplicaciones que necesiten una entrega fiable.

- **Uso para aplicaciones en tiempo real**: UDP se usa comúnmente, para aplicaciones donde la latencia baja es crucial, como en audio o streaming de vídeo, videojuegos en línea, y comunicación VoIP.

- **Simple y sin estado persistente**: UDP no guarda ningún tipo de información de estado entre los dispositivos.

- **Independencia**: Cada paquete de UDP es independiente del paquete anterior, y del futuro. 




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
