16-08-2024 20:53
Status: #idea
Tags: [[Exploiting]]

# SQL Injection

As inyeccións SQL permítennos acceder á base de datos desde a páxina web vulnerable. Si por ejemplo, temos unha URL como esta: `https://insecure-website.com/products?category=Gifts`, poderíamos facer unha inyección das seguintes maneiras: 

Query hipotética que hai nesta web: `SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1`

`https://insecure-website.com/products?category=Gifts'+OR+1=1--`
`https://insecure-website.com/products?category=Gifts'--`

Esto permitiríannos acceder a todos os productos que haxa dentro da base de datos, pasando algúns filtros que veñan despois desta cláusula.

Pode ser máis dañino, si por exemplo se usa en páginas de login, xa que se por exemplo, usamos o usuario administrator, teríamos que indicar unha contraseña válida para entrar, pero si facemos una inyección SQL da seguinte maneira:

administrator'--

Conseguiríamos entrar con calquera contraseña, xa que se comentaría a comprobación da contraseña.

Hai varios tipos de SQL injections, entre eles hai un ataque que se chama UNION SELECT attack, que nos permite conseguir datos de outras tablas nunha misma query.

Para conseguir esto temos que saber o número de columnas que ten a tabla na que estamos ahora mismo, xa que os UNION necesitan que o tipo de datos da columna sea exactamente o mismo que da columna que se quere conseguir, e que a tabla que queremos conseguir teña o mismo número de columnas que a tabla que se está a ver.

Por ejemplo, esta URL: `https://0aae00de04ae790e8428904300b10085.web-security-academy.net/filter?category=Accessories'UNION SELECT NULL, NULL, NULL --`. Podemos ver como se está poñendo na URL: `category=Accessories' UNION SELECT NULL, NULL --`, se vamos poñendo union select null e nos devolve errores, quere decir que temos que seguir añadindo nulls a query, xa que non estamos acertando o número de columnas da tabla. É dicir, poderíamos probar: category=Accessories' UNION SELECT NULL --, category=Accessories' UNION SELECT NULL, NULL --, e category=Accessories' UNION SELECT NULL, NULL, NULL --, así infinitamente hasta que vaia.

Aquí temos un cheatsheet de PortSwigger para este tipo de ataque: https://portswigger.net/web-security/sql-injection/cheat-sheet, donde se poden ver os distintos tipos de base de datos que hai, e como varían entre eles (Oracle, MariaDB...)

---
# {{References}}