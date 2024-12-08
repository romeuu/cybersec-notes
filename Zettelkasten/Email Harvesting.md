08-12-2024 20:48
Status: #idea
Tags: [[Passive Information Gathering]]

# Email Harvesting

La principal herramienta que usaremos para email harvesting será theHarvester.

## theHarvester

Diseñado específicamente para recopilar direcciones de correo electrónico, subdominios, hosts, nombres de empleados, puertos abiertos y encabezados de diferentes fuentes públicas como motores de búsqueda, servidores PGP y la base de datos SHODAN. Es una herramienta de línea de comandos escrita en Python.

Comando básico para usarlo con google y linkedin, [listado de sources](https://github.com/laramies/theHarvester):

```shell
theHarvester -d hackersploit.org -b google, linkedin
```

Comando con google, linkedin, yahoo, dnsdumpster, duckduckgo y crtsh:

```shell
theHarvester -d hackersploit.org -b google,linkedin,yahoo,dnsdumpster,duckduckgo,crtsh
```

Comando general:

```shell
theHarvester -d hackersploit.org
```

En caso de que queramos buscar por nombre de compañía en vez de dominio, pondríamos el nombre de la compañía en el parámetro -d, como se muestra en el siguiente comando:

```shell
theHarvester -d HackerSploit
```





---
# {{References}}