# 2. Instalación del Entorno de Laboratorio

Para aprender y practicar con Kubernetes en entornos locales de desarrollo, no es necesario alquilar servidores en la nube. Se utilizan herramientas que empaquetan un clúster de Kubernetes en una máquina local (Debian / Ubuntu).

---

## 2.1 Herramientas Disponibles

* **Minikube:** La herramienta oficial recomendada por la comunidad para aprendizaje y desarrollo local. Crea un clúster de un solo nodo dentro de Docker o KVM.
* **`kubectl`:** La interfaz oficial de línea de comandos (CLI) para interactuar y controlar cualquier clúster de Kubernetes.
* **K3s:** Distribución ligera certificada por la CNCF creada por Rancher, óptima para entornos de producción en servidores modestos o Raspberry Pi.

En esta guía utilizaremos el tándem estándar de la industria para formación: **`kubectl` + Minikube**.

---

## 2.2 Requisitos Previos

Antes de comenzar, asegúrate de tener en tu equipo Linux:
* Al menos 2 CPUs (núcleos).
* 2 GB de memoria RAM libre (recomendado 4 GB).
* 20 GB de espacio en disco.
* Conexión a Internet.
* **Docker Engine** instalado y tu usuario añadido al grupo `docker`:
  ```bash
  sudo usermod -aG docker $USER && newgrp docker
  ```

---

## 2.3 Paso 1: Instalación de `kubectl`

`kubectl` descarga y envía los manifiestos YAML al `kube-apiserver`.

```bash
# 1. Descargar el binario oficial compilado para arquitectura amd64
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# 2. Dar permisos de ejecución al binario
chmod +x kubectl

# 3. Moverlo al directorio de ejecutables del sistema
sudo mv kubectl /usr/local/bin/

# 4. Verificar la versión instalada
kubectl version --client --output=yaml
```

> [!TIP] Autocompletado en Bash
> Para activar el autocompletado de comandos con la tecla Tabulador:
> ```bash
> echo 'source <(kubectl completion bash)' >> ~/.bashrc
> source ~/.bashrc
> ```

---

## 2.4 Paso 2: Instalación de Minikube

```bash
# 1. Descargar el paquete deb oficial de Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb

# 2. Instalar el paquete en Debian/Ubuntu
sudo dpkg -i minikube_latest_amd64.deb

# 3. Eliminar el instalador temporal
rm minikube_latest_amd64.deb

# 4. Iniciar Minikube indicando Docker como controlador
minikube start --driver=docker --cpus=2 --memory=3072
```

---

## 2.5 Paso 3: Verificación y Estado del Clúster

Una vez finalizado el arranque, comprobamos que el clúster está listo:

```bash
# Comprobar la información del plano de control
kubectl cluster-info

# Ver el estado de los nodos del clúster (debe aparecer en estado 'Ready')
kubectl get nodes -o wide

# Salida esperada:
# NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP   OS-IMAGE             KERNEL-VERSION
# minikube   Ready    control-plane   2m    v1.30.0   192.168.49.2  Ubuntu 22.04.4 LTS   ...
```

### Comandos útiles de gestión de Minikube:
```bash
# Ver estado del clúster
minikube status

# Detener el clúster (para ahorrar batería/memoria)
minikube stop

# Volver a arrancar el clúster existente
minikube start

# Abrir el panel de control gráfico web (Dashboard)
minikube dashboard

# Eliminar completamente el clúster de pruebas
minikube delete
```

---
[⬅️ 1. Introducción](01-introduccion.md) | [Volver al Índice](../README.md) | [Siguiente: 3. Pods ➡️](03-pods.md)
