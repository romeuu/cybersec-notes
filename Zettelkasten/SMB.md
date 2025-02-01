09-10-2024 21:05
Status: #idea
Tags: [[EJPTv2]]

# SMB

El puerto común de SMB es 445. Por otra parte, netbios-ssn, estará en el 139, que es el encargado de controlar la sesión del SMB, ahora mismo, netbios-ssn es una capa de transporte antigua, pero que se sigue usando en algunos equipos.

Normalmente, con nmap podremos saber los puertos en los que se encuentran estos servicios, pero también, la versión que se está usando (flag -sV).

Con la flag -sC de nmap podremos ver también información adicional, tal como el dominio de NetBIOS, la versión de producto, el nombre del equipo, etc. Esta flag se encarga de ejecutar scripts por defecto que tiene nmap, [aquí puedes encontrar una lista](https://nmap.org/nsedoc/categories/default.html)).  [[nmap#SMB Nmap Scripts|Para el propósito de SMB, hay scripts como el smb-os-discovery que nos darán información muy útil, como el OS, Workgroup, System time, o información de seguridad, como el nivel de privilegios de la cuenta usada, la autenticación, etc.]]

> [!Explicación de página de nmap]
> 	Intenta determinar el sistema operativo, el nombre del ordenador, el dominio, el grupo de trabajo y la hora actual a través del protocolo SMB (puertos 445 o 139). Esto se hace iniciando una sesión con la cuenta anónima (o con una cuenta de usuario adecuada, si se proporciona una; probablemente no haya diferencia); en respuesta al inicio de sesión, el servidor devolverá toda esta información.

Para montar la unidad SMB en un equipo Windows, tendremos que usar una unidad compartida en red, daremos click derecho sobre la opción de network en el menú lateral e indicaremos la ip del servicio SMB. Una vez hecho esto, podremos escoger que carpeta queremos montar de este servicio, y una vez se finalice el asistente de configuración, tendremos acceso a este equipo.

![[Pasted image 20241009211837.png]]

Una vez tengamos hecho esto, y si no hay protección, podremos listar usuarios dentro de este servicio entrando en C:/Users, y tener total libertad.

Posteriormente, podremos usar el comando **==`net use * /delete`==** para borrar las unidades que estén montadas.

![[Pasted image 20241009212319.png]]

Para montar una unidad desde la línea de comandos, usaremos el comando 
**==`net use Z: \\10.4.17.133\c$ smbserver_771 /user:administrator`==**, donde smbserver_771 será la contraseña, c$ será el disco duro C:, y el user sería administrator.

---
# {{References}}