# 08. Configuración de Grafana

Este es el último eslabón de la Cadena. Traducir series de tiempo crudas TSDB desde PromQL hacia gráficos interactivos.

## 1. Autenticación First-Boot
- Entrar en: `http://IP_DEL_SERVIDOR:3000`
- Usuario default: `admin`
- Pass (via ENV Compose): `admin`

## 2. Conectar Data Source (Prometheus)
1. Navega: **Administration -> Data Sources -> Add data source**.
2. Selecciona **Prometheus**.
3. En la caja de "URL" coloca la IP de host al motor Docker: `http://localhost:9090` (usamos network mode host en Docker).
4. Guardar y probar conexión.

## 3. Dashboards Altamente Recomendados
Para evitar fabricar dashboards de 0, importar con los IDs aprobados:
En **Create (+) -> Import**:
- SO Linux general: **`1860`** (Node Exporter Full)
- PostgreSQL Status global: **`9628`** (PostgreSQL Database)
- Advanced Query analysis: **`14114`** (PostgreSQL pg_stat_statements)

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [09-troubleshooting.md](09-troubleshooting.md)
