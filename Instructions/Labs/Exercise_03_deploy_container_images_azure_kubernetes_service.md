---
Exercise:
  title: 练习：将容器映像部署到 Azure Kubernetes 服务
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# 练习 3 - 将应用程序部署到 Azure Kubernetes 服务 (AKS)

## 目标

此引导项目包括以下练习：

+ 练习 1：预配 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS)。
+ 练习 2：生成 Linux 和 Windows 容器映像并将其存储在 Azure 容器注册表。
+ **练习 3：** 将容器映像部署到 Azure Kubernetes 服务。
+ 练习 4：查看部署并取消预配所有资源。

在本练习中，将容器映像部署到 Azure Kubernetes 服务。

## 练习 3：将容器映像部署到 AKS 
在本练习中，你要将在本练习前面创建的两个容器映像部署到 AKS 群集。
>注意：为了完成本练习，你将需要一个 [Azure 订阅](https://azure.microsoft.com/free/)****。
> 对于未指定的任何属性，请使用默认值。
> **注意：** 在继续本练习之前，请确保 AKS 群集的预配已成功完成。

### 任务 1：创建自定义 AKS 命名空间
在此任务中，你将在本实验室前面创建的 AKS 群集上创建两个命名空间。

1. 在 Azure 门户的“**搜索**”文本框中，搜索并选择“**Kubernetes 服务**”。
1. 在“Kubernetes 服务”页上，选择“aks-01”********。
1. 在 aks-01 页的垂直中心菜单中选择“命名空间”********。
1. 在“aks-01 \| 命名空间”页上，选择“+ 创建”，然后在下拉菜单中选择“命名空间”************。
1. 在“创建命名空间”窗格的“名称”文本框中，输入 dev-node 并选择“创建”****************。
1. 在“aks-01 \| 命名空间”页上，选择“+ 创建”，然后在下拉菜单中选择“命名空间”************。
1. 在“创建命名空间”窗格的“名称”文本框中，输入 dev-dotnet 并选择“创建”****************。

### 任务 2：创建用于部署 Linux 映像的 Kubernetes 清单
在此任务中，你将创建一个 Kubernetes 清单，用于将 Linux 映像部署到 Linux 节点池

1. 在 Azure 门户中，选择 Cloud Shell 图标。
1. 确保 Cloud Shell 窗格左上角的下拉菜单中出现“Bash”****。
1. 在 Azure Cloud Shell 的 Bash 会话中，创建一个目录，该目录将托管用于基于 Linux 映像预配 Pod 的部署清单，然后通过运行以下命令从当前目录更改为该目录：

   ```bash
   mkdir ~/aks-l01
   cd ~/aks-l01
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，使用内置编辑器在 aks-l01 目录中创建一个名为 aks-deployment-l01.yaml 的文件，并将以下内容复制到该文件中：

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hellofromnode-deployment
     labels:
       environment: dev
       app: hellofromnode
   spec:
     replicas: 1
     template:
       metadata:
         name: hellofromnode
         labels:
           app: hellofromnode
       spec:
         nodeSelector:
           "kubernetes.io/os": linux
         containers:
         - name: hellofromnode
           image: ACR_NAME.azurecr.io/hellofromnode:v1.0
           resources:
             limits:
               cpu: 1
               memory: 800M
           ports:
             - containerPort: 80
     selector:
       matchLabels:
         app: hellofromnode
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: hellofromnode-service
   spec:
     type: LoadBalancer
     ports:
     - protocol: TCP
       port: 80
     selector:
       app: hellofromnode
   ```

   > 注意：部署将基于 Linux 容器映像在 AKS 群集中的 Linux 节点池中创建 Pod****。 此外，该清单还包含一个服务，该服务将通过端口 80 上的公共 IP 地址提供对部署中的 Pod 的负载均衡访问。

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，通过运行以下命令替换文件 aks-deployment-l01.yaml 中的 ACR_NAME 占位符****：

   ```azurecli
   ACR_RGNAME='acr-01-RG'
   ACR_NAME=$(az acr list --resource-group $ACR_RGNAME --query "[].name" --output tsv)
   sed -i "s/ACR_NAME/$ACR_NAME/" ./aks-deployment-l01.yaml
   ```

### 任务 3：创建用于部署 Windows 映像的 Kubernetes 清单
在此任务中，你将创建一个 Kubernetes 清单，用于将 Windows 映像部署到 Windows 节点池

1. 在 Azure Cloud Shell 的 Bash 会话中，创建一个目录，该目录将托管用于基于 Windows 映像预配 Pod 的部署清单，然后通过运行以下命令从当前目录更改为该目录：

   ```bash
   mkdir ~/aks-w01
   cd ~/aks-w01
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，使用内置编辑器在 aks-w01 目录中创建名为 aks-deployment-w01.yaml 的文件，并将以下内容复制到其中：

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hellofromdotnet-deployment
     labels:
       environment: dev
       app: hellofromdotnet
   spec:
     replicas: 1
     template:
       metadata:
         name: hellofromdotnet
         labels:
           app: hellofromdotnet
       spec:
         nodeSelector:
           "kubernetes.io/os": windows
         containers:
         - name: hellofromdotnet
           image: ACR_NAME.azurecr.io/hellofromdotnet:v1.0
           resources:
             limits:
               cpu: 1
               memory: 800M
           ports:
             - containerPort: 80
     selector:
       matchLabels:
         app: hellofromdotnet
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: hellofromdotnet-service
   spec:
     type: LoadBalancer
     ports:
     - protocol: TCP
       port: 80
     selector:
       app: hellofromdotnet
   ```

   > 注意：该部署将基于 Windows 容器映像在 AKS 群集中的 Windows 节点池中创建 Pod****。 此外，该清单还包含一个服务，该服务将通过端口 80 上的公共 IP 地址提供对部署中的 Pod 的负载均衡访问。

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，通过运行以下命令替换文件 aks-deployment-l01.yaml 中的 ACR_NAME 占位符：****

   ```azurecli
   sed -i "s/ACR_NAME/$ACR_NAME/" ./aks-deployment-w01.yaml
   ```

### 任务 4：使用 YAML 清单文件执行 AKS 部署
在此任务中，你要将这两个容器映像部署到目标 AKS 群集中它们相应的命名空间和节点池。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令连接到 AKS 群集：

   ```azurecli
   AKSRG='aks-01-RG'
   AKSNAME='aks-01'
   az aks get-credentials --resource-group $AKSRG --name $AKSNAME
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令验证连接是否成功：

   ```kubectl
   kubectl get nodes
   ```

   > 注意：命令的输出应包括所有 AKS 节点（本例中为四个）的列表****。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令，在 dev-node 命名空间的相应 YAML 清单文件中创建定义的第一个部署：****

   ```kubectl
   cd ~/aks-l01
   kubectl apply -f aks-deployment-l01.yaml -n=dev-node
   ```

   > 注意：继续执行下一步，无需等待部署完成****。 预配所有资源可能需要几分钟时间。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令，创建在相应的 YAML 清单文件中定义的第二个部署：

   ```kubectl
   cd ~/aks-w01
   kubectl apply -f aks-deployment-w01.yaml -n=dev-dotnet
   ```

   > 注意：继续执行下一步，无需等待部署完成****。 预配所有资源可能需要几分钟时间。

