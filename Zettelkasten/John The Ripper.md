 05-09-2024 17:19
Status: #idea
Tags: [[Cracking Passwords]], [[Exploiting]], [[Brute Forcing]]

# John The Ripper

John The Ripper, al igual que [[Hydra]], es una herramienta que nos permitirá realizar ataques de fuerza bruta para descifrar contraseñas.

Nos permite realizar ataques de diccionario, ataques basados en patrones, o basados en reglas.

Además, al igual que Hydra, tiene paralelismo, lo que nos permitirá hacer peticiones concurrentes y paralelas para conseguir una mayor velocidad y fiabilidad de nuestro ataque.

John se centra más en el componente de contraseñas, o archivos cifrados, mientras que Hydra en servicios de autenticación, como pueden ser SSH, Telnet, etc.
## Usos de John

- Comando básico para ficheiro hash2.txt con hash dentro: john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hash2.txt 

- Buscar que formatos de md5 hai para pasarlle a john: john --list=formats | grep -iF "md5"

Para crackear contraseñas de Windows usaremos el formato nt, con el siguiente comando: 
- john --wordlist=/usr/share/wordlists/rockyou.txt --format=nt hash5.txt

Para crackear contraseñas de Linux necesitaremos saber el contenido del fichero /etc/shadow y /etc/passwd, para posteriormente usando el comando unshadow conseguir un txt con un hash que john pueda forzar:
- unshadow passwd shadow > unshadowed.txt
- Usaremos el formato sha512crypt, ya que es el que se usa en Linux para las contraseñas: john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt

Si john non nos da directamente a hash crackeada podemos facer un: john --show ficheiro.txt, e saliranos o resultado que encontrou.

Para usar o modo single, no que por ejemplo, sabendo o nombre de usuario, poderíamos probar varias variacións que faría John usaremos este comando: 
- john --single --format=raw-md5 hash7.txt, sendo o contido de hash7.txt o nombre de usuario e o hash con dous puntos no medio, así: 
	- Joker:7bf6d9bb82bed1302f331fc6b816aada

Para crackear ficheros zip podemos usar zip2john, y posteriormente usar el hash que nos generará este script:
- zip2john file.zip > zip_hash.txt
- john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt

Para crackear ficheros rar podemos usar rar2john, y unrar para descomprimir el fichero .rar:
- rar2john file.rar > rar_hash.txt
- john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt








---
# {{References}}