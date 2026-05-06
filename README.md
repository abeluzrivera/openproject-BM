# openproject-BM

Repositorio para levantar [OpenProject](https://www.openproject.org/) mediante Docker Compose con volúmenes persistentes.

## Estructura del repositorio

```
openproject-BM/
├── data/
│   ├── pgdata/             # Base de datos PostgreSQL (ignorado por git)
│   ├── assets/             # Archivos adjuntos y recursos (ignorado por git)
│   └── logs/               # Logs de la aplicación (ignorado por git)
├── docker-compose.yml      # Definición de servicios y bind mounts
├── .env                    # Variables de entorno (NO se sube a git)
├── .env.example            # Plantilla de variables de entorno
├── .gitignore
└── README.md
```

## Requisitos

- [Docker](https://docs.docker.com/get-docker/) >= 20.10
- [Docker Compose](https://docs.docker.com/compose/install/) >= 2.x

## Inicio rápido

### 1. Configurar variables de entorno

```bash
cp .env.example .env
```

Edita `.env` y reemplaza al menos `SECRET_KEY_BASE` con un valor seguro:

```bash
# Linux/macOS
openssl rand -hex 64

# Windows (PowerShell)
[System.Convert]::ToBase64String((1..64 | ForEach-Object { Get-Random -Maximum 256 }))
```

### 2. Levantar el servicio

```bash
docker compose up -d
```

### 3. Acceder a OpenProject

Abre el navegador en: [http://localhost:9080](http://localhost:9080)

Credenciales por defecto:
- **Usuario:** `admin`
- **Contraseña:** `admin` (se solicita cambio en el primer inicio de sesión)

## Datos persistentes (bind mounts)

Los datos se guardan en la carpeta `data/` del repositorio. Las subcarpetas están ignoradas por git (solo se conserva un `.gitkeep` para mantener la estructura).

| Carpeta local     | Ruta en el contenedor         | Contenido                    |
|-------------------|-------------------------------|------------------------------|
| `data/pgdata/`    | `/var/openproject/pgdata`     | Base de datos PostgreSQL     |
| `data/assets/`    | `/var/openproject/assets`     | Archivos adjuntos y recursos |
| `data/logs/`      | `/var/log/supervisor`         | Logs de la aplicación        |

## Comandos útiles

```bash
# Ver logs en tiempo real
docker compose logs -f openproject

# Detener sin eliminar datos
docker compose stop

# Detener y eliminar contenedores (los volúmenes se conservan)
docker compose down

# Eliminar TODO incluyendo volúmenes (¡destruye los datos!)
docker compose down -v

# Actualizar imagen
docker compose pull && docker compose up -d
```

## Configuración de email (opcional)

Descomenta y completa las variables `SMTP_*` en `.env` y el bloque correspondiente en `docker-compose.yml`.

## Notas

- El archivo `.env` está excluido de git para proteger las credenciales. Usa `.env.example` como referencia.
- Para entornos productivos, cambia `OPENPROJECT_HTTPS=true` y configura un proxy inverso (nginx/Traefik) con certificado TLS.
