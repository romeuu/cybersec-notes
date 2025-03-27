27-03-2025 18:28
Status: #idea
Tags: [[Windows Privilege Escalation]]

# Access Token Impersonation

Los Access Token son un elemento central del proceso de autenticación de Windows, y son creados y manejados por el proceso LSASS (Local Security Authority Subsystem Service).

Para entender lo que son estos token, podemos pensar en ellos como una key temporal, similar a una cookie web, que nos proporcionará acceso al sistema y a los recursos en red que existan en este.

Estos token son generados por el proceso winlogon.exe cada vez que un usuario se autentica. Estos tokens contienen los privilegios asociadas a la cuenta que se utilice para logearse. Posteriormente, este token se adjunta al proceso userinit.exe, que se encargará de que cada proceso tenga una copia de este token y se ejecute con los permisos correspondientes.

Los privilegios necesarios para que se pueda hacer una suplantación serían:

- **SeAssignPrimaryToken**: Esto permite a un usuario suplantar tokens.
- **SeCreateToken**: Permite crear tokens arbitrarios con permisos de administrador.
- **SeImpersonatePrivilege**: Esto permite al usuario crear un proceso bajo el contexto de otro usuario, normalmente con permisos elevados.



---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
