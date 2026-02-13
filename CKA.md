### 创建pod
kubectl run POD_NAME --image=

### 暴露deployment， 创建service
kubectl expose deployment NAME --port 80

### Install Helm chart minio/operator into the new Namespace. The Helm Release should be called minio-operator
helm install minio-operator minio/operator --namespace=minio

### 看pod yaml 的ownerReference可以看到是通过谁创建的（deployment， statefulset， etc）， 如果没有就是手动创建的

### scale statefulset
kubectl scale statefulset XXX --replicas=N

### 服务质量类（Quality of Service class，QoS class）. Kubernetes 在 Node 资源不足时使用 QoS 类来就驱逐 Pod 作出决定


### 流程图
客户端浏览器 / curl
      |
      v
+-----------------------------+
| NodeIP:30080 / NodePort     |  ← NodePort Service（ingress-nginx-controller）
+-----------------------------+
      |
      v
+-----------------------------+
| Ingress Controller Pod      |  ← nginx / traefik / haproxy
| containerPort 80 / 443      |
+-----------------------------+
      |
      v
+-----------------------------+
| Ingress API 资源            |  ← ingress world
| spec.ingressClassName: nginx|
| host/path 规则              |
+-----------------------------+
      |
      v
+-----------------------------+
| Backend Service             |  ← europe-svc / asia-svc
| ClusterIP + service port    |
+-----------------------------+
      |
      v
+-----------------------------+
| Pod                        |  ← Deployment / ReplicaSet 管理
| containerPort               |
+-----------------------------+

