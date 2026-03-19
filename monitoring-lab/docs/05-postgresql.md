# 05. PostgreSQL 16 Setup

Instalación de la base de datos PostgreSQL de forma nativa en el Host. Optamos por la vía nativa en lugar de Docker para garantizar mayor semejanza a producción y tuning en sistema.

## 1. Agregar Repo Oficial e Instalar
```bash
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

sudo apt update
sudo apt install postgresql-16 postgresql-contrib-16 -y
```

## 2. Seguridad de Servicio
Asegurar el arranque automático tras cortes de energía:
```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

## 3. Usuario Restringido de Observabilidad
Crearemos un usuario dedicado y acotado para Prometheus (siguiendo el principio de _Least Privilege_):
```bash
sudo -u postgres psql -c "CREATE USER postgres_exporter WITH PASSWORD 'pg_password';"
sudo -u postgres psql -c "ALTER USER postgres_exporter SET SEARCH_PATH TO postgres_exporter,pg_catalog;"
sudo -u postgres psql -c "GRANT pg_monitor TO postgres_exporter;"
```

Para conectar luego este Engine con Prometheus, refiérase a:
👉 [06-exporters.md](06-exporters.md)

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [06-exporters.md](06-exporters.md)
