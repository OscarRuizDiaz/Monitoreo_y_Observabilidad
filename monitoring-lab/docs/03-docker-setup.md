# 03. Configuración de Docker

Instalaremos la versión oficial de Docker Engine en Ubuntu a través de su repositorio firmado y seguro.

## 1. Agregar llaves oficiales y Repositorio PPA
```bash
sudo apt update
sudo apt install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 2. Instalar Docker CE & Plugins
```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 3. Seguridad de Ejecución
Añadimos al usuario actual del sistema al grupo docker para evitar el uso del usuario `root` (`sudo`) constantemente:
```bash
sudo usermod -aG docker $USER
newgrp docker
```

Para ver su implementación en el stack ver: 👉 [04-monitoring-stack.md](04-monitoring-stack.md)

Volver al README: 👉 [../README.md](../README.md)
Siguiente: 👉 [04-monitoring-stack.md](04-monitoring-stack.md)
