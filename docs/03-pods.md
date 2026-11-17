# 3. Pods en Kubernetes

## 3.1 ¿Qué es un Pod?
Un **Pod** es la **unidad atómica mínima y más pequeña de computación** que se puede crear y gestionar en Kubernetes.

En lugar de desplegar contenedores directamente en el sistema operativo, Kubernetes siempre envuelve los contenedores dentro de un Pod.

### Características fundamentales de un Pod:
* **Entorno compartido:** Uno o varios contenedores dentro de un mismo Pod comparten:
  * **La misma dirección IP de red:** Se comunican entre sí a través de `localhost` y comparten los mismos puertos.
  * **Almacenamiento compartido:** Los volúmenes montados en el Pod pueden ser leídos y escritos por todos los contenedores del Pod.
  * **Espacio de nombres IPC y tiempo de vida:** Nacen y mueren juntos.
* **Naturaleza efímera:** Los Pods son "ganado, no mascotas" (*cattle, not pets*). Tienen un ciclo de vida temporal: si un Pod muere, no se resucita, sino que se destruye y los controladores crean uno completamente nuevo con una IP diferente.

---

## 3.2 Creación de Pods

### Método 1: Imperativo (Para pruebas rápidas)
Se ejecuta directamente en la terminal sin necesidad de crear archivos:

```bash
kubectl run pod-nginx --image=nginx:alpine --port=80
```

### Método 2: Declarativo con Manifiesto YAML (Recomendado)
Crea un archivo llamado `pod-servidor.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-servidor-web
  labels:
    app: tienda
    modulo: frontend
    entorno: produccion
spec:
  containers:
    - name: contenedor-nginx
      image: nginx:1.24-alpine
      ports:
        - containerPort: 80
      env:
        - name: ENTORNO_TRABAJO
          value: "Laboratorio ASIR"
      resources:
        requests:
          memory: "64Mi"
          cpu: "50m"
        limits:
          memory: "128Mi"
          cpu: "200m"
```

Aplicar el archivo en el clúster:
```bash
kubectl apply -f pod-servidor.yaml
```

---

## 3.3 Ciclo de Vida y Estados de un Pod

Un Pod atraviesa diferentes fases a lo largo de su existencia:

| Estado (*Phase*) | Significado Técnico |
| :--- | :--- |
| **`Pending`** | El Pod ha sido aceptado por el API Server pero el planificador (`scheduler`) aún no lo ha asignado a un nodo o está descargando la imagen Docker. |
| **`Running`** | El Pod está asignado a un nodo y al menos un contenedor está en ejecución o arrancando. |
| **`Succeeded`** | Todos los contenedores del Pod han terminado con éxito (código de salida 0). Típico de tareas por lotes (*Jobs*). |
| **`Failed`** | Todos los contenedores han terminado y al menos uno de ellos falló (código distinto de 0). |
| **`CrashLoopBackOff`** | Error habitual: El contenedor arranca, sufre un fallo inmediato (crash) y K8s intenta reiniciarlo en bucle con retardos crecientes. |

---

## 3.4 Comandos Esenciales de Gestión e Inspección

```bash
# 1. Listar los pods y ver su estado, IP asignada y nodo
kubectl get pods -o wide

# 2. Filtrar pods por etiquetas (labels)
kubectl get pods -l app=tienda

# 3. Inspeccionar en profundidad un Pod (muestra eventos del sistema y causas de error)
kubectl describe pod pod-servidor-web

# 4. Ver los registros (logs) del contenedor en tiempo real
kubectl logs pod-servidor-web -f

# 5. Abrir una shell interactiva dentro del contenedor del Pod
kubectl exec -it pod-servidor-web -- sh

# 6. Acceder al Pod desde el navegador local mediante redirección de puertos
# (Permite abrir http://localhost:8080 en tu navegador web)
kubectl port-forward pod/pod-servidor-web 8080:80

# 7. Eliminar el Pod
kubectl delete pod pod-servidor-web
# O bien eliminando desde el manifiesto:
kubectl delete -f pod-servidor.yaml
```

---
[⬅️ 2. Instalación](02-instalacion.md) | [Volver al Índice](../README.md) | [Siguiente: 4. ReplicaSets ➡️](04-replica-sets.md)
