### 创建pod
kubectl run POD_NAME --image=

### 暴露deployment， 创建service
kubectl expose deployment NAME --port 80


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

