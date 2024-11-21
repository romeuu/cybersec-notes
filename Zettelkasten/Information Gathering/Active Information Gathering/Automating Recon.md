 21-11-2024 20:43
Status: #idea
Tags: [[Active Information Gathering]]

# Automating Recon

Existen varios frameworks para recuperar información de recon de manera rápida y automática:

**FinalRecon**: Una herramienta de reconocimiento basada en Python que ofrece una variedad de módulos para diferentes tareas como la verificación de certificados SSL, recopilación de información Whois, análisis de encabezados y rastreo. Su estructura modular permite una personalización fácil para necesidades específicas.

**Recon-ng**: Un marco poderoso escrito en Python que ofrece una estructura modular con varios módulos para diferentes tareas de reconocimiento. Puede realizar enumeración DNS, descubrimiento de subdominios, escaneo de puertos, rastreo web y explotar vulnerabilidades conocidas.

**theHarvester**: Diseñado específicamente para recopilar direcciones de correo electrónico, subdominios, hosts, nombres de empleados, puertos abiertos y encabezados de diferentes fuentes públicas como motores de búsqueda, servidores PGP y la base de datos SHODAN. Es una herramienta de línea de comandos escrita en Python.

**SpiderFoot**: Una herramienta de automatización de inteligencia abierta de código abierto que se integra con diversas fuentes de datos para recopilar información sobre un objetivo, incluyendo direcciones IP, nombres de dominio, direcciones de correo electrónico y perfiles de redes sociales. Puede realizar búsquedas DNS, rastreo web, escaneo de puertos y más.

**OSINT Framework**: Una colección de diversas herramientas y recursos para la recopilación de inteligencia de fuentes abiertas. Cubre una amplia gama de fuentes de información, incluyendo redes sociales, motores de búsqueda, registros públicos y más.

## FinalRecon

```shell-session
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
pip3 install -r requirements.txt
chmod +x ./finalrecon.py
./finalrecon.py --help
```

```shell-session
./finalrecon.py --headers --whois --url http://inlanefreight.com
```

Con este comando, podremos ejecutar finalrecon, haciendo que nos muestre las headers y la información de whois de nuestro objetivo.

---
# {{References}}