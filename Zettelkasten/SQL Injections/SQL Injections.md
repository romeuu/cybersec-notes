 25-11-2024 18:54
Status: #idea
Tags: 

# SQL Injections

- [[#SQL Injection (SQLi)|SQL Injection (SQLi)]]
- [[#Syntax Errors|Syntax Errors]]
- [[#Tipos de SQL Injections|Tipos de SQL Injections]]
- [[#Authentication Bypass|Authentication Bypass]]
	- [[#Authentication Bypass#Operador OR|Operador OR]]
	- [[#Authentication Bypass#Ejercicio Resuelto|Ejercicio Resuelto]]
	- [[#Authentication Bypass#Usando comentarios|Usando comentarios]]
		- [[#Usando comentarios#Otro ejemplo|Otro ejemplo]]


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

Para confirmar que este panel es vulnerable, tendremos que añadir algunas de estas payloads, siendo la primera columna el caracter a usar, y la segunda su traducción en la URL (URL encoding):

`'`: `%27`
`"`: `%22`
`#`:`%23`
`;`:`%3B`
`)`:`%29`

Tendremos que usar el URL encoding en caso de que queramos/tengamos que probar a hacer la payload en peticiones GET, por ejemplo, si no tuviésemos el input para probar directamente.

En este caso vemos que si añadimos un apóstrofe sencillo, cambia el funcionamiento y salta un error.

![[Pasted image 20241126191744.png]]

Y nos da un error de sintaxis, que se debe a que no están bien cerrados los apóstrofes. Y aquí es donde entra en juego el operador OR.

### Operador OR

Una payload que serviría en este caso sería la siguiente:

```sql
admin' or '1'='1
```

Donde ponemos una condición que siempre será true (1=1) y otra que sabemos que es true en este caso, que sería el username igual a admin, quedando nuestra query así:

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```

Por lo tanto el resumen sería:

- Si el nombre de usuario es admin
	OR
- Si 1=1 devuelve true (que siempre es true)
	AND
- Si la contraseña es 'something'

Aquí tenemos un diagrama que lo explica:

![[Pasted image 20241126192024.png]]
Esto nos terminará devolviendo true, y permitiéndonos entrar en el panel de administración.

> [!NOTA]
> La query que hemos usado arriba es una de las muchas que podremos ver en  [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass)
> 

### Ejercicio Resuelto

Se nos presenta este panel de administración, que como podemos intuir, es vulnerable a inyecciones SQL. Para afirmar esto, probaremos a introducir caracteres como ' o ", y comprobar si nos devuelve algún mensaje de error.

![[Captura de pantalla 2024-11-26 a las 19.31.11.png]]

Efectivamente, si introducimos tom' veremos como nos salta un syntax error.

![[Captura de pantalla 2024-11-26 a las 19.32.38.png]]

A partir de aquí tendremos que probar payloads y ver como va reaccionando la query mostrada por pantalla.

Quedando el payload final así: 

```
tom' OR '1'='1
```


### Usando comentarios

En esta sección vamos a ver como usar comentarios para ayudarnos a crear payloads más complejas.

Como cualquier otro lenguaje, SQL nos permite hacer comentarios. Los comentarios son útiles por si queremos dejar una parte de una query comentada, y que así no se ejecute, o también para explicar que hace una query.

```shell-session
mysql> SELECT username FROM logins; -- Selects usernames from the logins table 

+---------------+
| username      |
+---------------+
| admin         |
| administrator |
| john          |
| tom           |
+---------------+
4 rows in set (0.00 sec)
```

También podremos usar el símbolo `#`:

```shell-session
mysql> SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)
```

¿Y como podremos usar esto para hacer un authentication bypass?

Pues dejando nuestra query de la siguiente manera:

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```

Consiguiendo así que todo lo que venga después de admin no se ejecute, y esto nos permitiría logearnos de manera instantánea.

#### Otro ejemplo

![[Pasted image 20241126194104.png]]

SQL permite el uso de paréntesis para permitir que se haga check de condiciones antes que otras.

En este ejemplo en concreto, vemos que se asegura de que el id siempre sea mayor que 1, también vemos que la contraseña está hasheada, lo que no nos permitirá usar este campo para intentar la inyección.

Si intentamos entrar con credenciales válidas de admin no nos dejará, ya que el id es 1:

![[Pasted image 20241126194252.png]]

Si intentamos entrar con el usuario de tom, veremos que si que nos deja, ya que la id es mayor que 1:

![[Pasted image 20241126194337.png]]

Y como podríamos hacer para entrar como admin? Pues haciendo una inyección con comentarios y cerrando el paréntesis antes del AND:

```
admin')--
```

Dejándonos entrar en el panel correctamente:

![[Pasted image 20241126194436.png]]

#### Ejercicio Resuelto

Se nos presenta con este panel de administración que ejecuta la query y se nos pide que entremos con el usuario con id 5:

![[Captura de pantalla 2024-11-26 a las 20.13.21.png]]

```
SELECT * FROM logins WHERE (username='tom' AND id > 1) AND password = 'd41d8cd98f00b204e9800998ecf8427e';
```

Podríamos ignorar la instrucción del id 5 y entrar con el usuario tom, pero este no tiene el id 5.

Por lo tanto tendremos que encontrar la manera de cerrar la variable username y hacerle ver a la query que somos el usuario con id 5.

Esto lo podemos conseguir con la payload: 

```
' OR id = 5) -- '
```

Dejando la query final así:

```
SELECT * FROM logins WHERE (username='' OR id = 5) -- '' AND id > 1) AND password = 'd41d8cd98f00b204e9800998ecf8427e';
```


### Union Clause



---
# {{References}}