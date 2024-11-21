21-11-2024 20:00
Status: #idea
Tags: [[Active Information Gathering]]

# Web Crawling

Crawling, o spidering, es el término que se utiliza para recorrer sistemáticamente todos los links de una página web para así conseguir formar un mapa, o sitemap, que nos permita conocer mejor la estructura de nuestro objetivo.

Existen crawlers que recorren nuestras webs de manera autónoma, como por ejemplo, los bots de indexación de Google.

![[Pasted image 20241121200142.png]]

Hay muchos crawlers que te permiten realizar un crawleo rápido de tu objetivo, entre ellos tenemos:

- Burp Suite Spider
- OWASP Zap (Zed Attack Proxy)
- Scrapy
- Apache Nutch
- ReconSpider


## Burp y ZAP

Tanto Burp como Zap ya vienen instalados en Parrot o Kali, así que no te tienes que preocupar de la instalación.


## Scrapy

Para instalar Scrapy, necesitaremos ejecutar este comando:

```shell-session
pip3 install scrapy
```

## ReconSpider

Para instalar ReconSpider, necesitaremos instalar scrapy antes, ya que es un módulo que se requiere.

```shell-session
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip

unzip ReconSpider.zip 
```

```shell-session
python3 ReconSpider.py http://inlanefreight.com
```

Después de ejecutar ReconSpider, los resultados se guardarán en el archivo results.json, donde se estructurarán los datos encontrados en la web, por ejemplo:

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```

---
# {{References}}