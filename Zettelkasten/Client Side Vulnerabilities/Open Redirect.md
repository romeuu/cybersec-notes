29-12-2024 13:38
Status: #idea
Tags: [[Client Side Vulnerabilities]]

# Open Redirect

Un Open Redirect ocurre cuando una aplicación web toma datos (url normalmente) de un input inseguro y redirige al usuario a este sitio web malicioso.

El impacto de esta vulnerabilidad es normalmente bajo, a no ser que se encadene a otras vulnerabilidades para escalar.

Muchas veces, los desarrolladores tendrán una whitelist de dominios a los que se podría hacer una redirección, consiguiendo así mitigar el riesgo de redirección a un site malicioso.

Unos ejemplos básicos de open redirect serían los siguientes:

https://example.com/login/?nextPage=https://google.com (PERMITIDO)
https://example.com/login/?nextPage=https://evilsite.com (DENEGADO)
https://example.com/login/?nextPage=https://evilsite.com/?google.com (PERMITIDO)

Podemos ver como en el último caso se aprovecha que google.com es un dominio que parece estar en la whitelist, y lo concatenamos a nuestra página web maliciosa para conseguir un bypass.




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
