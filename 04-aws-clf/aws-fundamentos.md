# AWS — Primeros pasos

## ¿Qué es la nube?

Antes de AWS las empresas compraban servidores físicos — proceso que tardaba semanas
y costaba miles de dólares. AWS resuelve esto con un modelo de alquiler por horas:
el servidor está disponible en 2 minutos y dejas de pagar cuando lo apagas.

Tres características definen cloud computing:
- On-demand — recursos disponibles sin planificación previa
- Escalabilidad — subes o bajas capacidad en minutos
- Pago por uso — pagas exactamente lo que consumes

## Modelos de servicio

| Modelo | Qué es | Analogía | Ejemplos |
|--------|--------|----------|---------|
| IaaS | Infraestructura como servicio | Terreno vacío — tú construyes | EC2 |
| PaaS | Plataforma como servicio | Cocina equipada — tú cocinas | Elastic Beanstalk |
| SaaS | Software como servicio | Restaurante — solo comes | Gmail, Slack |

## Servicios core de AWS

| Servicio | Qué hace | Equivalente conocido |
|----------|----------|---------------------|
| EC2 | Servidores virtuales | Una computadora rentada |
| S3 | Almacenamiento de archivos | Google Drive para programas |
| RDS | Bases de datos administradas | MySQL sin instalarlo |
| VPC | Red privada en la nube | Subnetting que ya aprendiste |
| IAM | Control de usuarios y permisos | El sudo de AWS |
| Lambda | Código sin servidor | Función que se ejecuta sola |
| CloudFront | CDN global | Copias de tu sitio cerca de usuarios |
| Route 53 | DNS de AWS | El dig dentro de AWS |

## Modelo de responsabilidad compartida

AWS y tú comparten la seguridad pero en capas distintas.

AWS es responsable de: infraestructura física, hardware, red física, datacenters.

Tú eres responsable de: sistema operativo, aplicaciones, datos, configuraciones
de seguridad, permisos IAM.

La analogía exacta es un edificio de apartamentos — el dueño cuida el edificio,
tú cuidas lo que pasa dentro de tu apartamento.

## Regiones y zonas de disponibilidad

Una región es una ubicación geográfica — us-east-1 es Virginia, sa-east-1 es
São Paulo. Cada región es independiente.

Dentro de cada región hay zonas de disponibilidad — datacenters físicamente
separados pero conectados. Si uno falla, los otros siguen funcionando.

Para México la región más relevante es us-east-1 por ser la más completa y barata.

## Configuración inicial de cuenta

Pasos obligatorios al crear una cuenta AWS:

1. Activar MFA en usuario root — protege la cuenta más poderosa
2. Configurar alerta de facturación con Zero spend budget — evita cobros sorpresa
3. Crear usuario IAM para trabajo diario — nunca usar root cotidianamente
4. Activar MFA en usuario IAM — doble capa de seguridad

## EC2 — primer servidor en la nube

### Conceptos clave

Una instancia EC2 es un servidor virtual corriendo en datacenters de Amazon.
Una AMI (Amazon Machine Image) es la plantilla del SO — como un ISO de Linux.
El tipo de instancia define CPU y RAM — t3.micro es Free Tier eligible.

### Security Groups

Son el firewall de las instancias EC2. Controlan qué tráfico entra y sale
por puerto. Configuración correcta para un servidor básico:

- Puerto 22 SSH abierto solo a tu IP — no a 0.0.0.0/0
- Todo lo demás cerrado por defecto

### Par de llaves SSH

Exactamente el mismo mecanismo que usaste en Bandit y OverTheWire.
AWS te da el archivo .pem que es tu llave privada.

Flujo de conexión:
1. Descargar .pem al crear la instancia — única oportunidad
2. Copiar a WSL o VM
3. chmod 400 mi-llave-aws.pem — obligatorio
4. ssh -i ~/mi-llave-aws.pem ec2-user@IP_PUBLICA

El usuario por defecto de Amazon Linux es ec2-user.
En Ubuntu sería ubuntu, en Debian sería admin.

### Auditoría básica del servidor

Desde adentro con SSH:
`sudo ss -tulnp` — muestra todos los servicios corriendo

Desde afuera con nmap:
`nmap -Pn -sV -p- IP` — muestra solo lo que el firewall permite

La diferencia entre ambos outputs confirma que el Security Group
está funcionando correctamente. En este servidor:
- ss mostraba 6 servicios internos
- nmap solo veía puerto 22 desde fuera

### Gestión de costos

Detener una instancia la apaga pero la conserva — no genera costo de cómputo.
Terminar una instancia la elimina permanentemente.
Siempre detener cuando no se usa para no consumir horas del Free Tier.

## WSL Kali — entorno de trabajo en Windows

WSL permite usar Linux integrado en Windows sin VM.
Los archivos de Windows están en /mnt/c/ desde Linux.
Con la extensión WSL de VS Code, code . abre el editor
conectado al entorno Linux directamente.

El gestor de paquetes de Amazon Linux es yum en lugar de apt
pero el concepto es exactamente el mismo.