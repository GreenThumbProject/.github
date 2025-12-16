# GreenThumb Project - AI Context

This document provides comprehensive context about the GreenThumb project for AI assistants (Claude, GPT, Copilot, Gemini, etc.) to understand the codebase and provide accurate assistance.

**Last Updated**: December 2025

---

## Project Overview

**GreenThumb** is an automated greenhouse project focused on creating optimal plant growth environments through IoT, sensor data collection, computer vision, and machine learning.

### Current Phase

- **Research project**: Low-cost hydroponic platform for cherry tomatoes
- **Timeline**: 12-month research (PIBITI proposal)
- **Controller**: Raspberry Pi 5 (primary)
- **Future**: ESP32 microcontrollers, cloud integration

### Long-term Vision

- AI-powered greenhouse that establishes optimal environments for any plant species
- Scalable, reproducible system for commercial sale
- ML-driven growth optimization
- Data as a Service / MLaaS business model

---

## Repository Structure

### Core Repositories

| Repo | Purpose | Tech Stack | Visibility |
|------|---------|------------|------------|
| `rasp5` | Main Raspberry Pi 5 deployment | Docker, FastAPI, Python | Private |
| `greenthumb-core` | Shared library (models, DB, hardware) | Python, SQLModel | Private |
| `database` | Database schemas & migrations | PostgreSQL, SQL | Private |
| `cron` | Scheduled tasks | Python, cron | Private |
| `docs` | MkDocs documentation | Markdown, Material for MkDocs | Public |
| `research` | Academic papers | Markdown, LaTeX | Private |

### Repository Relationships

```
greenthumb-core ──imported by──> rasp5
greenthumb-core ──imported by──> cron
database ──schemas used by──> rasp5
rasp5 ──documented in──> docs
research ──referenced in──> docs
```

### Naming Convention

- **`greenthumb-*`** prefix: ONLY for installable Python packages/libraries
- **Short names** (`rasp5`, `database`, `cron`): For applications and deployments

---

## Architecture

### rasp5 Services (Docker Compose)

| Service | Port | Description |
|---------|------|-------------|
| `db` | 5432 (internal) | PostgreSQL 17.6 |
| `api` | 8080 | FastAPI + live video streaming |
| `data_collection` | - | Sensor readings (30min) + photos (4h) |
| `watchtower` | - | Auto-update from Docker Hub |
| `cron` | - | Scheduled tasks (optional profile) |

### greenthumb-core Structure

```
greenthumb_core/
├── db/           # Database engine, session, lifespan
│   ├── engine.py
│   └── lifespan.py
├── models/       # SQLModel definitions
│   └── models.py
└── rpi5/         # Hardware interfaces (sensors, camera)
    ├── camera.py
    ├── device.py
    └── sensors.py
```

### Database Schema (Key Tables)

| Table | Purpose |
|-------|---------|
| `device` | Greenhouse devices (Raspberry Pi) |
| `plant_species` | Plant catalog |
| `sensor_model` | Sensor hardware specs (AHT10, BMP280, TSL2561) |
| `device_sensor` | Sensors attached to devices |
| `variable` | Measured variables (temperature, humidity, etc.) |
| `unit` | Measurement units (°C, %, hPa, lux) |
| `measurement` | Collected data points |
| `cultivation` | Plant growth records |
| `sensor_capability` | What each sensor can measure |

---

## Technology Stack

### Current

| Component | Technology |
|-----------|------------|
| Language | Python 3.11+ |
| Web Framework | FastAPI |
| ORM | SQLModel (SQLAlchemy 2.0) |
| Database | PostgreSQL 17.6 |
| Containers | Docker, Docker Compose |
| CI/CD | GitHub Actions → Docker Hub |
| Hardware | Raspberry Pi 5, I2C sensors |
| Auto-updates | Watchtower |

### Sensors (I2C)

| Sensor | Address | Measurements |
|--------|---------|--------------|
| AHT10 | 0x38 | Temperature, Humidity |
| BMP280 | 0x76 | Pressure, Temperature |
| TSL2561 | 0x39 | Light Intensity |

### Planned

| Component | Technology |
|-----------|------------|
| Cloud DB | Supabase (PostgreSQL) |
| Image Storage | Cloudflare R2 |
| ML Framework | TensorFlow/PyTorch |
| Future Controllers | ESP32 microcontrollers |
| pH Sensor | (to be selected) |
| EC Sensor | (to be selected) |

