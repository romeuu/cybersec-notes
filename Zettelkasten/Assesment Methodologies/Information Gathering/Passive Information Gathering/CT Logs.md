21-11-2024 19:29
Status: #idea
Tags: [[Passive Information Gathering]]

# CT Logs

Los registros de CT son un tipo de registro público auditable de la emisión de certificados TLS/SSL por parte de cada autoridad de certificación. DigiCert fue la primera CA en crear un registro de CT que fue aceptado por Google en 2013.

Los registros de Certificate Transparency (CT) son importantes porque permiten a los propietarios de sitios web hacer un seguimiento de todos los certificados emitidos públicamente para sus dominios y protegerlos frente a aquellos atacantes que deseen hacerse con el control del dominio. Los registros de CT refuerzan el sistema de certificados TLS/SSL creando registros públicos auditables de la emisión de certificados. Desde 2015, Google exige a las CA que registren los certificados con validación extendida (EV) en registros de CT públicos. En abril de 2018, Google comenzó a exigir a las CA que registraran también los certificados con validación de empresa (OV) y con validación de dominio (DV) en registros de CT públicos.

Comando para buscar los CT Logs de facebook.com:

```shell-session
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
 
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com
```
---
# {{References}}