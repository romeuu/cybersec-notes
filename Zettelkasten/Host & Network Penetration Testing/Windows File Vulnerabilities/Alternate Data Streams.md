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




---
# Backlinks

```dataview
LIST
FROM [[]]
SORT file.name ASC
```
