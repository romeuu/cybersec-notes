12-12-2024 19:13
Status: #idea
Tags: [[Footprinting And Scanning]]

# Networking Fundamentals

En cualquier red, los hosts se comunican entre ellos con protocolos de red.

Estos se encargan de que estos diferentes hosts o equipos se puedan comunicar entre ellos, a pesar de usar hardware y software distinto.

## Packets

El principal objetivo de networking es el intercambio de información, y esto se hace con paquetes. Estos no son más que bytes que contienen la anteriormente mencionada información.

Cada paquete tiene una Header y un Payload.

Podemos ver el Header como la información que indica a donde va dirigido este paquete, y la payload como la información que se quiere intercambiar entre dispositivos.

## Modelo OSI

El modelo OSI es un modelo que se desarrolló para mejorar y asegurar la interoperabilidad en un rango muy grande de dispositivos.

Contiene 7 capas, que van de 7 a 1.

| #   | OSI Layer          | Function                                                                     | Examples                                     |
| --- | ------------------ | ---------------------------------------------------------------------------- | -------------------------------------------- |
| 7   | Application Layer  | Da acceso a los usuarios a la aplicación o funcionalidad que se esté usando. | HTTP, FTP, IRC, SSH, DNS                     |
| 6   | Presentation Layer | Traduce los datos entre la capa de aplicación y las capas más bajas.         | SSL/TLS, JPEG, GIF, SSH, IMAP                |
| 5   | Session Layer      | Maneja las sesiones y conexiones entre las aplicaciones.                     | APIS, NetBIOS, RPC                           |
| 4   | Transport Layer    | Se asegura de la comunicación end-to-end.                                    | TCP, UDP                                     |
| 3   | Network Layer      | Responsable del addressing lógico y el routing.                              | IP, ICMP, IPSec                              |
| 2   | Data Link Layer    | Maneja el acceso al medio físico y proporciona detección de errores.         | Ethernet, PPP, Switches, etc                 |
| 1   | Physical Layer     | Se encarga de la conexión física entre dispositivos.                         | USB, Ethernet Cables, Coax, Fiber, Hubs, etc |






---
# {{References}}