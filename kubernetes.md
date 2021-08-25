## kubernetes可以做什么
使用Web服务，用户希望应用程序能够7*24小时全天运行，开发人员希望每天多次部署新的应用版本。通过应用容器化可以实现这些目标，使应用简单、快捷的方式更新和发布，也能实现热更新、迁移等操作。使用Kubernetes能确保程序在任何时间、任何地方运行，还能扩展更多有需求的工具/资源。Kubernetes积累了Google在容器化应用业务方面的经验，以及社区成员的实践，是能在生产环境使用的开源平台。
#### step1 使用Minikube 部署 Kubernetes 集群
```shell
#下载kubectl
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.6.4/bin/linux/amd64/kubectl
chmod +x kubectl

#安装minikube
install minikube

#查看版本信息
minikube version

#运行minikube start命令启动集群：
minikube start --wait=false

#集群的详细信息及其健康状态
kubectl cluster-info

#将容器部署到集群上
kubectl create deployment first-deployment --image=katacoda/docker-http-server

#查看集群中的节点
kubectl get nodes

#为容器提供动态端口
kubectl expose deployment first-deployment --port=80 --type=NodePort

# Minikube 和命令启用仪表板
minikube addons enable dashboard

#通过部署以下 YAML 定义使 Kubernetes 仪表板可用。这应该只用于 Katacoda。
kubectl apply -f /opt/kubernetes-dashboard.yaml

#查看仪表板启动的进度
kubectl get pods -n kubernetes-dashboard -w

```



