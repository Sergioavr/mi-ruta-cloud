# Permisos en Linux

## ¿Qué son los permisos?
Controlan quién puede hacer qué con cada archivo o carpeta.
Hay tres tipos de usuarios y tres tipos de acciones.

## Los tres usuarios
| Usuario | Descripción |
|---------|-------------|
| Dueño | El usuario que creó el archivo |
| Grupo | Usuarios que comparten el mismo grupo |
| Otros | Cualquier otro usuario del sistema |

## Las tres acciones
| Símbolo | Nombre | En archivos | En carpetas |
|---------|--------|-------------|-------------|
| `r` | read | Ver contenido | Ver qué hay dentro |
| `w` | write | Modificar | Crear/borrar archivos |
| `x` | execute | Ejecutarlo | Entrar con cd |
| `-` | ninguno | Sin permiso | Sin permiso |

## El sistema numérico
Cada permiso tiene un valor numérico que se suma:

| Número | Permiso | Cálculo |
|--------|---------|---------|
| 4 | r (leer) | base |
| 2 | w (escribir) | base |
| 1 | x (ejecutar) | base |
| 7 | rwx | 4+2+1 |
| 6 | rw- | 4+2 |
| 5 | r-x | 4+1 |
| 4 | r-- | 4 |

## Permisos más comunes
| Número | Permisos | Uso típico |
|--------|----------|------------|
| `777` | rwxrwxrwx | Nunca en producción — todos pueden todo |
| `755` | rwxr-xr-x | Carpetas y scripts |
| `644` | rw-r--r-- | Archivos normales |
| `600` | rw------- | Claves SSH y configs privadas |
| `400` | r-------- | Archivos muy sensibles, solo lectura |

## Comando chmod
Cambia los permisos de un archivo o carpeta.

```bash
chmod 600 archivo.md      # Solo el dueño lee y escribe
chmod 644 archivo.md      # Dueño escribe, otros solo leen
chmod 755 carpeta/        # Estándar para carpetas
chmod +x script.sh        # Agrega permiso de ejecución
chmod -x script.sh        # Quita permiso de ejecución
```

## ¿Afecta a Git?
Git solo rastrea si un archivo es ejecutable o no, no todos los permisos.

```bash
chmod +x archivo    # Git sí detecta este cambio
chmod 644 archivo   # Git NO detecta este cambio
```

## ¿Son locales o se suben a GitHub?
100% locales. GitHub no guarda permisos de Linux.
Cuando alguien clona tu repo, su sistema asigna permisos automáticamente.

## Ver permisos
```bash
ls -la                    # Permisos de la carpeta actual
ls -la archivo.md         # Permisos de un archivo específico
ls -laR ~/mi-ruta-cloud   # Permisos de todo el árbol recursivo
```

## ¿Por qué importa en ciberseguridad?
Los permisos mal configurados son uno de los vectores de ataque
más comunes en sistemas Linux reales.

| Escenario | Riesgo |
|-----------|--------|
| Archivo sensible con `644` | Cualquier usuario del sistema puede leerlo |
| Script de root con `777` | Un atacante puede modificarlo y escalar privilegios |
| Clave SSH con `644` | SSH se niega a funcionar y la clave queda expuesta |
| Archivo con bit SUID mal puesto | Escalada de privilegios a root |