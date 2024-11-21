21-11-2024 20:02
Status: #idea
Tags: [[Active Information Gathering]]

# robots.txt

El fichero robots.txt es un archivo que indica que sitios son permitidos para hacer crawling, y cuales no, es decir, se indican las rutas o directorios de nuestra web que no queremos que sean percibidas por nadie.

```txt
User-agent: *
Disallow: /private/
```

Esto es un ejemplo de robots.txt, donde se permite cualquier user-agent (por lo tanto cualquier bot), y no se permite acceder a la ruta/directorio /private/.



---
# {{References}}