## contexts
### choose to use specific config file
kubectl --kubeconfig CONFIG_FILE config get-contexts

### List only contexts name
kubectl --kubeconfig CONFIG_FILE config get-contexts -oname

### switch context
kubectl config use-context CONTEXT_NAME

### switch namespace
kubectl config set-context --current --namespace=NAMESPACE

### view config
kubectl config view [-o yaml/json] [--raw]

## Base64
### encoded
echo "STRING" | base64
### decoded 
echo "STRING" | base64 -d

## selector & template
selector 找到符合这些 label 的 Pod，并把它们当成自己创建和管理的 Pod
template 是 Pod 模板 意思是：“我要创建的 Pod 长什么样？” 包括：labels, container, image, port, volumeMount, 等等
selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx

## stateful 和 deployment 区别
Deployment 适合无状态应用
StatefulSet 适合有状态应用（需要固定身份和存储）

Deployment 创建的pod名字是随机的，而statefulSet创建的是固定的
Deployment 所有 Pod：可以共享同一个 PVC，或完全不使用存储，不会自动创建 PVC
StatefulSet 每个 Pod：自动生成独立 PVC，不共享存储，PVC 不会随 Pod 删除
Deployment 适合：Nginx Web API 微服务 后端应用 无状态 worker
StatefulSet 适合：MySQL PostgreSQL Redis Apache Kafka

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

