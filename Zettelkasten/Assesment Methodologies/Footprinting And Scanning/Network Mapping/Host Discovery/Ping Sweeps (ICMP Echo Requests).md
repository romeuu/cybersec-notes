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

El host nos responderá con un ICMP Echo Reply (Type 0) si está activo, y si no lo está no nos dará respuesta. Puede ser también, que como se destacó anteriormente, el host bloquee este tipo de requests, pero si que esté activo.

Si se quiere limitar el número de paquetes a enviar, podremos usar las flags **-c** (Linux) o **-n** (Windows) seguido del número.

### Fping

Fping es una mejora del ping tradicional que nos ayudará a hacer recon en un broadcast.

Por ejemplo, podríamos hacer lo siguiente, donde -a nos mostrará los sistemas que están activos, -g para especificar la IP netmask, y 2>/dev/null para que no nos muestre los hosts inactivos:

````
fping -a -g 10.10.23.0/24 2>/dev/null
````

Lo que nos daría todos los hosts activos en la red 10.10.23.0/24.

---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
