 21-11-2024 20:06
Status: #idea
Tags: [[Active Information Gathering]]

# Well-Known URIs

Este es un directorio común que utilizan la mayoría de webs, ya que es un estándar definido RFC 8615.

En este directorio, podremos encontrar diferentes archivos:

| **URI Sufijo**               | **Descripción**                                                                                                            | **Estado**  | **Referencia**                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ----------- | --------------------------------------------------------------------------------------- |
| security.txt                 | Contiene información de contacto para investigadores de seguridad para reportar vulnerabilidades.                          | Permanente  | RFC 9116                                                                                |
| /.well-known/change-password | Proporciona una URL estándar para dirigir a los usuarios a una página de cambio de contraseña.                             | Provisional | https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri |
| openid-configuration         | Define los detalles de configuración para OpenID Connect, una capa de identidad sobre el protocolo OAuth 2.0.              | Permanente  | http://openid.net/specs/openid-connect-discovery-1_0.html                               |
| assetlinks.json              | Se utiliza para verificar la propiedad de activos digitales (por ejemplo, aplicaciones) asociados con un dominio.          | Permanente  | https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md     |
| mta-sts.txt                  | Especifica la política para SMTP MTA Strict Transport Security (MTA-STS) para mejorar la seguridad del correo electrónico. | Permanente  | RFC 8461                                                                                |

## Web Recon

Si llamamos al endpoint openid-configuration, nos puede brindar información como endpoints relacionados con la capa de autenticación oauth2.

Por ejemplo, si llamamos a https://example.com/.well-known/openid-configuration, nos podría dar información como la siguiente:

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

Esto nos dará información útil como el issuer de los JWT que se usen, el endpoint de autorización, de token e información del usuario. 

También, información de JWKS URI, que es información del JSON Web Key Set (JWKS), detallando así las llaves criptográficas del servidor.

---
# {{References}}