 20-09-2024 21:14
Status: #idea
Tags: [[Bug Bounty]]

# Información de IPs

Existen páginas como https://bgp.he.net/ que nos permiten saber información adicional de una IP, encontrando así el AS (sistema autónomo), que es una red o grupo de redes bajo un control común y una política de enrutamiento compartida. 

Con el AS podremos saber cuantas IPs tiene esa empresa, y tener un poco más de información de como está organizada la estructura de red.

Por otra parte, si queremos información rápida sobre una ip, siempre podemos hacer un curl de 
ipinfo.io/IP, y esto nos dará la información sobre nuestra IP a investigar, por ejemplo:

curl ipinfo.io/151.101.193.140

También, si queremos seguir indagando y tener información del AS, podremos usar Arin (https://whois.arin.net/), que nos dará también información extra.


---
# {{References}}