---

## Development Guidelines

### Code Style

- Python: Follow PEP 8
- Type hints: Required for all functions
- Documentation: Docstrings for public functions
- SQL: Uppercase keywords, lowercase table/column names

### Git Workflow

- Main branch: `main`
- Feature branches: `feature/description`
- CI/CD triggers on push to `main`
- Conventional commits: `type(scope): description`

### Common Commands (rasp5)

```bash
make up        # Start all services
make down      # Stop all services
make logs      # View logs
make rebuild   # Full rebuild (down, prune, build, up)
make db-shell  # PostgreSQL shell
make restart-<service>  # Restart specific service
make logs-<service>     # Logs for specific service
```

---

## Important Files

### rasp5

| File | Purpose |
|------|---------|
| `compose.yaml` | Docker Compose configuration |
| `Makefile` | Developer commands |
| `.env.example` | Environment variables template |
| `data_collection/main.py` | Sensor collection loop |
| `fastapi/app.py` | API endpoints |
| `db/01_schema.sql` | Database schema |
| `db/02_seed.sql` | Initial seed data |

### greenthumb-core

| File | Purpose |
|------|---------|
| `pyproject.toml` | Package configuration |
| `src/greenthumb_core/models/` | SQLModel definitions |
| `src/greenthumb_core/rpi5/` | Hardware interfaces |
| `src/greenthumb_core/db/` | Database utilities |

---

## Environment Variables

### Required (rasp5)

| Variable | Description |
|----------|-------------|
| `DB_PASSWORD` | PostgreSQL password |
| `DOCKERHUB_USERNAME` | Docker Hub username |
| `GH_PAT` | GitHub PAT for greenthumb-core |

### Planned (Cloud)

| Variable | Description |
|----------|-------------|
| `SUPABASE_URL` | Supabase project URL |
| `SUPABASE_KEY` | Supabase anon key |
| `CLOUDFLARE_R2_ENDPOINT` | R2 endpoint URL |
| `CLOUDFLARE_R2_ACCESS_KEY` | R2 access key |
| `CLOUDFLARE_R2_SECRET_KEY` | R2 secret key |

---

## GitHub Secrets (CI/CD)

### rasp5 Repository

| Secret | Purpose |
|--------|---------|
| `DOCKERHUB_USERNAME` | Docker Hub username |
| `DOCKERHUB_TOKEN` | Docker Hub access token |
| `GH_PAT` | GitHub PAT for private repo access |

---

## Language Notes

- **Primary**: English (code, docs, comments)
- **Portuguese**: Research paper, university deliverables
- **Bilingual summaries**: Both EN and PT maintained in docs

---

## Data Collection

- **Sensor readings**: Every 30 minutes
- **Photo capture**: Every 4 hours
- **Log file**: `logs/data_collection.txt`
- **Photo storage**: `imgs/` directory

---

## Current Limitations

1. Single device support (device_id=1 hardcoded in places)
2. No cloud sync implemented yet
3. pH and EC sensors not integrated
4. LED/pump PWM control not implemented
5. Computer vision not implemented yet

---

## Future Work (Priority Order)

1. pH and EC sensor integration
2. LED PWM control
3. Water pump control
4. Cloud sync (Supabase)
5. Image upload (Cloudflare R2)
6. Computer vision for growth analysis
7. Multi-device support
8. ML growth prediction

---

## Contact

- **Developer**: Henrique Bucci R. Netto
- **Email**: henriquebrn@al.insper.edu.br
- **GitHub Personal**: [@henriquebrnetto](https://github.com/henriquebrnetto)
- **Organization**: [GreenThumbProject](https://github.com/GreenThumbProject)

---

## How to Use This Document

When assisting with GreenThumb code:

1. **Identify the repository** the user is working on
2. **Check the tech stack** for that repository
3. **Follow coding guidelines** (PEP 8, type hints, etc.)
4. **Reference correct file paths** based on repository structure
5. **Use correct environment variables** for configuration
6. **Keep naming conventions** (greenthumb-* only for packages)

For database work:
- Models are in `greenthumb-core/src/greenthumb_core/models/`
- Use SQLModel, not plain SQLAlchemy
- Follow existing table naming conventions

For hardware work:
- All sensors use I2C on Raspberry Pi 5
- Camera is USB at `/dev/video0`
- Requires `[rpi5]` extra dependencies from greenthumb-core
