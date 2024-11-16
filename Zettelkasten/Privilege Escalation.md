03-11-2024 18:59
Status: #idea
Tags:

# Privilege Escalation

**==find / -user root -perm /4000==**

Si quieres quitar los mensajes de permission denied: **==find / -user root -perm /4000 2>dev/null==**

Con este comando podremos saber que ficheros tienen permisos SUID.

Existe GTFOBins (https://gtfobins.github.io/), que es una página que nos da ayuda a la hora de hacer bypass a restricciones de seguridad con los binarios que se encuentren con el comando anterior.

sudo -l: Este comando nos permitirá identificar que puede correr nuestro usuario de manera privilegiada, posteriormente, lo que nos proporcione podremos buscarlo en GTFOBins.



---
# {{References}}