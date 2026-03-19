# 📊 Monitoring Lab: Enterprise Observability

Plataforma de monitoreo y observabilidad completa para servidores Linux y PostgreSQL, desplegada con Docker y herramientas Cloud Native.

## 🎯 Arquitectura General
Ver diagrama de arquitectura aquí:
👉 [01-architecture.md](docs/01-architecture.md)

## 🏗 Stack Tecnológico
- **OS**: Ubuntu 24.04 LTS (Hyper-V)
- **Container Engine**: Docker Compose
- **Database**: PostgreSQL 16
- **Metrics**: Node Exporter & Postgres Exporter
- **Observability**: Prometheus & Grafana

## 🚀 Quick Start
Para desplegar el repositorio completo de manera rápida:
1. Instalar OS: 👉 [02-installation.md](docs/02-installation.md)
2. Instalar Docker: 👉 [03-docker-setup.md](docs/03-docker-setup.md)
3. Desplegar Monitoreo: 👉 [04-monitoring-stack.md](docs/04-monitoring-stack.md)
4. Configurar PostgreSQL: 👉 [05-postgresql.md](docs/05-postgresql.md)

## 📚 Tabla de Contenidos (Documentación)
- [01. Arquitectura y Diagramas](docs/01-architecture.md)
- [02. Instalación del Sistema Base](docs/02-installation.md)
- [03. Configuración de Docker](docs/03-docker-setup.md)
- [04. Despliegue del Stack (Prometheus/Grafana)](docs/04-monitoring-stack.md)
- [05. PostgreSQL 16 Setup](docs/05-postgresql.md)
- [06. Exporters (Postgres & Node)](docs/06-exporters.md)
- [07. Habilitando pg_stat_statements](docs/07-pg-stat-statements.md)
- [08. Configuración de Grafana](docs/08-grafana.md)
- [09. Troubleshooting](docs/09-troubleshooting.md)
- [10. Roadmap](docs/10-roadmap.md)

## 🔌 Puertos y Servicios
| Servicio | Puerto | Uso |
|---|---|---|
| Grafana | `3000` | UI Web |
| Prometheus | `9090` | Motor TSDB |
| Node Exporter | `9100` | SO Métricas |
| Postgres Exporter | `9187` | DB Métricas |
| PostgreSQL | `5432` | Base de datos |
