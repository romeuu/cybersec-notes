08-12-2024 19:55
Status: #idea
Tags: [[Passive Information Gathering]]

# Subdomain Enumeration

La enumeración de subdominios es distinta a el [[Subdomain Bruteforcing]], ya que no estamos interactuando directamente con el objetivo. Cabe destacar, que sublist3r nos permite hacer bruteforce también con la opción --bruteforce.

Repositorio de GitHub: https://github.com/aboul3la/Sublist3r

Sublist3r usará OSINT para enumerar subdominios, por lo cual se usarán servicios como Google, Bing, Yahoo, Netcraft, VirustTotal, ThreatCrowd, DNSdumpster y ReverseDNS. Esto nos permitirá, como aclaramos antes, no interactuar con el objetivo.

Para instalar sublist3r en nuestra máquina de kali usaremos:

```shell
sudo apt-get install sublist3r
```

Comando de ejemplo, que usará google y yahoo:

```shell
sublist3r -d hackersploit.org -e google,yahoo
```

Si no especificamos el parámetro -e, se usarán todos los sources que tenga sublist3r.

Tenemos que tener en cuenta que se van a realizar muchas peticiones a Google, por lo tanto es posible que se bloqueen las peticiones en algún momento. Esto podemos burlarlo con una VPN.







---
# {{References}}