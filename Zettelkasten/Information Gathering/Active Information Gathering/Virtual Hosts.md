 20-11-2024 20:46
Status: #idea
Tags: [[Active Information Gathering]]

# Virtual Hosts

Un **virtual host** es una configuración en un servidor web que permite que múltiples sitios web diferentes se alojen en el mismo servidor. Cada sitio web tiene su propio nombre de dominio y puede tener diferentes configuraciones y contenido. Los virtual hosts permiten que un solo servidor maneje múltiples dominios sin necesidad de tener un servidor físico separado para cada uno.

### Usando Gobuster para Descubrir Virtual Hosts

Gobuster es una herramienta poderosa que no solo se usa para la fuerza bruta de directorios y archivos, sino también para descubrir virtual hosts. Aquí te explico cómo hacerlo:

1. **Identificar el Target**: Primero, necesitas identificar la dirección IP del servidor web que quieres analizar. Puedes hacer esto utilizando DNS lookups o otras técnicas de reconocimiento.

    ```
    dig +short inlanefreight.htb
    ```

1. **Preparar la Wordlist**: Prepara una lista de palabras (wordlist) que contenga posibles nombres de virtual hosts. Puedes usar una lista precompilada como SecLists o crear una personalizada basada en la industria de tu objetivo, convenciones de nomenclatura, etc.
    
3. **Ejecutar Gobuster para Virtual Hosts**: Una vez que tengas la IP del target y la wordlist, puedes ejecutar Gobuster para intentar descubrir virtual hosts. La sintaxis general del comando es:

    ```
    gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
    ```

	Por ejemplo, si tu IP de destino es `10.129.53.14` y tu wordlist se llama `vhosts.txt`, el comando sería:
 
    ```
    gobuster vhost -u http://10.129.53.14 -w vhosts.txt --append-domain
    ```


### Ejemplo Completo

Supongamos que tienes una IP de destino `10.129.53.14` y una wordlist llamada `vhosts.txt`. El comando completo sería:

sh

Copy

```
gobuster vhost -u http://10.129.53.14 -w vhosts.txt --append-domain
```

### Explicación del Comando

- `gobuster vhost`: Especifica que estás usando Gobuster para descubrir virtual hosts.
- `-u http://<target_IP_address>`: Especifica la URL del target.
- `-w <wordlist_file>`: Especifica la ruta a la wordlist que contiene los posibles nombres de virtual hosts.
- `--append-domain`: Añade el dominio al final de cada palabra en la wordlist.





---
# {{References}}