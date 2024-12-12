10-12-2024 19:14
Status: #idea
Tags: [[Active Information Gathering]]

# DNS Zone Transfers

En ciertos casos, administradores del servidor DNS querrán copiar o transferir ficheros de zona de un servidor DNS a otro. Esto es lo que se conoce como DNS Zone Transfer.

Si está mal configurado y sin proteger, esta funcionalidad podrá ser usada por atacantes y copiar el fichero de zona del servidor de DNS primario al servidor DNS secundario.

Además, este fichero nos podrá dar una visión general de la organización de red de una empresa.

En algunos casos, se podrían encontrar direcciones internas de red en el servidor DNS de la organización.

## Procedimiento

Para hacer un zone transfer tendremos que usar la utilidad dig de la siguiente manera:

```shell
dig axfr @nameserverprincipal dominio.com
```

Tendremos que indicar el nameserver principal, que podemos obtener con utilidades como DNSEnum o DNSDumpster.

Esto nos dará una lista de subdominios que no tenían porque ser públicos, es decir, puede haber dominios que no se encuentren en otros lados más que al realizar la zone transfer.


---
# {{References}}