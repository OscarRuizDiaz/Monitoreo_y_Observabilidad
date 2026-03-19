# 07. Habilitando pg_stat_statements

Esta extensión oficial nos habilita a capturar e indexar métricas avanzadas granulares por cada Sentencia SQL en PostgreSQL.

## 1. Inserción en memoria (postgresql.conf)
Localiza y edita `/etc/postgresql/16/main/postgresql.conf`:

```conf
shared_preload_libraries = 'pg_stat_statements'
pg_stat_statements.track = all
pg_stat_statements.max = 10000
track_activity_query_size = 2048
```

## 2. Aplicar Reload y Extension
Reiniciar la Base de datos para alojar recursos y activar modulo:
```bash
sudo systemctl restart postgresql
sudo -u postgres psql -c "CREATE EXTENSION IF NOT EXISTS pg_stat_statements;"
```

## 3. Validar con Queries Customizadas
La recolección se enviará usando este YAML referenciado en memoria env:
👉 [../exporters/postgres_exporter/queries.yaml](../exporters/postgres_exporter/queries.yaml)

Con esto, Grafana expondrá un TOP 10 de queries pesadas consumiendo I/O y CPU.

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [08-grafana.md](08-grafana.md)
