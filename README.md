# 📊 Plataforma de Monitoreo y Observabilidad: Linux & PostgreSQL 16

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Postgres](https://img.shields.io/badge/postgres-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=Prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

## 📋 Descripción del Proyecto

Este proyecto implementa una plataforma de monitoreo y observabilidad completa, diseñada a nivel de producción (enterprise). El objetivo es centralizar la recolección, almacenamiento y visualización de métricas de un sistema base Linux (Ubuntu Server) y de una base de datos PostgreSQL 16. La solución extrae métricas detalladas a nivel de sistema operativo y ofrece visibilidad profunda sobre el rendimiento de las consultas SQL mediante `pg_stat_statements`.

## 🏗️ Stack Tecnológico

* **Sistema Operativo:** Ubuntu Server 24.04 LTS (Host: Virtual Machine sobre Hyper-V)
* **Contenedores:** Docker + Docker Compose
* **Base de Datos:** PostgreSQL 16 (Instalación nativa / servicio systemd)
* **Recolector de Métricas DB:** Postgres Exporter
* **Recolector de Métricas SO:** Node Exporter
* **Almacenamiento de Métricas y Alertas:** Prometheus
* **Visualización de Datos:** Grafana
* **Seguridad / Red:** UFW (Uncomplicated Firewall)

## 💡 Casos de Uso

* **Monitorización de Servidores en Tiempo Real:** Seguimiento de CPU, RAM, I/O de disco y carga de red del servidor Ubuntu.
* **Auditoría de Rendimiento de Base de Datos:** Observabilidad de PostgreSQL 16, incluyendo buffers, locks, conexiones y rendimiento transaccional.
* **Optimización de Consultas SQL:** Identificación de cuellos de botella y queries lentas mediante `pg_stat_statements` y el Postgres Exporter.
* **Gestión Proactiva:** Análisis de telemetría para prevención de caídas y planificación de recursos a futuro.

## 📐 Arquitectura

### Diagrama de Arquitectura y Red

```mermaid
graph TD
    subgraph "Hyper-V VM (Ubuntu Server 24.04 LTS)"
        UFW[Firewall UFW]
        
        subgraph "Servicios Nativos"
            OS[Ubuntu OS] --> |Métricas SO| NE(Node Exporter :9100)
            PG[(PostgreSQL 16 :5432)]
            PG_EXT[pg_stat_statements] -.-> PG
            PG --> |Métricas DB| PE(Postgres Exporter :9187)
        end
        
        subgraph "Docker Compose Stack"
            PROM(Prometheus :9090)
            GRAF(Grafana :3000)
        end
        
        NE --> |Scraping HTTP| PROM
        PE --> |Scraping HTTP| PROM
        PROM --> |Data Source HTTP| GRAF
    end
    
    Admin((Usuario / DevOps)) -->|Acceso Dashboard Web| GRAF
    Admin -->|Consultas DDL/DML| PG
    
    style UFW fill:#f96,stroke:#333,stroke-width:2px
```

### Flujo de Datos

```mermaid
sequenceDiagram
    participant OS as Ubuntu 24.04
    participant PG as PostgreSQL 16
    participant Exporters as Exporters (Node/Postgres)
    participant Prom as Prometheus
    participant Graf as Grafana
    
    OS->>Exporters: Exposición de estado (CPU, RAM, Disk, IO)
    PG->>Exporters: Estadísticas de Base de Datos (pg_stat_statements, locks)
    loop Cadencia de Recolección (ej. 15s)
        Prom->>Exporters: Pull de métricas HTTP GET /metrics
        Exporters-->>Prom: Datos métricos crudos
    end
    Graf->>Prom: Ejecuta Query con PromQL
    Prom-->>Graf: Series de Tiempo (Time-series data)
    Graf->>Graf: Renderizado Visual en Dashboards
```

### Relación de Servicios

```mermaid
flowchart LR
    A[Grafana] <-->|Lee datos| B(Prometheus)
    B -->|Scrapea métricas| C{Node Exporter}
    B -->|Scrapea métricas| D{Postgres Exporter}
    D -.->|Se conecta vía driver libpq| E[(PostgreSQL 16)]
    C -.->|Lee /proc y /sys| F[Sistema Base Ubuntu]
```

## 🔌 Puertos Utilizados

| Componente | Puerto Default | Protocolo | Descripción |
| :--- | :---: | :---: | :--- |
| **Grafana** | `3000` | TCP | Interfaz web de Dashboards visuales |
| **Prometheus** | `9090` | TCP | Interfaz web de Prometheus / Scraping interno |
| **Node Exporter** | `9100` | TCP | Exposición de métricas del servidor base |
| **Postgres Exporter** | `9187` | TCP | Exposición de métricas de PostgreSQL |
| **PostgreSQL 16** | `5432` | TCP | Conexión directa a la base de datos relacional |

## 📁 Estructura del Proyecto

Modelo de distribución de los assets y configuraciones en el entorno:

```text
monitoring/
├── docker-compose.yml           # Declaración del stack de monitorización
├── prometheus/
│   └── prometheus.yml           # Configuración de recolección de Prometheus
├── grafana/
│   └── provisioning/            # Auto-aprovisionamiento de datasources y paneles
│       ├── datasources/
│       └── dashboards/
└── exporters/
    └── postgres_queries.yaml    # (Opcional) Consultas personalizadas para extensiones extra
```

---

## 🚀 GUÍA PASO A PASO (SETUP COMPLETO)

### 2.1 Instalación del Sistema Base Ubuntu Server 24.04 LTS

1. **Creación de la VM en Hyper-V:**
   * Asignar memoria dinámica sugerida (mínima de 4GB para DB + Observabilidad).
   * Generar 2-4 vCPUs.
   * Conectar un Virtual Switch de red preconfigurado.
2. **Setup de OS (Configuración Inicial):**
   * Seleccionar "Ubuntu Server (minimized)" u opcional estándar.
   * Asegurar habilitación del `OpenSSH Server`.
3. **Actualización crítica inicial:**
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo reboot
   ```

### 2.2 Instalación Asegurada de Docker

1. **Agregar repositorio oficial e instalar utilidades:**
   ```bash
   sudo apt update
   sudo apt install -y ca-certificates curl gnupg

   sudo install -m 0755 -d /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   sudo chmod a+r /etc/apt/keyrings/docker.gpg

   echo \
     "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
     "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
2. **Instalar Motor Docker y Plugin Compose:**
   ```bash
   sudo apt update
   sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
3. **Uso de Docker sin Sudo (Recomendado):**
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

### 2.3 Implementación del Stack de Monitoreo

Crear estructura de directorios:
```bash
mkdir -p ~/monitoring/{prometheus,grafana,exporters}
cd ~/monitoring
```

**`docker-compose.yml`**:
```yaml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: always
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--storage.tsdb.retention.time=15d'
    network_mode: "host" # Útil para acceder a los resource exporters nativos con localhost.
    
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=strong_admin_pass_123!
      - GF_USERS_ALLOW_SIGN_UP=false
    ports:
      - "3000:3000"

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: always
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    ports:
      - "9100:9100"

volumes:
  prometheus_data:
  grafana_data:
```

**`prometheus/prometheus.yml`**:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'postgres-exporter'
    static_configs:
      - targets: ['localhost:9187'] 
```

Levantar servicios:
```bash
docker compose up -d
```

### 2.4 Configuración del Firewall (UFW)

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing

# CRITICOS: SSH y Grafana Web
sudo ufw allow 22/tcp
sudo ufw allow 3000/tcp

# Opcional (Si conectas PostgreSQL directo desde otro nodo)
# sudo ufw allow 5432/tcp

sudo ufw enable
```

### 2.5 Instalación de PostgreSQL 16 (Nativo)

```bash
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

sudo apt update
sudo apt install -y postgresql-16 postgresql-contrib-16

sudo systemctl enable postgresql
sudo systemctl start postgresql
```

### 2.6 Configuración de Postgres Exporter (Como Servicio Systemd)

Descargar binario autónomo:
```bash
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.15.0/postgres_exporter-0.15.0.linux-amd64.tar.gz
tar xvf postgres_exporter-*.tar.gz
sudo mv postgres_exporter-*/postgres_exporter /usr/local/bin/
sudo chown postgres:postgres /usr/local/bin/postgres_exporter
```

Crear privilegios a nivel base de datos:
```bash
sudo -u postgres psql -c "CREATE USER postgres_exporter WITH PASSWORD 'pg_exporter_pass';"
sudo -u postgres psql -c "ALTER USER postgres_exporter SET SEARCH_PATH TO postgres_exporter,pg_catalog;"
sudo -u postgres psql -c "GRANT pg_monitor to postgres_exporter;"
```

Crear servicio Systemd en `/etc/systemd/system/postgres_exporter.service`:
```ini
[Unit]
Description=Prometheus exporter for PostgreSQL
Wants=network-online.target
After=network-online.target postgresql.service

[Service]
User=postgres
Group=postgres
Environment="DATA_SOURCE_NAME=postgresql://postgres_exporter:pg_exporter_pass@localhost:5432/postgres?sslmode=disable"
# Extensión para métricas adicionales como pg_stat_statements:
Environment="PG_EXPORTER_AUTO_DISCOVER_DATABASES=true"
ExecStart=/usr/local/bin/postgres_exporter
Restart=always

[Install]
WantedBy=multi-user.target
```

Iniciar recolector:
```bash
sudo systemctl daemon-reload
sudo systemctl enable postgres_exporter
sudo systemctl start postgres_exporter
```

### 2.7 Activación de `pg_stat_statements` y Métricas Avanzadas

Editar la configuración core de psql `/etc/postgresql/16/main/postgresql.conf`:
```conf
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
pg_stat_statements.max = 10000
track_activity_query_size = 2048
```

Reiniciar Base de Datos y habilitar la extensión global:
```bash
sudo systemctl restart postgresql
sudo -u postgres psql -c "CREATE EXTENSION IF NOT EXISTS pg_stat_statements;"
```

### 2.8 Configuración Final en Grafana

1. Autenticar en `http://<IP_Servidor>:3000`.
2. Dirigirse a **Administration -> Data Sources -> Add Data Source -> Prometheus**.
3. URL: `http://localhost:9090` (si configuro stack via network root de docker) u `172.x.x.x` interno.
4. Importar **Dashboards Clásicos** en la opción Create (+) -> Import:
   * Servidor Linux: ID `1860` (Node Exporter Full)
   * PostgreSQL Base: ID `9628` (PostgreSQL Database)
   * Consultas Avanzadas Queries: ID `14114` (PostgreSQL pg_stat_statements)

---

## 🔒 BUENAS PRÁCTICAS IMPLEMENTADAS

* **Seguridad (Least Privilege):** El usuario `postgres_exporter` se aisla utilizando el ROL `pg_monitor`, el cual permite obtener la metadata y configuraciones, sin poseer ningún privilegio de manipulación (DML/DDL).
* **Networking (Cero Exposición):** Solamente el puerto de visualización (3000 de Grafana) y el de gestión (22 SSH) están expuestos en el host a través de UFW. Prometheus extrae silenciosamente a nivel localhost interno.
* **Separación de Servicios:** El Engine de PostgreSQL y Postgres Exporter están aislados a nivel nativo como demonios de sistema (`systemd`), mientras que Grafana y Prometheus se gestionan via Contenedores para agilizar las actualizaciones e implementaciones.
* **Persistencia:** Volúmenes nativos Docker garantizados para configuración y data de Grafana, así como Base de Datos transaccional de Prometheus TSDB (`prometheus_data`).

---

## 📊 MÉTRICAS CRÍTICAS RECOLECTADAS

**Sistema Operativo Ubuntu:**
* Tiempos y uso de CPU discriminado por Load (I/O Wait, User, System).
* Memoria real libre vs Memoria en buffers de caché.
* Espacio en disco previsional de colapso de particiones.

**Motor PostgreSQL (`pg_stat` general):**
* Cache Hit Ratio del buffer de disco. (Clave para performance).
* Cantidad y tipologías de Conexiones activas / idle.
* Tiempo de escritura del Background Writer Process (Picos de E/S).
* Tasas de operaciones CRUD de la BD (Insert / Fetched / Updated / Deleted tuples).

**Consultas Avanzadas (`pg_stat_statements`):**
* Cantidad de ejecuciones temporales de una consulta SQL específica.
* Sumatoria temporal del tiempo real total incurrido en procesamiento de X query.
* Desviación estándar de los tiempos de planificación y ejecución dentro del motor semántico.

---

## 🛠️ TROUBLESHOOTING

* **Prometheus Targets en estado DOWN:**
  * Verificar en Grafana > Prometheus UI (`localhost:9090/targets`).
  * Desperfecto recurrente: Si la instancia está DOWN, ejecutar `sudo systemctl status postgres_exporter` para validar que el servicio del sistema recolector sigue con vida.
* **Falta de datos en métricas de pg_stat_statements:**
  * Causa: La extensión no se inicializó correctamente o el exporter no la detecta.
  * Solución: Validar primero que los módulos están cargados haciendo `SHOW shared_preload_libraries;` directo en PostgreSQL.
* **Grafana con "No Data":**
  * Asegurarse de que el sincronismo de tiempo de la máquina de host concuerde con el visualizador en el navegador (Husos horarios locales vs UTC).
* **Error de permisos en Node Exporter:**
  * Si hay alertas en las terminales con Denegación a `/proc` u otro recurso por parte de apparmor/selinux, revise que el parámetro `ro` de solo lectura esté aplicado en `docker-compose.yml`.

---

## 🗺️ ROADMAP (MEJORAS FUTURAS)

1. **Gestión Total de Alertas (Alertmanager):** Incorporarlo para emitir notificaciones críticas de caídas de BD en canales de MS Teams, Slack o vía SMTP.
2. **Centralización de Logs (Loki + Promtail):** Desplegar Promtail sobre los ficheros de log generados por `/var/log/postgresql/` para cruzar en el mismo Dashboard las métricas y los errores semánticos crudos de Postgre.
3. **Escalabilidad y Alta Disponibilidad (HA):** Requerimiento eventual de Patroni Backup Storage.
4. **Despliegue Multi-Entornos con Ansible/Terraform:** Empaquetar el proceso de instalación manual descripto para lanzamientos "One-Click" en infraestructuras agnósticas (Cloud u On-Premises).
