![image-20240919095223508](/Users/xiadong3/Documents/typora/k8s/assets/image-20240919095223508.png)

在 Kubernetes（K8s）集群中，**Master 节点（控制平面节点）**和**Worker 节点（工作节点）**是两个主要的节点类型，它们各自承担不同的职责，共同确保集群的正常运行和应用的部署。以下是对这两种节点的详细介绍：

### **1. Master 节点（控制平面节点）**

Master 节点负责管理和控制整个 Kubernetes 集群。它包含一系列控制平面组件，主要职责包括集群的调度、管理、监控以及协调工作节点的活动。具体组件和功能如下：

- **API Server（API 服务器）**：
  - 集群的入口点，所有的 REST 请求（如 `kubectl` 命令、内部组件的通信）都通过 API Server 进行。
  - 负责验证和配置数据，确保集群状态的声明式管理。
- **etcd**：
  - 分布式键值存储，用于保存 Kubernetes 集群的所有数据，包括配置、状态和元数据。
  - 提供高可用性和一致性的数据存储，确保集群状态的可靠性。
- **Scheduler（调度器）**：
  - 负责将待调度的 Pod 分配到合适的 Worker 节点上。
  - 根据资源需求、约束条件、策略等因素进行智能调度，优化资源利用。
- **Controller Manager（控制器管理器）**：
  - 运行多个控制器，如节点控制器、复制控制器、部署控制器等。
  - 负责监控集群状态，并执行必要的操作以确保集群达到期望状态。
- **Cloud Controller Manager（云控制器管理器，可选）**：
  - 用于与底层云服务提供商（如 AWS、GCP、Azure）进行集成。
  - 管理云资源，如负载均衡、存储卷等。

### **2. Worker 节点（工作节点）**

Worker 节点是 Kubernetes 集群中实际运行应用容器的地方。每个 Worker 节点上运行多个 Pod，负责执行用户的工作负载。主要组件和职责如下：

- **kubelet**：
  - 每个 Worker 节点上运行的主要代理，负责与 Master 节点通信，接收调度指令，并管理本地的 Pod 和容器。
  - 监控容器的生命周期，确保它们按照预期运行。
- **kube-proxy**：
  - 负责网络代理和负载均衡，管理节点上的网络规则，确保服务的网络访问和负载分发。
  - 支持 Kubernetes 服务发现和跨 Pod 通信。
- **容器运行时（Container Runtime）**：
  - 负责实际运行容器的环境，如 Docker、containerd、CRI-O 等。
  - 提供容器的创建、管理和销毁等功能。
- **Pod 和容器**：
  - Worker 节点上实际运行的应用单元，由一个或多个容器组成。
  - 每个 Pod 都有自己的网络、存储和资源隔离。

### **Master 节点与 Worker 节点的协作**

- **调度与执行**：
  - Master 节点的 Scheduler 根据资源需求和策略，将 Pod 调度到合适的 Worker 节点上。
  - Worker 节点的 kubelet 接收调度指令，拉取容器镜像并启动容器。
- **资源管理**：
  - Master 节点通过 API Server 和 etcd 维护整个集群的状态和配置。
  - Worker 节点报告自身的资源使用情况，确保资源的高效利用和负载均衡。
- **高可用性与扩展性**：
  - Master 节点通常配置为高可用模式，避免单点故障。
  - Worker 节点可以根据负载动态添加或移除，实现集群的弹性扩展。

### **总结**

- **Master 节点**：负责集群的整体管理和控制，包括调度、监控、配置管理等核心功能。
- **Worker 节点**：负责实际运行应用容器，执行 Master 节点下发的任务，提供计算和存储资源。





# 网络配置

学习

DHCP

DNS（可不可以自己配置host-->ip，而不经过dns）

IP

```bash
TYPE=Ethernet
BOOTPROTO=static
NAME=eth0
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```





### 控制器的作用

在 Kubernetes 中，控制器是一种负责监视和调节集群状态的进程。它们确保集群的实际状态（例如，正在运行的 Pod 数量）匹配期望的状态（如用户通过配置所定义的状态）。控制器通过不断循环监视、更新和调整来维护状态。常见的控制器类型包括：

1. **Deployment 控制器**：管理 Pod 和 ReplicaSets，确保指定数量的副本始终运行。
2. **ReplicaSet 控制器**：维护一定数量的 Pod 副本。
3. **StatefulSet 控制器**：用于管理有状态的应用。
4. **DaemonSet 控制器**：确保所有（或某些）Node 上都运行一个 Pod 的副本。
5. **Job 控制器**：管理短暂的一次性任务。

控制器的核心是通过 API 服务器观察集群状态，根据现有状态与期望状态的差异执行必要的操作以达到期望状态。

### YAML 文件的作用

YAML 文件在 Kubernetes 中用作资源配置的标准格式。通过 YAML 文件，用户可以定义、配置和管理 Kubernetes 的各种资源，如 Pods、Services、Deployments 和其他控制器管理的资源。这些文件通常包括资源的类型、元数据、所需的配置和行为说明。

例如，一个简单的 Deployment 的 YAML 配置文件可能如下所示：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

在这个例子中，YAML 文件定义了一个 Deployment 资源，期望运行三个副本的 Nginx 容器。

### 控制器与 YAML 文件的关系

- **定义与执行**：YAML 文件提供了声明性的配置，即定义“什么是期望的状态”，而控制器则是执行者，负责实现这一状态。
- **持续监控与管理**：控制器不断监视由 YAML 文件定义的资源的状态，确保实际状态与文件中定义的期望状态相符。
- **自动化和自我修复**：当检测到偏差时，控制器会自动采取行动来修复这些偏差，如扩展或替换故障的 Pod。

总的来说，YAML 文件是用户与 Kubernetes 集群交互的界面，而控制器则是确保用户定义的意图得以实现的机制。用户通过编写和应用 YAML 文件来表达他们的意图，控制器则不断地工作以确保集群的状态匹配这些定义。