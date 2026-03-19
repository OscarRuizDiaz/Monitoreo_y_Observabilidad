# 09. Troubleshooting

Colección de problemas habituales detectados en las operaciones y despliegues.

## > Problema A: Prometheus targets DOWN o Disconnected
1. Verifícalo logueado en Prometheus web: `http://IP:9090/targets`.
2. Si Node Exporter está abajo: Comprueba que SELinux/AppArmor no intercepte el read-only o reinicia su stack.
3. Si el Postgres Exporter falla, chequear sistema principal: `sudo systemctl status postgres_exporter`.

## > Problema B: Errores Auth en base de datos Postgres
Error en CLI de docker: `pg_password authentication failed for user "postgres_exporter"`
Confirma tus credenciales preconfiguradas en el archivo principal: 👉 [../exporters/postgres_exporter/postgres_exporter.env](../exporters/postgres_exporter/postgres_exporter.env)
Valida recreando el rol `pg_monitor` en tu base de datos si fuese requerido.

## > Problema C: Tableros vacíos "No Data" o con pg_stat sin métricas
Asegúrate del timezone de la TSDB vs la PC donde abres Grafana.
Para fallos con *pg_stat*, verifica que la extensión cargó y aparece:
```bash
sudo -u postgres psql -c "\dx"
```
Valida que el Query file apunte correcto aquí: 👉 [../exporters/postgres_exporter/queries.yaml](../exporters/postgres_exporter/queries.yaml)

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [10-roadmap.md](10-roadmap.md)
