# 04. Despliegue del Stack(Prometheus/Grafana)

El bloque central de monitoreo y visualización se maneja nativamente vía Docker Compose en contenedores inmutables.

## 1. Archivos Base de Referencia
Toda la configuración declarativa se encuentra aquí:
Para ver la estructura del compose: 👉 [../docker-compose.yml](../docker-compose.yml)  
Para ver el prometheus config: 👉 [../prometheus/prometheus.yml](../prometheus/prometheus.yml)

## 2. Iniciar el Stack
Acceder al directorio raíz `monitoring-lab` y ejecutar el cluster en modo background:
```bash
cd monitoring-lab
docker compose up -d
```

## 3. Verificación y Persistencia
Comprobar el estado de los contenedores subyacentes:
```bash
docker compose ps
```
- Node Exporter estará operativo en `localhost:9100`.
- Prometheus escuchará en `localhost:9090`.
- Grafana montará su web en el puerto `3000`.

Todos los datos se mantendrán seguros gracias a los volúmenes (`prometheus_data` y `grafana_data`).

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [05-postgresql.md](05-postgresql.md)
