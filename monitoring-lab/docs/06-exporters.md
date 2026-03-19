# 06. Configuración de Exporters (Postgres Exporter)

Mapearemos el Exportador Oficial como un proceso **Systemd** del Host debido a la arquitectura nativa previa.

## 1. Instalación de Binario
```bash
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.15.0/postgres_exporter-0.15.0.linux-amd64.tar.gz
tar xvf postgres_exporter-*.tar.gz
sudo mv postgres_exporter-*/postgres_exporter /usr/local/bin/
sudo chown postgres:postgres /usr/local/bin/postgres_exporter
```

## 2. Entorno y Queries
La definición del origen de datos está alojada en este archivo:
👉 [../exporters/postgres_exporter/postgres_exporter.env](../exporters/postgres_exporter/postgres_exporter.env)

## 3. Despliegue de Servicio en Systemd
Crear el archivo base `/etc/systemd/system/postgres_exporter.service`:
```ini
[Unit]
Description=Prometheus exporter for PostgreSQL
Wants=network-online.target
After=network-online.target postgresql.service

[Service]
User=postgres
Group=postgres
EnvironmentFile=/home/usuario/monitoring-lab/exporters/postgres_exporter/postgres_exporter.env
ExecStart=/usr/local/bin/postgres_exporter
Restart=always

[Install]
WantedBy=multi-user.target
```

Habilitarlo en el Kernel:
```bash
sudo systemctl daemon-reload
sudo systemctl enable postgres_exporter --now
```

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [07-pg-stat-statements.md](07-pg-stat-statements.md)
