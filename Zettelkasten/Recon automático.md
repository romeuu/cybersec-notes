 05-10-2024 17:57
Status: #idea
Tags: [[Bug Bounty]]

# Recon automático

1- Anotar wildcards de programa en un ficheiro plano, por ejemplo, wildcards. Sin incluir o asterisco do principio.
2- Pasar o contenido de este fichero por assetfinder, e acto seguido usar anew para metelo nun ficheiro de dominios (domains):

	cat wildcards | assetfinder -subs-only | anew domains

3- Ahora tendo o ficheiro dos dominios, podemos crear un de hosts pasando estos dominios por httprobe e ver cales responden:

	cat domains | httprobe -c 80 --prefer-https | anew hosts

4- (OPCIONAL) Podemos pasar un findomain para ver que dominios novos se añadiron recientemente:

	findomain -f wildcards | tee -a findomain.out

Posteriormente terás que parsear os datos do ficheiro findomain.out e eliminar o output do script, podes mirar de facelo automático no script de automatización.

5- Usa o script de Python que tes para filtrar items fora de scope e ter o ficheiro hosts_scope.

6- Pasa un fff para conseguir as headers e os bodies de todas as URLs válidas.

	cat hosts_scope | fff -d 1 -S -o roots

Esto creará unha carpeta que sea roots, onde nela se creará unha carpeta por URL, onde terás o archivo de body e headers.

7- Con estos archivos podes usar gf, e ver si hai algo interesante neles, como aws-keys, errores de php, etc.


Alternativa en Laravel:

1- Se crea fichero con wildcards dentro con el mismo formato.
2- ``cat wildcards | assetfinder -subs-only | anew domains``
3- (opcional): Si hay rango de IPs aceptado en scope, tendremos que usar amass sobre nuestro fichero de domains: ``massdns -r resolvers.txt -o S -t A domains > amass-results.txt``

Integraciones pendientes:

- Fix polling.
- Facer comando para limpiar ficheiros temporales de cada programa, podes deixalos 7 días, e despois mirar por fecha e borralos.
- **Resumen diario**: En lugar de solo enviar alertas por nuevos subdominios, podrías generar un **informe diario** que incluya información sobre todos los subdominios detectados, puertos abiertos, vulnerabilidades conocidas, y demás.
- **Integración con más herramientas de recon**: Puedes integrar más herramientas de reconocimiento, como **Subfinder**, **Amass**, **Nmap** y **Shodan**, para realizar más investigaciones automáticas sobre los objetivos.
- **Banner Grabbing**: Captura los banners de los servicios expuestos para obtener más detalles sobre las versiones y configuraciones de los servicios.
- Arreglar sendDocument de Telegram.





---
# {{References}}