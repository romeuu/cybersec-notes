21-11-2024 19:32
Status: #idea
Tags: [[Active Information Gathering]]

# Fingerprinting

El fingerprinting se centra en extraer detalles técnicos sobre las tecnologías que alimentan un sitio o una aplicación web. Al igual que una huella dactilar identifica a una persona, las firmas digitales de servidores web, sistemas operativos y componentes de software pueden revelar información crítica sobre la infraestructura de un objetivo y sus posibles puntos débiles en materia de seguridad. Este conocimiento permite a los atacantes adaptar los ataques y explotar las vulnerabilidades específicas de las tecnologías identificadas.

Hay distintas técnicas de fingerprinting:
- Banner Grabbing.
- Analizar cabeceras HTTP.
- Búsqueda de respuestas específicas.
- Analizar contenido de la página.


## Banner Grabbing

Con un simple curl obtendremos el banner que nos devuelve el servidor.

```shell-session
curl -I inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

En este caso, podemos ver como el servidor es Apache/2.4.41, específicamente la versión de Ubuntu. 

También podemos hacer una llamada a la página raíz, para ver si hace redirects, por ejemplo, 
llamando al dominio con https://, o por ejemplo, a https://www.dominio.com.

```shell-session
curl -I https://inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```
## Wafw00f

Con esta herramienta podremos detectar los distintos WAFs que tenga nuestro objetivo. Para instalar wafw00f, se puede hacer con el siguiente comando:

```shell-session
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

El funcionamiento es sencillo, solo tendremos que pasar el dominio a wafw00f de la siguiente manera:

```shell-session
wafw00f inlanefreight.com


[*] Checking https://inlanefreight.com
[+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
[~] Number of requests: 2
```

Y tan sencillo como eso, nos dará el WAF.

---
# {{References}}