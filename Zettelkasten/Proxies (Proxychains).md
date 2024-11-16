 15-11-2024 19:37
Status: #idea
Tags:

# Proxies (Proxychains)

Puede darse el momento en el que queramos pasar nuestro tráfico por el proxy de burpsuite, por ejemplo, con nmap, o si simplemente queremos interceptar tráfico que generamos con curl.

Para esto tendremos que editar el fichero /etc/proxychains.conf, y añadir la línea al final:
**==http 127.0.0.1 8080==**, que hará que el tráfico pase por Burp.






---
# {{References}}