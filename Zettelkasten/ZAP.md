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



---
# {{References}}