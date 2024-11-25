 25-11-2024 18:54
Status: #idea
Tags: 

# SQL Injections

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



---
# {{References}}