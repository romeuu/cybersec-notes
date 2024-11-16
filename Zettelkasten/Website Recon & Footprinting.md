10-10-2024 19:39
Status: #idea
Tags: [[Passive Information Gathering]]

# Website Recon & Footprinting

En este apartado entrarán técnicas como DNS Lookup, para saber que IPs tiene una página web. 

Hay herramientas como **==host==** en kali que te permiten tener esta información en tu terminal directamente, si no también existen páginas web como [MxToolBox](https://mxtoolbox.com/).

Al hacer website recon, el primer punto que tenemos que atacar sería el **==robots.txt==**, ya que aquí podremos saber información acerca de la web que nos permite saber que es lo que no se quiere indexar en Google.

También es interesante ver el sitemap de la web, normalmente **==/sitemap.xml==**.

Por otra parte, **==Wappalyzer o Built With==** son extensiones que podemos usar en nuestro navegador para obtener más información de que tecnologías se están usando en la web.

Tenemos herramientas como **==whatweb==**, que viene por defecto instalado en kali. Su uso es sencillo, pondremos whatweb example.com, y automáticamente nos dará información a mayores de este sitio web, como que tecnologías o servidores se están utilizando.

Si queremos descargar una página al completo para analizar el código fuente, podremos descargar la aplicación **==httrack==** (sudo apt-get isntall webhttrack).




---
# {{References}}