 22-11-2024 18:17
Status: #idea
Tags: [[Active Information Gathering]]

# fuff

Fuff viene instalado de serie en la pwnbox y en Kali, pero por si lo necesitas instalar es tan sencillo como hacer:

```
apt install ffuf -y
```

## Directorios

Fuff se puede usar para hacer Fuzzing de directorios por ejemplo, como en este comando, donde necesitamos mapear FUZZ en la seclist, y FUZZ para el directorio:

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://83.136.255.173:59036/FUZZ -ic
```

## Extensiones

Podemos usar ffuf para hacer fuzzing de extensiones usando una seclist de extensiones y poniéndolo al final del nombre del fichero que buscamos.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ -ic
```

## Page Fuzzing

También podemos hacer lo mismo pero para el nombre de la página, si estamos convencidos de que sabemos la extensión.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ.php -ic
```

## Recursividad

Con fuff podremos especificar una recursividad, lo que hará que se escaneen otros directorios/rutas, dentro de los que ha encontrado, esto se puede hacer con la flag -recursion-depth, y especificando la extensión con -e.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v -ic
```


## Subdominios

Con ffuf también podremos hacer fuzzing de subdominios, usando una wordlist de subdominios y simplemente poniendo la keyword FUZZ en la url.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/ -ic
```

## VHOSTS

Para descubrir VHOSTS en una IP o dominio, podremos usar la header Host de la siguiente manera:

```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -ic


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

mail2                   [Status: 200, Size: 900, Words: 423, Lines: 56]
dns2                    [Status: 200, Size: 900, Words: 423, Lines: 56]
ns3                     [Status: 200, Size: 900, Words: 423, Lines: 56]
dns1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
lists                   [Status: 200, Size: 900, Words: 423, Lines: 56]
webmail                 [Status: 200, Size: 900, Words: 423, Lines: 56]
static                  [Status: 200, Size: 900, Words: 423, Lines: 56]
web                     [Status: 200, Size: 900, Words: 423, Lines: 56]
www1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
<...SNIP...>
```

Veremos que todas las palabras dan un 200 OK, pero lo que de verdad importa es el size de la respuesta, ya que si el vhost existe, nos devolverá un tamaño distinto.

Para esto tendremos la flag -fs, que nos permite hacer filtering por el size de nuestra respuesta, por lo tanto, en el anterior ejemplo haríamos -fs 900, y nos saldrían todas las peticiones que no tengan 900 como size de respuesta.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900 -ic
```

A partir de aquí, tendríamos que añadir nuestro nuevo subdominio a /etc/hosts para poder acceder a el.

## Fuzzing parámetros GET

Al igual que en los anteriores casos también podremos hacer fuzzing de parámetros GET en nuestro objetivo.

Pasará lo mismo que con los vhosts y necesitaremos filtrar por size, por lo tanto debemos hacer un primer escaneo y posteriormente, coger el size que nos devuelve y meterlo en la flag -fs.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx -ic
```

## Fuzzing parámetros POST

Al ser POST, no sirve enviar parámetros en la url, por lo tanto tendremos que mandar un body y poner un content type.

> [!Tip de PHP:]
> In PHP, "POST" data "content-type" can only accept "application/x-www-form-urlencoded". So, we can set that in "ffuf" with "-H 'Content-Type: application/x-www-form-urlencoded'".

Lo primero que tendremos que hacer será ver que parámetros acepta, por lo tanto haremos FUZZ de eso primero. Podrás usar un comando como este:

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx -ic
```

Como antes, tendrás que filtrar por size, y ya tendrás los parámetros que admite.

En este caso hemos visto que admite un parámetro llamado id, por lo tanto podremos hacer Fuzzing del valor de id.

## Value Fuzzing

Es común que por ejemplo, los ids sean valores numéricos y autoincrementables, aunque pueden ser uuids en aplicaciones más robustas.

Por lo tanto, podremos generar una wordlist de números enteros para probar. Se puede usar un bucle for en una secuencia y guardarlo en un txt con este comando:

```shell-session
for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

Posteriormente, lo único que nos faltaría sería usar esta wordlist y poner la keyword FUZZ en nuestra propiedad id.

```shell-session
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx -ic
```

Recuerda, tendrás que filtrar por size también.

Posteriormente podremos hacer un curl si por ejemplo encontramos un id 73, y queremos probar a ver que sale, o hacerlo directamente en ZAP/Burp.

```
curl -X POST http://admin.academy.htb:59036/admin/admin.php -d "id=73"
```


---
# {{References}}