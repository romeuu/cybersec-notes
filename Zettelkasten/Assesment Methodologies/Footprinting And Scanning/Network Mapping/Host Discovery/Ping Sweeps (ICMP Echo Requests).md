17-01-2025 18:38
Status: #idea
Tags: [[Host Discovery]]

# Ping Sweeps (ICMP Echo Requests)

Un ping sweep es una técnica para el descubrimiento de hosts que nos permite determinar si un host está activo o no. 

El funcionamiento básico sería enviar una serie de ICMP Echo Requests (ping) a un rango de direcciones IP y observar las respuestas que nos llegan, determinando así que hosts están activos.

### Funcionamiento

Se usa el comando básico de ping:

````
ping www.site.test
````


> [!TIP] TIP
> Si estás haciendo host discovery y el host es Windows no deberemos utilizar la técnica de ping sweep, ya que Windows bloquea por defecto las peticiones ICMP Echo.




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
