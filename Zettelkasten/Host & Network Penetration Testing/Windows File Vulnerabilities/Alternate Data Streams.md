29-03-2025 20:47
Status: #idea
Tags: [[Windows File Vulnerabilities]]

# Alternate Data Streams

Alternate Data Streams (ADS) es un atributo NTFS y fue diseñado para dar compatibilidad con MacOS HFS (Hierarchical File System).

Cualquier fichero creado en un sistema de archivos NTFS tendrá dos ramas o streams:

- **Data Stream**: El stream por defecto que tiene el contenido del archivo.
- **Resource Stream**: Normalmente contiene la metadata del fichero.

Se puede usar ADS para guardar código malicioso o ejecutables para evadir la detección.

Esto lo podríamos hacer guardando el payload en la metadata de un fichero legítimo.

Esta técnica es usada para evitar AVs y herramientas de análisis estático.

## Funcionamiento

Podríamos crear un fichero test.txt, y ocultar un fichero dentro de este mismo fichero de la siguiente manera:

```cmd
notepad test.txt:secret.txt
```

Si abrimos el notepad, y guardamos, cuando abramos test.txt no podremos ver el contenido del fichero secret.txt.

Para poder volver a ver el contenido, tendríamos que usar el mismo comando de antes.


---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
