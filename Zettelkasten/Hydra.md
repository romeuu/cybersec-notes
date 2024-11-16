 15-10-2024 20:16
Status: #idea
Tags: [[Cracking Passwords]], [[Exploiting]], [[Brute Forcing]]

# Hydra

Hydra es una herramienta de prueba de penetración que se utiliza para realizar ataques de fuerza bruta, en servicios de autenticación remota, por ejemplo, SSH, HTTP, Telnet, etc.

Hydra nos permite realizar ataques avanzados y flexibles, donde podremos usar listas y diccionarios para el usuario y la contraseña, o si ya sabemos el usuario, para la contraseña únicamente. 

También posee paralelización, es decir, se hacen varios intentos de autenticación de manera simultánea, lo que mejorará la velocidad y la eficiencia de nuestros ataques.

==**hydra ssh://ip -L users.txt -P locks.txt**==

Con este comando podremos hacer bruteforce a un servicio ssh con la ip requerida, con el listado de usuarios users.txt, y contraseñas locks.txt.

Si por otro lado ya sabemos el usuario, podríamos usar este comando para forzar la contraseña:

**==hydra -l lin -P locks.txt 10.10.236.154 ssh -t 4==**

---
# {{References}}