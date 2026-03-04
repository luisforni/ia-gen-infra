# IA-Gen Infra

[![GitHub repo](https://img.shields.io/badge/GitHub-luisforni-blue?style=flat&logo=github)](https://github.com/luisforni/ia-gen-infra)
[![License](https://img.shields.io/badge/License-MIT-green)](#licencia)
![Docker](https://img.shields.io/badge/Docker-24+-2496ED?logo=docker)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-2.0+-2496ED?logo=docker)

Orquestación Docker del ecosistema IA-Gen: chat generativo con Ollama (LLM), FastAPI (backend), Next.js (frontend) y Redis (caché/rate limiting).

## Arquitectura

```
┌─────────────────┐     ┌─────────────────┐
│  Frontend        │────▶│  Backend         │
│  Next.js :3000   │     │  FastAPI  :8000  │
└─────────────────┘     └────────┬────────┘
                                  │
                     ┌────────────┴─────────────┐
                     │                           │
              ┌──────▼──────┐          ┌────────▼────────┐
              │   Ollama     │          │     Redis        │
              │   LLM :11434 │          │   Cache  :6379   │
              └─────────────┘          └─────────────────┘
```

## Requisitos

- Docker y Docker Compose instalados
- Git

## Estructura de directorios requerida

Los tres repositorios deben estar en el **mismo directorio padre**:

```
ia-gen/
├── ia-gen-backend/
├── ia-gen-frontend/
└── ia-gen-infra/        ← este repositorio
```

## Instalación y puesta en marcha

### 1. Clonar los tres repositorios

```bash
mkdir ia-gen && cd ia-gen

git clone https://github.com/luisforni/ia-gen-backend.git
git clone https://github.com/luisforni/ia-gen-frontend.git
git clone https://github.com/luisforni/ia-gen-infra.git
```

### 2. Iniciar los servicios

```bash
cd ia-gen-infra

docker compose up -d --build
```

### 3. Descargar el modelo LLM

Espera ~10 segundos a que Ollama arranque y luego ejecuta:

```bash
docker exec ollama ollama pull llama3.2:1b
```

La descarga es de ~1.3 GB. Puedes ver el progreso en tiempo real.

### 4. Verificar que todo funciona

```bash
curl http://localhost:8000/api/v1/health
# {"status":"healthy","redis":true,"ollama":true}
```

### 5. Abrir la aplicación

- **Chat**: http://localhost:3000
- **API docs**: http://localhost:8000/docs

---

## Comandos útiles

```bash
# Ver estado de los contenedores
docker compose ps

# Ver logs en tiempo real
docker compose logs -f

# Ver logs de un servicio específico
docker compose logs -f backend
docker compose logs -f frontend

# Detener todos los servicios
docker compose down

# Reiniciar un servicio
docker compose restart backend

# Listar modelos descargados en Ollama
docker exec ollama ollama list
```

## Servicios y puertos

| Servicio  | Contenedor      | Puerto |
|-----------|-----------------|--------|
| Frontend  | ia-gen-frontend | 3000   |
| Backend   | ia-gen-backend  | 8000   |
| Ollama    | ollama          | 11434  |
| Redis     | redis           | 6379   |

## Variables de entorno

Las variables de entorno de cada servicio se configuran en `docker-compose.yml`. Las principales son:

| Variable                   | Valor por defecto                   | Descripción                       |
|----------------------------|-------------------------------------|-----------------------------------|
| `OLLAMA_HOST`              | `http://ollama:11434`               | URL interna de Ollama             |
| `MODEL_NAME`               | `llama3.2:1b`                       | Modelo LLM a usar                 |
| `REDIS_URL`                | `redis://redis:6379/0`              | URL interna de Redis              |
| `NEXT_PUBLIC_API_BASE_URL` | `http://ia-gen-backend:8000/api/v1` | URL del backend para el frontend  |

## Repositorios relacionados

- **Backend**: [ia-gen-backend](https://github.com/luisforni/ia-gen-backend)
- **Frontend**: [ia-gen-frontend](https://github.com/luisforni/ia-gen-frontend)

## Licencia

MIT
