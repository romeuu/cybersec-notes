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
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://83.136.255.173:59036/FUZZ
```

## Extensiones

Podemos usar ffuf para hacer fuzzing de extensiones usando una seclist de extensiones y poniéndolo al final del nombre del fichero que buscamos.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```


## Page Fuzzing

También podemos hacer lo mismo pero para el nombre de la página, si estamos convencidos de que sabemos la extensión.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php
```

## Recursividad




---
# {{References}}