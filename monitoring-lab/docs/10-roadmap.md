# 10. Roadmap (Mejoras Futuras)

Toda plataforma robusta debe evolucionar en el tiempo. Aquí planteamos un pipeline de escala Cloud Native.

## A Integrar:
- [ ] **1. Alertmanager (Crítico)**: Añadir módulo Prom/Alertmanager para inyectar reglas complejas y enrutarlas directo como Push-Notifications hacia Canales de Slack y/o Equipos PagerDuty.
- [ ] **2. Logging Unificado (Loki / Promtail)**: Ingerir los outputs binarios del log de Postgre (`/var/log/postgresql/`) consolidando Logs + Dashboard en el mismo endpoint visual.
- [ ] **3. Escalabilidad Multi-Nodo (Patroni)**: Clusterizar Postgre en Active/Standby configurado por etcd/zookeeper aumentando resiliencia.
- [ ] **4. Autenticación Central (SSO)**: Limitar los perfiles administradores en Grafana acoplándose a un Active Directory / Okta Empresarial.

Fin de Documentación técnica y despliegue.

Volver al README: 👉 [../README.md](../README.md)
