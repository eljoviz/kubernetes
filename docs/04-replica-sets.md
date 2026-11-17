# 4. ReplicaSets en Kubernetes

## 4.1 ¿Qué es un ReplicaSet y por qué es necesario?
En el apartado anterior vimos que los Pods son efímeros. Si creamos un Pod suelto y el proceso falla o alguien lo elimina manualmente, **el Pod se pierde para siempre**.

Un **ReplicaSet** es un controlador cuyo propósito exclusivo es **garantizar que un número específico y constante de réplicas idénticas de un Pod se encuentren siempre en ejecución y disponibles** en el clúster.

### Capacidades clave:
1. **Autoreparación (*Self-Healing*):** Supervisa el clúster en tiempo real. Si un Pod muere, crea inmediatamente otro para mantener la cuota deseada.
2. **Escalado horizontal:** Permite aumentar o reducir la cantidad de instancias de un servicio en segundos cambiando únicamente una cifra.
3. **Desacoplamiento mediante etiquetas (*Selectors*):** El ReplicaSet no está atado a Pods fijos, sino que gestiona cualquier Pod que coincida con sus etiquetas (`matchLabels`).

---

## 4.2 Manifiesto YAML de un ReplicaSet

Crea un archivo llamado `replicaset-web.yaml`:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-frontend
  labels:
    app: tienda-online
    capa: frontend
spec:
  # 1. NÚMERO DE RÉPLICAS DESEADAS
  replicas: 3

  # 2. SELECTOR DE ETIQUETAS
  selector:
    matchLabels:
      app: tienda-online
      capa: frontend

  # 3. PLANTILLA DEL POD QUE DEBE CREAR SI FALTAN
  template:
    metadata:
      labels:
        app: tienda-online
        capa: frontend
    spec:
      containers:
        - name: web
          image: nginx:alpine
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "32Mi"
              cpu: "50m"
            limits:
              memory: "64Mi"
              cpu: "100m"
```

Aplicar el ReplicaSet:
```bash
kubectl apply -f replicaset-web.yaml
```

---

## 4.3 Demostración de Autocuración (*Self-Healing*)

Vamos a comprobar cómo actúa Kubernetes de forma autónoma:

```bash
# 1. Comprobar que el ReplicaSet ha creado exactamente 3 Pods
kubectl get replicasets
kubectl get pods -l app=tienda-online

# Salida:
# NAME                        READY   STATUS    RESTARTS   AGE
# replicaset-frontend-abc12   1/1     Running   0          20s
# replicaset-frontend-def34   1/1     Running   0          20s
# replicaset-frontend-ghi56   1/1     Running   0          20s

# 2. PRUEBA DE FUEGO: Eliminar un Pod de forma manual
kubectl delete pod replicaset-frontend-abc12

# 3. Comprobar de inmediato:
kubectl get pods -l app=tienda-online
```

> [!NOTE] ¿Qué ha ocurrido?
> En el milisegundo en que el Pod fue borrado, el `kube-controller-manager` detectó que solo quedaban 2 Pods activos frente a los 3 definidos en el campo `replicas`. Automáticamente ordenó al planificador crear un nuevo Pod (`replicaset-frontend-xyz89`) para restablecer el estado deseado.

---

## 4.4 Escalado de Réplicas

### Vía Imperativa (Rápida en terminal):
```bash
# Aumentar a 5 réplicas
kubectl scale replicaset replicaset-frontend --replicas=5

# Comprobar la creación de los 2 nuevos pods
kubectl get pods -l app=tienda-online

# Reducir a 2 réplicas
kubectl scale replicaset replicaset-frontend --replicas=2
```

### Vía Declarativa (Buenas prácticas):
Edita el archivo `replicaset-web.yaml`, cambia `replicas: 3` por `replicas: 4` y vuelve a aplicar:
```bash
kubectl apply -f replicaset-web.yaml
```

---

## 4.5 ¿Por qué casi nunca se usan ReplicaSets directamente?
Aunque los ReplicaSets garantizan la cantidad de réplicas, **carecen de mecanismos para actualizar versiones de software**. Si cambias la imagen de `nginx:1.24` a `nginx:1.25` en el YAML de un ReplicaSet, este **no actualizará los Pods que ya están corriendo**.

Para solucionar esto, Kubernetes introduce el objeto supremo para aplicaciones sin estado: **el Deployment**.

---
[⬅️ 3. Pods](03-pods.md) | [Volver al Índice](../README.md) | [Siguiente: 5. Deployments ➡️](05-deployments.md)
