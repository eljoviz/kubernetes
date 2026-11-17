# ☸️ Kubernetes (K8s) – Guía y Prácticas de Laboratorio

[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Minikube](https://img.shields.io/badge/Minikube-1D63ED?style=for-the-badge&logo=minikube&logoColor=white)](https://minikube.sigs.k8s.io/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)](https://www.kernel.org/)
[![ASIR](https://img.shields.io/badge/FP_Grado_Superior-2º_ASIR-blue?style=for-the-badge)](https://github.com/eljoviz/kubernetes)

Repositorio de formación técnica, apuntes y casos prácticos de laboratorio sobre orquestación de contenedores y despliegue de infraestructuras con **Kubernetes (K8s)**.

---

## 📋 Índice de Contenidos

Haz clic en cualquiera de los apartados para acceder a su documentación completa con teoría, diagramas y comandos prácticos paso a paso:

| # | Apartado | Descripción y Conceptos Clave | Documento |
| :-: | :--- | :--- | :-: |
| **1** | **Introducción a Kubernetes** | Qué es K8s, diferencias con Docker individual, arquitectura de clúster (*Control Plane* vs *Worker Nodes*) y paradigma declarativo YAML. | [📖 Ver Apartado 1](docs/01-introduccion.md) |
| **2** | **Instalación del Entorno** | Requisitos previos, instalación de la CLI oficial `kubectl`, arranque de clúster local con **Minikube** y comprobación de nodos. | [📖 Ver Apartado 2](docs/02-instalacion.md) |
| **3** | **Pods** | Unidad básica de cómputo, compartición de red e IP, creación imperativa (`run`) y declarativa (YAML), ciclo de vida e inspección. | [📖 Ver Apartado 3](docs/03-pods.md) |
| **4** | **ReplicaSets** | Controlador de disponibilidad, selección por etiquetas (`matchLabels`), prueba de autocuración (*Self-Healing*) y escalado de réplicas. | [📖 Ver Apartado 4](docs/04-replica-sets.md) |
| **5** | **Deployments** | Gestión avanzada de aplicaciones, actualizaciones progresivas (*Rolling Updates*) sin caída de servicio, historial de versiones y *Rollback*. | [📖 Ver Apartado 5](docs/05-deployments.md) |

---

## 🏛️ Arquitectura General

```mermaid
flowchart LR
    subgraph CP["Plano de Control (Master)"]
        direction TB
        API["kube-apiserver"]
        ETCD[("etcd")]
        SCHED["kube-scheduler"]
        CM["controller-manager"]
        API <--> ETCD
        API <--> SCHED
        API <--> CM
    end

    subgraph WN["Nodo de Trabajo (Worker)"]
        direction TB
        K["kubelet"]
        KP["kube-proxy"]
        CR["Container Runtime"]
        subgraph Pods["Pods"]
            P1["Pod 1"]
            P2["Pod 2"]
        end
        K --> CR --> Pods
    end

    Admin["kubectl"] -->|Comandos HTTPS| API
    API <-->|Control| K
```

---

## 📁 Estructura del Repositorio

El repositorio se organiza siguiendo las mejores prácticas de documentación modular:

```text
kubernetes/
├── README.md               # Portada principal e índice navegable del repositorio
├── docs/                   # Carpeta técnica con los temas desarrollados
│   ├── 01-introduccion.md  # 1. Introducción, conceptos y arquitectura
│   ├── 02-instalacion.md   # 2. Instalación de kubectl y Minikube
│   ├── 03-pods.md          # 3. Gestión y ciclo de vida de Pods
│   ├── 04-replica-sets.md  # 4. Controladores de réplicas y Self-Healing
│   └── 05-deployments.md   # 5. Despliegues continuos, Rolling Updates y Rollback
├── img/                    # Esquemas visuales y diagramas de arquitectura
│   ├── .gitkeep
│   └── arquitectura_k8s.svg
└── LICENSE                 # Licencia de código abierto GPLv3
```

---

## ⚡ Chuleta Rápida de Comandos Esenciales

```bash
# Nodos y Clúster
kubectl cluster-info
kubectl get nodes -o wide

# Pods
kubectl run mi-pod --image=nginx:alpine
kubectl get pods -o wide
kubectl describe pod <nombre-pod>
kubectl logs <nombre-pod> -f
kubectl exec -it <nombre-pod> -- sh
kubectl delete pod <nombre-pod>

# ReplicaSets y Deployments
kubectl apply -f <archivo.yaml>
kubectl get deployments,replicasets,pods
kubectl scale deployment/<nombre> --replicas=5
kubectl rollout status deployment/<nombre>
kubectl rollout history deployment/<nombre>
kubectl rollout undo deployment/<nombre>
```

---

## 👤 Autor
* **Alumno:** Jose Vizuete González
* **Ciclo:** 2º Curso – C.F.G.S. Administración de Sistemas Informáticos en Red (ASIR)
* **Repositorio GitHub:** [https://github.com/eljoviz/kubernetes](https://github.com/eljoviz/kubernetes)
