20-11-2024 20:06
Status: #idea
Tags: [[Active Information Gathering]]

# Subdomain Bruteforcing

Para hacer enumeración de subdominios hay varias herramientas. Entre ellas destacamos DNSEnum, que nos permitirá hacer enumeración de registros DNS, [[DNS Zone Transfers]], Bruteforcing de subdominios, Google Scraping, Reverse Lookups, y WHOIS Lookups.

## DNSEnum

Aquí tenemos un ejemplo común:

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

Explicación:
 - Con la opción --enum, presentamos nuestro objetivo.
 - Con la opción -f, ponemos la wordlist que queremos usar.
 - Y con la opción -r, activamos recursividad, que quiere decir que si dnsenum encuentra un subdominio, intentará buscar subdominios de este subdominio.

Para hacer bruteforcing con gobuster y **si es un puerto distinto al 80, tendremos que usar vhosts**, por lo tanto tendrás que usar este comando:

```bash
gobuster vhost -u http://inlanefreight.htb:32368 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

Si encuentras subdominios, podrás hacer lo mismo, pero para intentar buscar subdominios dentro de un subdominio, por ejemplo:

```bash
gobuster vhost -u http://web1337.inlanefreight.htb:32368 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```



---
# {{References}}