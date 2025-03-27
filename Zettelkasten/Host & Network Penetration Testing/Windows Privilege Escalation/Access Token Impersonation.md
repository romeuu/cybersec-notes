27-03-2025 18:28
Status: #idea
Tags: [[Windows Privilege Escalation]]

- [[#The Incognito Module|The Incognito Module]]
# Access Token Impersonation

Los Access Token son un elemento central del proceso de autenticación de Windows, y son creados y manejados por el proceso LSASS (Local Security Authority Subsystem Service).

Para entender lo que son estos token, podemos pensar en ellos como una key temporal, similar a una cookie web, que nos proporcionará acceso al sistema y a los recursos en red que existan en este.

Estos token son generados por el proceso winlogon.exe cada vez que un usuario se autentica. Estos tokens contienen los privilegios asociadas a la cuenta que se utilice para logearse. Posteriormente, este token se adjunta al proceso userinit.exe, que se encargará de que cada proceso tenga una copia de este token y se ejecute con los permisos correspondientes.

Los privilegios necesarios para que se pueda hacer una suplantación serían:

- **SeAssignPrimaryToken**: Esto permite a un usuario suplantar tokens.
- **SeCreateToken**: Permite crear tokens arbitrarios con permisos de administrador.
- **SeImpersonatePrivilege**: Esto permite al usuario crear un proceso bajo el contexto de otro usuario, normalmente con permisos elevados.


> [!TIP] Privilegios
> Para comprobar si nuestro usuario tiene estos privilegios, podremos usar el comando **getprivs** de meterpreter.


## The Incognito Module

**Incognito** es un módulo de meterpreter que nos permite suplantar tokens de usuarios después de exploitear correctamente.

Podemos usar este módulo para listar los tokens que podemos suplantar.

```shell
load incognito
list_tokens -u
```

Esto nos dará una lista de los tokens que podremos suplantar. Si por ejemplo, tenemos el token **ATTACKDEFENSE\Administrator** listo para suplantar, solo tendremos que usar el siguiente comando:

```shell
impersonate_token "ATTACKDEFENSE\Administrator"
```

Si todo ha salido bien, deberíamos de ver que somos el usuario **ATTACKDEFENSE\Administrator**.

Si hacemos **getprivs**, puede que nos ponga que no tenemos permisos, por lo tanto, lo que tendremos que hacer es migrar a un proceso que si pueda, por ejemplo, **explorer.exe**.

```shell
pgrep explorer
```

Esto nos devolverá el PID de explorer, y podremos hacer **migrate X**, para migrar a este proceso.

> [!TIP] Si no hay tokens
>Si ves que no hay tokens que puedas suplantar, tendrás que hacer un [**potato attack**](https://jlajara.gitlab.io/Potatoes_Windows_Privesc), que nos permitirá tener un token de **NT AUTHORITY\SYSTEM**, con los privilegios asociados a esta cuenta.




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
