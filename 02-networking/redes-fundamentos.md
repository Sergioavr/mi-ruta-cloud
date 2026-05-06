# Redes — Fundamentos completos

## El modelo OSI

El modelo OSI divide la comunicación en red en 7 capas, cada una con una responsabilidad específica.

| # | Capa | Qué hace | Ejemplos |
|---|------|----------|---------|
| 7 | Aplicación | Lo que el usuario usa directamente | HTTP, HTTPS, DNS, SSH |
| 6 | Presentación | Traduce, cifra y comprime | TLS, JPEG, ASCII |
| 5 | Sesión | Abre y mantiene conexiones | Login de sesión, RPC |
| 4 | Transporte | Divide datos, controla errores | TCP, UDP |
| 3 | Red | Direccionamiento y enrutamiento | IP, ICMP, routers |
| 2 | Enlace | Comunicación en la misma red | Ethernet, WiFi, MAC |
| 1 | Física | Transmisión de bits | Cables, fibra, señales WiFi |

La mayoría de ataques ocurren en capa 7 porque está diseñada para recibir conexiones externas por definición. Las capas inferiores requieren acceso físico al medio — imposible de forma remota.

## TCP vs UDP

| Característica | TCP | UDP |
|----------------|-----|-----|
| Garantía de entrega | Sí — reenvía si falla | No — dispara y olvida |
| Orden de paquetes | Sí | No |
| Velocidad | Más lento | Más rápido |
| Handshake | Sí — SYN/SYN-ACK/ACK | No |
| Uso típico | Web, SSH, email | Video, juegos, DNS |

### Three-way handshake de TCP

El proceso de establecer una conexión TCP antes de enviar datos:

1. Cliente envía SYN — "¿estás ahí?"
2. Servidor responde SYN-ACK — "sí, ¿tú estás?"
3. Cliente responde ACK — "sí, hablemos"

El SYN flood es un ataque que abusa de este proceso — el atacante manda miles de SYN sin completar el handshake, llenando la tabla de conexiones hasta colapsar el servidor.

## Puertos

Un puerto identifica a qué servicio va dirigido el tráfico dentro de una misma IP. La analogía exacta es un edificio — la IP es la dirección y el puerto es el número de apartamento.

| Rango | Números | Uso |
|-------|---------|-----|
| Bien conocidos | 0 – 1023 | Servicios estándar, requieren root |
| Registrados | 1024 – 49151 | Aplicaciones comunes |
| Dinámicos | 49152 – 65535 | Asignados temporalmente por el sistema |

### Puertos esenciales

| Puerto | Protocolo | Servicio |
|--------|-----------|---------|
| 22 | TCP | SSH |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 53 | UDP/TCP | DNS |
| 21 | TCP | FTP |
| 25 | TCP | SMTP |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 6379 | TCP | Redis |

### Comandos de red

`ss -tulnp` muestra conexiones activas. -t TCP, -u UDP, -l escuchando, -n numérico, -p proceso. Requiere sudo para ver el proceso.

`ip addr show` muestra interfaces y sus IPs. `ip route show` muestra la tabla de rutas — cómo el sistema decide por dónde mandar cada paquete.

Una IP en 127.0.0.1 significa que el servicio solo acepta conexiones locales. Una IP en 0.0.0.0 significa que está expuesto al mundo.

## nmap — escáner de puertos

### Flags principales

| Flag | Qué hace |
|------|----------|
| `-p-` | Escanea los 65535 puertos |
| `-p 80,443` | Escanea puertos específicos |
| `-sV` | Detecta versión del servicio |
| `-O` | Detecta sistema operativo |
| `-A` | Todo junto — versión, OS, scripts, traceroute |
| `-T1` | Muy lento, sigiloso |
| `-T4` | Rápido, el más usado en práctica |
| `-sS` | SYN scan — no completa el handshake, más sigiloso |
| `-sU` | Escanea puertos UDP |
| `-sn` | Solo verifica si el host está vivo |
| `-oN archivo` | Guarda resultado en archivo |

### Comando más completo para auditoría

`sudo nmap -sV -O -T4 -p- objetivo`

### Cómo leer el output

Para cada puerto abierto pregúntate: ¿tiene sentido que esté abierto en este servidor? Los estados posibles son open (analiza), closed (ignora) y filtered (hay firewall — conéctate desde adentro con SSH y compara con ss).

Busca versiones desactualizadas con `searchsploit servicio version` para encontrar exploits conocidos.

Nunca uses nmap sobre sistemas que no son tuyos sin permiso explícito.

## IP y subnetting

### Estructura de una IP

Una dirección IPv4 son 4 grupos de 8 bits — cada grupo va del 0 al 255. Los primeros grupos identifican la red, el último identifica el dispositivo.

### Rangos privados

| Rango | Uso |
|-------|-----|
| 10.0.0.0/8 | Redes empresariales grandes |
| 172.16.0.0/12 | Redes medianas |
| 192.168.0.0/16 | Redes domésticas |

### IPs especiales

| IP | Significado |
|----|-------------|
| 127.0.0.1 | Loopback — tú mismo, localhost |
| 0.0.0.0 | Todas las interfaces disponibles |
| 255.255.255.255 | Broadcast — todos en la red |

### Notación CIDR

El número después de la diagonal indica cuántos bits son la red. Los bits restantes son para dispositivos.

| CIDR | Dispositivos posibles | Uso típico |
|------|----------------------|------------|
| /8 | 16 millones | Redes enormes |
| /16 | 65,534 | Redes grandes |
| /24 | 254 | Redes domésticas |
| /32 | 1 solo | Un único dispositivo |

En AWS al crear una VPC defines su tamaño con CIDR desde el inicio — es difícil cambiarlo después.

## DNS — Domain Name System

### ¿Qué es y cuándo interviene?

El DNS traduce nombres de dominio a IPs — es la agenda telefónica de internet. Interviene solo una vez al principio de cada conexión. Una vez obtenida la IP, la comunicación es directa sin pasar por DNS.

Si el DNS cae, las conexiones ya establecidas siguen funcionando porque ya tienen la IP.

### Jerarquía DNS

Tu máquina → caché local → resolver del router → DNS del ISP → DNS raíz → DNS autoritativo del dominio

### Tipos de registros

| Tipo | Para qué sirve |
|------|----------------|
| A | IPv4 del dominio |
| AAAA | IPv6 del dominio — 4 veces más grande que A |
| MX | Servidor que recibe emails del dominio |
| NS | Servidores DNS autoritativos — quién tiene la autoridad final |
| TXT | Verificaciones y configuraciones de seguridad |
| CNAME | Alias de otro dominio |

### TTL — Time To Live

Segundos que una respuesta DNS es válida en caché. TTL corto (7s como Google) permite cambiar IPs rápido. TTL largo (3600s) reduce consultas pero hace los cambios más lentos.

### dig vs nslookup

Siempre usa dig — es el estándar profesional, muestra TTL y tiene más opciones. nslookup es legacy, solo úsalo en Windows donde dig no está disponible.

`dig google.com` consulta IPv4. `dig google.com MX` consulta servidor de correo. `dig google.com TXT` revela servicios externos de la organización.

### OSINT con DNS

Con solo el dominio y dig puedes descubrir qué servicios usa una organización — DocuSign, Microsoft, Cisco, Cloudflare, Google Workspace. Todo es información pública y legal. Esto se llama OSINT — Open Source Intelligence.

El typosquatting es registrar dominios con errores tipográficos comunes como goolge.com para capturar tráfico de usuarios que se equivocan. El DNS Spoofing es envenenar el caché DNS para que un dominio legítimo resuelva a una IP maliciosa.