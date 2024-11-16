 14-10-2024 19:24
Status: #idea
Tags: [[Burp Suite]]

# Intruder

Se usa para ataques de fuerza bruta con distintas configuraciones, como Sniper, Battery Ram, etc. 

## Sniper

Se usa una lista con contraseñas, por ejemplo, sabiendo el nombre de usuario que se quiere emplear, ya que este ataque, prueba el ataque uno a uno, es decir, prueba las contraseñas una a una. 

Si tienes pocas contraseñas que se prueben, podemos mirar el tamaño de las respuestas ya que es el que nos indicará si hemos conseguido una contraseña válida o no, ya que varía según lo que devuelva la página web.

Pero si tienes muchas contraseñas a probar, podremos hacer un "Define extract grep item", ver que tags usa la respuesta donde varía nuestra web, por ejemplo, "Contraseña incorrecta", y así nos saldrán filtradas nuestras respuestas.

![[Pasted image 20241014192708.png]]

![[Pasted image 20241014192741.png]]

Así podremos ver que, en este caso, la contraseña password es una contraseña correcta, ya que como vemos, no existe la respuesta de "Username and/or password incorrect.".

## Battery Ram: 

Este ataque coloca la misma carga útil en todas las posiciones de carga útil definidas simultáneamente. Utiliza un único conjunto de cargas útiles.

El número total de peticiones generadas en el ataque es el número de cargas útiles en el conjunto de cargas útiles.

El battery ram es útil cuando un ataque requiere que la misma entrada sea insertada en múltiples lugares dentro de la petición. Por ejemplo, un nombre de usuario dentro de una cookie y un parámetro del cuerpo.

![[Pasted image 20241014193512.png]]

Como se puede ver, usamos como payloads tanto el nombre de usuario como la contraseña.

Battery Ram funciona en paralelo por así decirlo, imagínate que la primera palabra de tu lista a cargar es "Spring2017", pues usará esta palabra para los parámetros que necesites en la petición.

## Pitchfork

Este ataque itera a través de un conjunto de carga útil diferente para cada posición definida. Las cargas útiles se colocan en cada posición simultáneamente. Por ejemplo, las tres primeras peticiones serían:

Solicitud 1:

Posición 1 = Primera carga útil del conjunto 1.
Posición 2 = Primera carga útil del conjunto 2.

Segunda solicitud:

Posición 1 = Segunda carga útil del conjunto 1.
Posición 2 = Segunda carga útil del conjunto 2.

Petición tres:

Posición 1 = Tercera carga útil del conjunto 1.
Posición 2 = Tercera carga útil del conjunto 2.

El número total de peticiones generadas en el ataque es el número de cargas útiles en el conjunto de cargas útiles más pequeño.

El ataque Pitchfork es útil cuando un ataque requiere insertar datos diferentes pero relacionados en varios lugares de la solicitud. Por ejemplo, colocar un nombre de usuario en un parámetro, y un número de identificación conocido correspondiente a ese nombre de usuario en otro parámetro.

## Cluster Bomb Attack

Definición de Portswigger:

Este ataque itera a través de un conjunto de carga útil diferente para cada posición definida. Los payloads se colocan de cada conjunto sucesivamente, de forma que se prueban todas las combinaciones de payloads. Por ejemplo, las tres primeras peticiones serían:

Primera petición:

Posición 1 = Primera carga útil del conjunto 1.
Posición 2 = Primera carga útil del conjunto 2.
Segunda solicitud:

Posición 1 = Primera carga útil del conjunto 1.
Posición 2 = Segunda carga útil del conjunto 2.
Petición tres:

Posición 1 = Primera carga útil del conjunto 1.
Posición 2 = Tercera carga útil del conjunto 2.
El número total de peticiones generadas en el ataque es el producto del número de payloads en todos los conjuntos de payloads definidos - esto puede ser extremadamente grande.

El ataque Cluster bomb es útil cuando un ataque requiere insertar datos desconocidos o no relacionados en varios lugares de la petición. Por ejemplo, al adivinar un nombre de usuario y una contraseña.

El ataque Cluster Bomb nos va a permitir usar las dos payloads esperando una por la otra, es decir, si tienes dos listas este será el orden de peticiones:

Lista 1: 
- test
- admin
- password
- 1234

Lista 2:
- 1234
- 4321
- test
- admin

Primera petición:

- test:1234

Segunda petición:

- test:4321

Tercera petición:

- test:test

Cuarta petición:

- test:admin

Quinta petición: 

- admin:1234

Y así sucesivamente hasta que se consuman las dos listas.


---
# {{References}}