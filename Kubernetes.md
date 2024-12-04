# 基础框架
## Master
API server:解析用户请求

etcd:存储集群状态
Scheduler:容器调度

### Controller manager
维护集群状态的kube-controller-manager组件，由Replication Controller,Node Controller,ResourceQuota Controller,Namespace Controller,ServiceAccount Controller,Token Controller,Service Controller,Endpoint Controller

Replication Controller:控制Pod副本数量一致

Node Controller:管控节点

ResourceQuota Controller:管控资源配额不会超量占用

Namespace Controller:通过API Server创建新的Namespace并保存在Etcd中

ServiceAccount Controller:服务账号控制器

Token Controlle:令牌控制器

Service Controller:监听Service变化

Endpoint Controller:负责维护和生成所有的Endpoint对象的控制器
## Node
Kubelet:接受Master指令并执行

Kube-proxy:外部网络访问

Container Runtime

# 资源类型

Pod

Damonset

Deployment

Services

Configmaps

Endpoints

Nodes

Replicasets

Namespace

ServiceAccount

ResourceQuota

PV

PVC

## Ingress
path通过path知道用户使用的是什么类型的服务
通过service和port知道调用哪些端口

Job

CronJob

HPA

# 使用
## yaml文件解读
1. apiversion
使用的api版本，v1，apps/v1,batch/v1,autoscaling/v1,networking.k8s.io/v1,rbac.autoorization.k8s.io/v1

2. kind
创建的类别

3. metadata
帮助唯一识别对象的数据

4. spec
期望的状态
## 命令
```shell
导出配置文件
kubectl get yaml -o deployment xxxx > xxxx.yaml
kubectl apply -f xxx.yaml

```


### Port详解
port:K8S访问Service的端口

nodePort:外部访问K8S集群中Service的端口

targetPort:Pod的端口

containerPort:Pod内部容器的端口

### Label和Selector

### Taint和Affinity
Affinity可以使得Pod调度到某一类节点
Taint可以使某一类节点排斥Pod

Toleration:

#### Taint
给节点打上Key、Value、和Effect

## 网络配置
clusterIP:集群内部访问

nodePort:通过IP:Port访问

ingress:通过Ingress分发流量

### 配置外部访问

Kubernetes访问外部服务

配置ExternalName
Endpoint
https://blog.csdn.net/iiothub/article/details/136035831