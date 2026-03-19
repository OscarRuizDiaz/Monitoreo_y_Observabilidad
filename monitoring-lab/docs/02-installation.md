# 02. Instalación del Sistema Base Ubuntu

Esta guía asume que utilizarás **Hyper-V** para el despliegue del entorno base.

## 1. Configuración de Hyper-V
1. Crear Máquina Virtual "Generación 2".
2. Asignar 4GB RAM Inicial (Habilitar memoria dinámica).
3. Red: Conectar a un Virtual Switch Externo con salida a WAN.

## 2. Configuración de Sistema Operativo
- Versión recomendada: **Ubuntu Server 24.04 LTS**.
- Opciones de software: Seleccionar únicamente "Ubuntu Server".
- Habilitar **OpenSSH Server** explícitamente durante el asistente.

## 3. Post-Instalación
```bash
sudo apt update && sudo apt upgrade -y
# Configurar hostname e identificar el entorno
sudo hostnamectl set-hostname monitor-lab
sudo reboot
```

## 4. Buenas Prácticas Aplicadas
- **Networking**: Se habilitará UFW posteriormente bloqueando puertos entrantes por defecto.

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [03-docker-setup.md](03-docker-setup.md)
