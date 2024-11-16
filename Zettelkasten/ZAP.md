 16-11-2024 10:32
Status: #idea
Tags: [[Web Utilities]]

# ZAP

Zap es una alternativa open source a Burp Suite. Tiene herramientas muy parecidas a lo que nos ofrece Burp.

## Fuzzing

Vamos a empezar por el fuzzing, o lo que sería algo como el intruder de Burp, donde proporcionando una wordlist podremos buscar directorios o ficheros en nuestro objetivo.

![[Pasted image 20241116103402.png]]

Lo que tendremos que configurar será:
- Fuzz Location
- Payloads
- Processors
- Options

### Fuzz Location

El Fuzz Location de ZAP será el equivalente al payload position del Intruder de Burp. Para poner nuestra location en una palabra de nuestra petición, daremos doble click y le daremos al botón de Add.

![[Pasted image 20241116103540.png]]

### Payloads

Los payloads son iguales a Intruder, solo que no son tan avanzadas. Con el botón de Add podremos añadir las payloads que necesitemos, pudiendo escoger entre 8 tipos diferentes. 

Están disponibles los siguientes tipos:
- File: Nos permite seleccionar una wordlist.
- File Fuzzers: Nos permite seleccionar wordlists que incluye ZAP.
- Numberz: Nos permite generar secuencias numéricas con increments customizados.

### Processors

Esta sección nos va a permitir hacer procesamiento de nuestras payloads. Podremos usar:

- Base64 decode/encode
- MD5 Hash
- Postfix String
- Prefix String
- SHA-1/256/512 Hash
- URL Decode/Encode
- Script

### Options

Esta sección nos permitirá ajustar opciones de nuestro fuzzing, podremos ajustar el número de hilos que se usan para que el escaneo funciona más rápido, por ejemplo, a 20 hilos.

Solo faltaría darle a start, y el fuzzer empezará a funcionar.


## Spider

La función spider nos va a permitir hacer crawling en toda la página web. Al finalizar el escaneo, nos mostrará las URLs que ha encontrado, y veremos en el panel lateral, la estructura de la web.

Para acceder a esta herramienta, haremos una petición a la página web en la que queremos hacer el crawl, y daremos click derecho en la petición en el historial, y seleccionaremos Attack > Spider.

Nos saldrá un diálogo en el que confirmaremos el target, y simplemente tendremos que aceptar.

![[Captura de pantalla 2024-11-16 a las 12.45.24.png]]

En esta imagen podemos ver la estructura de la web que se mencionaba anteriormente.
## Scanner





---
# References