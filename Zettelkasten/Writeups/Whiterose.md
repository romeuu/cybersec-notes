22-02-2025 20:11
Status: #idea
Tags: [[Writeups]]

# Whiterose

Olivia Cortez:olivi8

10.10.170.109

Se nos indica al principio que el usuario Olivia Cortez con la contraseña olivi8 serán útiles a lo largo de la máquina.

Tras un escaneo de nmap, podemos determinar que los puertos 80 y 22 están abiertos, teniendo así los servicios de SSH y Nginx activos.

```shell
nmap -sC -sV -O --osscan-guess 10.10.170.109

Starting Nmap 7.80 ( https://nmap.org ) at 2025-02-22 19:32 GMT
Nmap scan report for cyprusbank.thm (10.10.170.109)
Host is up (0.00035s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b9:07:96:0d:c4:b6:0c:d6:22:1a:e4:6c:8e:ac:6f:7d (RSA)
|   256 ba:ff:92:3e:0f:03:7e:da:30:ca:e3:52:8d:47:d9:6c (ECDSA)
|_  256 5d:e4:14:39:ca:06:17:47:93:53:86:de:2b:77:09:7d (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
MAC Address: 02:12:8D:60:9B:2F (Unknown)
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 3.8 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ )
```

Las versiones de estos servicios son OpenSSH 7.6p1 Ubuntu 4ubuntu0.7, y nginx/1.14.0 respectivamente.

El sistema operativo de la máquina es Linux.

Al acceder a la web veremos un mensaje de mantenimiento.

![[Pasted image 20250222203344.png]]

## Enumeración

### Directorios

```shell
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://cyprusbank.thm/FUZZ -ic
```

Con esta búsqueda no se ha encontrado nada, por lo tanto paso a hacer enumeración de VHOSTS.

```shell
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://cyprusbank.thm -H 'Host: FUZZ.cyprusbank.thm' -ic -mc all -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1
________________________________________________

 :: Method           : GET
 :: URL              : http://cyprusbank.thm
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.cyprusbank.thm
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: all
 :: Filter           : Response size: 57
 :: Filter           : Response words: 1
 :: Filter           : Response lines: 4
________________________________________________

www                     [Status: 200, Size: 252, Words: 19, Lines: 9]
admin                   [Status: 302, Size: 28, Words: 4, Lines: 1]
WWW                     [Status: 200, Size: 252, Words: 19, Lines: 9]
:: Progress: [4997/4997] :: Job [1/1] :: 13044 req/sec :: Duration: [0:00:01] :: Errors: 0 ::
```

Como vemos, admin llama a la atención, por lo tanto editaremos el archivo /etc/hosts, para poder acceder.

Una vez accedemos, hay un panel de administración.

![[Pasted image 20250222204508.png]]

Si probamos a usar las credenciales que nos han indicado anteriormente en la sala, obtendremos acceso al panel.

![[Pasted image 20250222205606.png]]

Los datos que vemos en el panel están censurados, por lo que no podremos ver el número de teléfono de Tyrell.

Si accedemos a la sección de mensajes, veremos una url con un parámetro interesante. Si lo cambiamos, haremos un IDOR y obtendremos las credenciales de una cuenta de administrador.


---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
