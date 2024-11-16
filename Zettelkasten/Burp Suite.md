 05-08-2024 21:30
Status: #idea
Tags: [[Web Utilities]]

# Burp Suite

Secciones de Burp Suite:

- **[[Intruder]]**: Se usa para ataques de fuerza bruta con distintas configuraciones, como Sniper, Battery Ram, etc. El más común es Sniper, en el que se usa una lista con contraseñas, por ejemplo, sabiendo el nombre de usuario que se quiere emplear. El tamaño de las respuestas es el que nos indicará si hemos conseguido una contraseña válida o no, ya que varía según lo que devuelva la página web.
- **Repeater**: El repeater nos permite modificar peticiones que interceptemos, o ya hayamos hecho. Pudiendo así editar headers, queries, etc, y viendo la respuesta que nos devuelve en el panel de "Response" a la derecha.
- **Decoder**: El decoder es una sección que nos permite encodear y decodear (como su nombre indica) diferentes cadenas de texto que nosotros enviemos. Es útil, por ejemplo, para hacer decode de strings en base64, etc.
- **Comparer**: El comparer nos permite comparar lo que le enviemos, por ejemplo, podríamos comparar dos respuestas a peticiones, y a ver las diferencias que hay entre ellas.




---
# {{References}}