20-11-2024 19:54
Status: #project
Tags: [[Passive Information Gathering]]

# DNS

Existen varias herramientas que nos permiten obtener información sobre DNS. Entre ellas, tenemos dig, nslookup, host, dnsenum, fierce, dnsrecon, theHarvester, o servicios online que podemos usar para hacer lookups.

Tabla de comandos de dig:

![[Captura de pantalla 2024-11-20 a las 19.59.01.png]]

## DNSRecon

DNSRecon nos permite recuperar registros como A, MX, TXT, AAAA, etc para un dominio en concreto, algo muy parecido a lo que hace dig. También nos proporcionará los nameservers.

```shell
dnsrecon -d zonetransfer.me
```

## DnsDumpster

[DnsDumpster](https://dnsdumpster.com/) es una página web que nos permite recuperar información como DNSRecon o dig pero de manera muy ordenada y sencilla. Nos permite clasificar las acciones que realiza por tipo, es decir, por pasivo o activo.

Si por ejemplo tenemos un dns que queremos investigar, podremos ver que otras webs utilizan ese mismo servidor dns también.

Al igual que las otras herramientas, tendremos registros TXT, A, AAAA, MX ,etc.

Además, también nos presentará subdominios en la sección de HOSTS (A), que nos permitirá descargar también la información en un archivo xslx, o en formato html que nos permitirá ver el reporte, y descargarlo también como un png.

Al tener una imagen de los HOSTS, nos permite ver también si por ejemplo un host está protegido por cloudflare o no, etc.

## DNSEnum

Este comando, al igual que los anteriores nos dará información sobre los registros dns de cierto dominio. También podremos realizar [[Subdomain Bruteforcing]] con esta herramienta. Cabe destacar que por defecto hace bruteforcing, por lo tanto no se consideraría pasivo.

```bash
dnsenum zonetransfer.me
```








---
# {{References}}