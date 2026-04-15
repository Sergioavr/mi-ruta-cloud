# Usuarios y grupos en Linux

## Comandos esenciales
| Comando | Qué hace |
|---------|----------|
| `whoami` | Muestra tu usuario actual |
| `groups` | Muestra los grupos a los que perteneces |
| `id` | Muestra tu UID, GID y grupos detallado |
| `su usuario` | Cambia de usuario |
| `sudo su` | Cambia a root |

## /etc/passwd — el archivo de usuarios
Cada línea tiene este formato: 
- usuario:x:UID:GID:descripcion:home:shell
kali   :x:1000:1000:Kali:/home/kali:/usr/bin/zsh

-| Campo | Significado |
| usuario | Nombre del usuario |
| x | Contraseña (guardada en /etc/shadow) |
| UID | ID numérico del usuario (1000+ son usuarios reales) |
| GID | ID del grupo principal |
| home | Carpeta personal |
| shell | Terminal que usa al iniciar sesión |

## /etc/group — el archivo de grupos
-grupo:x:GID:usuarios
sudo :x:27 :kali

Grupos importantes en Kali:
| Grupo | Para qué sirve |
|-------|----------------|
| sudo | Puede usar sudo |
| kali | Grupo principal del usuario |
| docker | Puede usar Docker sin sudo |

## Crear y gestionar usuarios
```bash
sudo useradd -m usuario    # Crea usuario con carpeta home
sudo passwd usuario        # Asigna contraseña
sudo usermod -aG sudo user # Agrega usuario al grupo sudo
sudo userdel -r usuario    # Elimina usuario y su home
```

## Relación con permisos
Los permisos de ls -la usan usuarios y grupos:
- La columna dueño → usuario de /etc/passwd
- La columna grupo → grupo de /etc/group
- Por eso chmod 600 protege contra otros usuarios del sistema