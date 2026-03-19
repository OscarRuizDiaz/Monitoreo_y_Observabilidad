# 01. Arquitectura del Proyecto

Este documento describe la arquitectura técnica, flujo de datos y relación entre los componentes de observabilidad.

## Diagrama Funcional de Arquitectura

```mermaid
graph TD
    subgraph "Ubuntu 24.04 Host (Hyper-V)"
        PG[(PostgreSQL 16)]
        PG_STAT[pg_stat_statements] -.-> PG
        
        NE(Node Exporter) --> |Scrape OS| HostOS[Host OS]
        PE(Postgres Exporter) --> |Scrape DB| PG
        
        subgraph "Docker Stack"
            PROM(Prometheus)
            GRAF(Grafana)
        end
        
        PROM --> |Pull| NE
        PROM --> |Pull| PE
        GRAF --> |Query TSDB| PROM
    end
```

## Flujo de Datos
```mermaid
sequenceDiagram
    participant Postgres
    participant Exporter
    participant Prometheus
    Exporter->>Postgres: Ejecuta query de métricas
    Postgres-->>Exporter: Resultados SQL (pg_stat, cache, locks)
    Prometheus->>Exporter: HTTP GET /metrics
    Exporter-->>Prometheus: Time-Series Data export
```

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [02-installation.md](02-installation.md)
