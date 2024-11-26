 25-11-2024 18:54
Status: #idea
Tags: 

# SQL Injections

- [[#SQL Injection (SQLi)|SQL Injection (SQLi)]]
- [[#Syntax Errors|Syntax Errors]]
- [[#Tipos de SQL Injections|Tipos de SQL Injections]]
- [[#Authentication Bypass|Authentication Bypass]]


La mayoría de aplicaciones tienen una base de datos para guardar información de lo que sucede en la aplicación. Para conseguir que la web sea dinámica, se necesita interactuar en tiempo real con la base de datos.

El flujo de funcionamiento es el siguiente:

1- El usuario hace una petición.
2- Llega a la aplicación.
3- Se traslada al back-end de la aplicación.
4- Desde el back-end de la aplicación, se contacta con la base de datos y se recupera lo solicitado.

![[Pasted image 20241125185612.png]]

## SQL Injection (SQLi)

Hay muchos tipos de inyecciones que se pueden realizar en web apps, tales como HTTP Injection, code injection y command injection, pero el más común de todos, son sin duda los SQL Injections.

Su funcionamiento es sencillo, el usuario malintencionado hará un uso de un campo input, para modificar la query final que se envía a la base de datos, devolviendo así información que el usuario no debería de ver, o obteniendo control sobre la base de datos.

Hay muchas maneras de conseguir esto, pero lo más normal es que el atacante inyecte código SQL en un input que no tiene validaciones correctas. El caso más básico de inyecciones SQL, sería usar apóstrofes (') o ("), para romper el string del back-end, y así poder inyectar la query maliciosa.

Una vez el atacante comprueba que puede hacer inyecciones SQL, tendrá que ejecutar otras queries para obtener información, o para alterar el funcionamiento de la base de datos. Normalmente, se utilizan [stacked queries](https://www.sqlinjection.net/stacked-queries/) o también con [unions](https://www.mysqltutorial.org/sql-union-mysql.aspx/).

El impacto de una inyección SQL puede ser devastador, sobre todo si los permisos en el back-end y la base de datos son muy laxos. 

- Para empezar, podríamos recuperar los datos que queríamos, obteniendo así acceso a información "protegida". Además, este tipo de vulnerabilidad puede provocar un breach de datos y contraseñas.
- Otro caso de uso sería poder acceder a cuentas de usuario sin necesitar las credenciales, ya que vulneraríamos la comprobación de contraseña con una inyección. Esto nos daría acceso a paneles de administración. Por otro lado, los atacantes también podrían leer y escribir en ficheros del back-end, lo que puede permitir la creación de back doors en nuestra aplicación, consiguiendo así control total de nuestra web.

Para prevenir este tipo de vulnerabilidad, deberemos asegurarnos de que el código de nuestra web es fuerte y seguro, además de tener permisos especificados en nuestra base de datos y tener un back-end que haga comprobaciones antes de llamar a la base de datos.

## Syntax Errors

```php
Error: near line 1: near "'": syntax error
```

Es común encontrarnos con errores de sintaxis cuando estamos haciendo payloads y probándolas. Normalmente este problema se debe a que no estamos cerrando bien los apóstrofes o hemos escrito algo mal.


## Tipos de SQL Injections

Se caracterizan por cuando y como conseguimos acceder a los datos.

![[Pasted image 20241126183711.png]]

En casos sencillos, el output será mostrado por pantalla en el front-end, pudiendo así leerlo directamente. Esto se conoce como In-band, y tiene dos tipos: **Union Based**, y **Error Based**.

Con el **Union Based** podremos especificar la localización específica para que la query vuelque los datos ahí. Mientras que la **Error Based**, se identifica cuando podemos los errores de PHP o SQL en el front-end, lo que nos permitirá crear un error SQL que nos devuelva el resultado de nuestra query.

En casos más complicados, no veremos el output en el front-end, por lo que tendremos que utilizar lógica SQL para conseguir ver el output caracter a caracter. Esto se conoce como **Blind SQL Injection**, y tiene dos tipos: **Boolean Based** y **Time Based**.

Con el tipo **Boolean Based** podremos usar statements para controlar si una página nos devuelve algo de output, por ejemplo 'original query response', si nuestra condición es true. Mientras que las inyecciones **Time Based**, podremos usar queries de SQL para por ejemplo, introducir una pequeña delay con la sentencia Sleep().

Y finalmente, en algunos casos no tendremos acceso directo al output, por lo que tendremos que dirigir el output a una localización remota, como un registro DNS, esperando recuperar el output desde ahí. Esto se conoce como inyección **Out-of-band**.

## Authentication Bypass

En el siguiente panel de administración podremos ver el funcionamiento de una inyección SQL para burlar la seguridad de este.

![[Pasted image 20241126185850.png]]

Si nos logeamos con el usuario admin y la contraseña p@ssw0rd, funcionará y podremos logearnos como admin. Pero, ¿podríamos acceder al panel sin saber la contraseña?

![[Pasted image 20241126185917.png]]

La query que se está ejecutando es la siguiente:

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';
```

Vemos como la página está recuperando los parámetros que enviamos, tanto el username como el password y usándolos directamente en la query.

Esto es una luz verde para las SQL injection, ya que este es el tipo de código que nos permitirá vulnerar la seguridad.



---
# {{References}}