---
Guided Exercise:
  title: 引导式练习：将应用程序部署到 AKS
---
# 引导式练习 - 将应用程序部署到 Azure Kubernetes 服务 (AKS)

## 目标

此引导式练习包括以下活动：

+ 练习 1：预配 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS)
+ 练习 2：生成 Linux 和 Windows 容器映像并将其存储在 ACR 中
+ 练习 3：将容器映像部署到 AKS 
+ 练习 4：查看部署并取消预配所有资源

## 练习 1：预配 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS)
在本练习中，你将创建 Azure 容器注册表和 AKS 群集。


>**注意**：为了完成本练习，你将需要一个 [Azure 订阅](https://azure.microsoft.com/free/)。
> 对于未指定的任何属性，请使用默认值。


### 任务 1：创建 Azure 容器注册表
在此任务中，你将创建 Azure 容器注册表

1. 在计算机中，打开 Web 浏览器窗口并导航到 Azure 门户 (https://portal.azure.com)。
1. 出现提示时，使用在 Azure 订阅中具有所有者角色的用户帐户登录，你将在本练习中使用它。 
1. 登录到 Azure 门户。 
1. 在 Azure 门户的“**搜索**”文本框中，搜索并选择“**容器注册表**”。
1. 在“**容器注册表**”页上，选择“ **+ 创建**”并指定以下设置：

    |设置|值|
    |---|---|
    |订阅|你将在此练习中使用的 Azure 订阅的名称|
    |资源组|新资源组的名称 **acr-01-RG**|
    |注册表名称|由 5 到 50 个字母数字字符组成的任何有效、全局唯一的名称|
    |区域|可在其中创建 Azure 容器注册表和 AKS 群集的任何 Azure 区域|
    |可用性区域|无 |
    |SKU|**基本**|

1. 在“**容器注册表**”页上，选择“**查看 + 创建**”，然后在“**查看 + 创建**”选项卡上，选择“**创建**”。

   > **注意：** 继续下一个练习，无需等待 Azure 容器注册表的预配完成。

### 任务 2：创建 Azure 虚拟网络和 AKS 群集
在此任务中，你将创建一个 Azure 虚拟网络，并将包含 Windows 节点池的 AKS 群集部署到该虚拟网络中。

> **注意：** 虽然可以在预配 AKS 群集时创建虚拟网络，但这不是典型的方案。 更重要的是，将 AKS 群集部署到现有虚拟网络需要一些你应该熟悉的其他注意事项。

1. 在 Azure 门户的“**搜索**”文本框中，搜索并选择“**虚拟网络**”。
1. 在“**虚拟网络**”页上，选择“ **+ 创建**”，然后在“**创建虚拟网络**”页的“**基本信息**”选项卡上，指定以下设置：

    |设置|值|
    |---|---|
    |订阅|在第一个练习中选择的 Azure 订阅的名称|
    |资源组|新资源组的名称 **aks-01-RG**|
    |虚拟网络名称|**vnet-01**|
    |区域|在本练习的第一个练习中选择的同一 Azure 区域|

1. 在“**创建虚拟网络**”页的“**基本信息**”选项卡上，选择“**下一步**”。
1. 在“**创建虚拟网络**”页的“**安全性**”选项卡上，接受默认设置并选择“**下一步**”。
1. 在“**创建虚拟网络**”页的“**IP 地址**”选项卡上，确保 IP 地址空间设置为 **10.0.0.0/16**，删除**默认**子网，选择“**查看 + 创建**”，然后在“**查看 + 创建**”选项卡上选择“**创建**”。

   > **注意：** 创建虚拟网络应该只需几秒钟，因此你应该能够直接转到下一步。

1. 在 Azure 门户的“**搜索**”文本框中，搜索并选择“**Kubernetes 服务**”。
1. 在“**Kubernetes 服务**”页上，选择“ **+ 创建**”，在下拉列表中选择“**创建 Kubernetes 群集**”，然后在“**创建 Kubernetes 群集**”页的“**基本信息**”选项卡上指定以下设置：

    |设置|值|
    |---|---|
    |订阅|在第一个练习中选择的 Azure 订阅的名称|
    |资源组|**aks-01-RG**|
    |群集预设配置|开发/测试|
    |Kubernetes 群集名称|**aks-01**|
    |区域|在第一个练习中选择的同一 Azure 区域|
    |可用性区域|无 |
    |AKS 定价层|**免费**|
    |Kubernetes 版本|接受默认值|
    |自动升级|已禁用|
    |节点大小|**标准 B4ms**|
    |缩放方法|**手动**|
    |节点计数|**2**|

   > **注意：** 可能需要增加 vCPU 配额或更改 VM SKU 以适应节点大小和节点计数值。 有关增加 vCPU 配额的过程的信息，请参阅 Microsoft Learn 文章：[增加 VM 系列 vCPU 配额](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests)。

1. 在“**创建 Kubernetes 群集**”页的“**基本信息**”选项卡上，选择“**下一步：节点池 >** ”。
1. 在“**创建 Kubernetes 群集**”页的“**节点池**”选项卡上，选择“**下一步：访问 >** ”。
1. 在“**创建 Kubernetes 群集**”页的“**访问**”选项卡上，选择“**下一步：网络 >** ”。
1. 在“**创建 Kubernetes 群集**”页的“**网络**”选项卡上，选择“**Azure CNI**”选项，在“**虚拟网络**”下拉列表中选择“**vnet-01**”，然后在“**群集子网**”文本框下选择“**托管子网配置**”。
1. 在“**vnet-01 \| 子网**”页中，选择“ **+ 子网**”。
1. 在“**添加子网**”页中，指定以下设置，然后选择“**保存**”：

    |设置|值|
    |---|---|
    |名称|**aks-subnet**|
    |子网地址范围|**10.0.0.0/20**|

1. 返回“**vnet-01 \| 子网**”页，在页面左上角的痕迹导航路径中，选择“**创建 Kubernetes 群集**”。 
1. 返回到“**创建 Kubernetes 群集**”页的“**网络**”选项卡，指定以下设置：

    |设置|值|
    |---|---|
    |群集子网|**aks-subnet (10.0.0.0/20)**|
    |Kubernetes 服务地址范围|**172.16.0.0/22**|
    |Kubernetes DNS 服务 IP 地址|**172.16.3.254**|
    |DNS 名称前缀|**aks-01-dns**|
    |启用专用群集|已禁用|
    |设置授权 IP 范围|已禁用|
    |网络策略|**无**|

1. 在“**创建 Kubernetes 群集**”页的“**网络**”选项卡上，选择“**节点池**”选项卡。

   > **注意：** 你会将 Windows 节点池添加到群集。 这需要将网络配置从默认的 **Kubenet** 更改为 **Azure CNI**。 Kubenet 网络配置不支持 Windows 节点池。

1. 在“**创建 Kubernetes 群集**”页的“**节点池**”选项卡上，选择“ **+ 添加节点池**”。
1. 在“**添加节点池**”页上，指定以下设置：

    |设置|值|
    |---|---|
    |节点池名称|**w1pool**|
    |模型|**用户**|
    |OS 类型|**Windows**|
    |可用性区域|**无**|
    |启用 Azure 现成实例|已禁用|
    |节点大小|**标准 B4s_v2**|
    |缩放方法|**手动**|
    |节点计数|**2**|
    |每个节点的最大 Pod 数|**30**|
    |启用每个节点的公共 IP|已禁用|

   > **注意：** 可能需要增加 vCPU 配额或更改 VM SKU 以适应节点大小和节点计数值。 有关增加 vCPU 配额的过程的信息，请参阅 Microsoft Learn 文章：[增加 VM 系列 vCPU 配额](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests)。

1. 在“**添加节点池**”页上，选择“**添加**”。
1. 在“**创建 Kubernetes 群集**”页的“**节点池**”选项卡上，选择“**集成**”选项卡。
1. 在“**创建 Kubernetes 群集**”页的“**集成**”选项卡上的“**容器注册表**”下拉列表中，选择代表你在上一练习中创建的 Azure 容器注册表的条目，禁用“**启用建议的警报规则**”复选框，确保 **Azure Policy** 选项已禁用，然后选择“**查看 + 创建**”。
1. 在“**创建 Kubernetes 群集**”页的“**查看 + 创建**”选项卡上，选择“**创建**”。

   > **注意：** 继续下一个练习，无需等待 AKS 群集的预配完成。 预配过程可能需要 5 分钟。

## 练习 2：生成 Linux 和 Windows 容器映像并将其存储在 ACR 中
在本练习中，你将生成基于 Linux 和 Windows 的 Docker 映像，并将其推送到之前在本练习中创建的 Azure 容器注册表中。

### 任务 1：生成 Linux 容器映像并将其存储在 ACR 中
在此任务中，你将使用 ACR 任务生成 Linux 容器映像，并将其自动推送到 ACR 中。

1. 在 Azure 门户中，选择 Cloud Shell 图标。
1. 如果系统提示选择“Bash”或“PowerShell”，请选择“Bash”。 
1. 如果出现提示，请选择“**创建存储**”，然后等待，直到出现 Azure Cloud Shell 窗格。 
1. 确保 Cloud Shell 窗格左上角的下拉菜单中出现“**Bash**”。
1. 在 Cloud Shell 内的 Bash 会话中，创建一个目录来托管 Linux 映像的 Dockerfile，并通过运行以下命令，从当前目录更改为该目录：

   ```bash
   mkdir ~/image-l01
   cd ~/image-l01
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，使用内置编辑器在 image-l01 目录中创建一个名为 server.js 的文件，并将以下内容复制到该文件中：

   ```js
   const http = require('http')
   const port = 80
   const server = http.createServer((request, response) => {
     response.writeHead(200, {'Content-Type': 'text/plain'})
     response.write('Hello World from Node\n')
     response.end('Version: ' + process.env.NODE_VERSION + '\n')
   })
   server.listen(port)
   console.log(`Server running at http://localhost: ${port}`)
   ```

   > **注意：** 运行后，生成的 Node.js 代码将显示“**Hello World from Node**”消息。

1. 在 Azure Cloud Shell 的 Bash 会话中，再次使用内置编辑器在 image-l01 目录中创建一个名为 package.json 的文件，并将以下内容复制到该文件中：

   ```json
   {
     "name": "helloworld",
     "version": "1.0.0",
     "description": "Sample app for ACR Build",
     "main": "server.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "start": "node server.js"
     },
     "license": "MIT"
   }
   ```

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，再次使用内置编辑器在 image-l01 目录中创建一个名为 Dockerfile 的文件，并将以下内容复制到该文件中：

   ```Dockerfile
   FROM node:20.2-alpine
   COPY . /src
   RUN cd /src && npm install
   EXPOSE 80
   CMD ["node", "/src/server.js"]
   ```

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，标识之前在本练习中创建的 Azure 容器注册表的名称，然后通过运行以下命令，将其存储在名为 **$ACRNAME** 的变量中：

   ```azurecli
   ACR_RGNAME='acr-01-RG'
   ACR_NAME=$(az acr list --resource-group $ACR_RGNAME --query "[].name" --output tsv)
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，基于存储在当前目录中的 Dockerfile 创建一个 Docker 映像，然后通过运行以下命令（确保包含尾随句点），将其自动推送到其名称存储在 **$ACRNAME** 变量中的 Azure 容器注册表：

   ```azurecli
   az acr build --registry $ACR_NAME --image hellofromnode:v1.0 .
   ```

   > **注意：** 跟踪生成进度并确保它成功完成。 此过程应会在 1 分钟内完成。

### 任务 2：生成 Windows 容器映像并将其存储在 ACR 中
在此任务中，你将使用 ACR 任务生成 Windows 容器映像，并将其自动推送到 ACR 中。

1. 在 Cloud Shell 内的 Bash 会话中，创建一个目录来托管 Windows 映像的 Dockerfile，并通过运行以下命令，从当前目录更改为该目录：

   ```bash
   mkdir ~/image-w01
   cd ~/image-w01
   ```

1. 在 Azure Cloud Shell 的 Bash 会话中，克隆托管用于生成 Windows 映像的文件的公共 GitHub 存储库，并通过运行以下命令，将当前目录切换到克隆的存储库：

   ```git
   git clone https://github.com/Azure-Samples/dotnetcore-docs-hello-world.git
   cd ~/image-w01/dotnetcore-docs-hello-world
   ```

   > **注意：** 该存储库包含 .NET 7 Web 应用的源代码，该代码显示“**Hello World from .NET 7**”消息。

1. 在 Azure Cloud Shell 的 Bash 会话中，基于存储在当前目录中的 Dockerfile 创建一个 Docker 映像，然后通过运行以下命令（确保包含尾随句点），将其自动推送到其名称存储在 **$ACRNAME** 变量中的 Azure 容器注册表：

   ```azurecli
   az acr build --registry $ACR_NAME --image hellofromdotnet:v1.0 --platform windows --file Dockerfile.windows .
   ```

   > **注意：** 定义 Windows 映像生成的 Dockerfile 的名称为 **Dockerfile.windows**。

   > **注意：** 跟踪生成进度并确保它成功完成。 所需时间应该不超过 3 分钟。

1. 关闭 Azure Cloud Shell 窗格。
1. 在 Azure 门户中，导航到“**容器注册表**”页，然后选择表示你将两个映像推送到的容器注册表的条目。
1. 在“容器注册表”页上的垂直中心菜单中，选择“**存储库**”，然后验证 **hellofromnode** 和 **hellofromdotnet** 是否显示在存储库列表中。

## 练习 3：将容器映像部署到 AKS 
在本练习中，你要将在本练习前面创建的两个容器映像部署到 AKS 群集。

> **注意：** 在继续本练习之前，请确保 AKS 群集的预配已成功完成。

### 任务 1：创建自定义 AKS 命名空间
在此任务中，你将在本练习前面创建的 AKS 群集上创建两个命名空间。

1. 在 Azure 门户的“**搜索**”文本框中，搜索并选择“**Kubernetes 服务**”。
1. 在“**Kubernetes 服务**”页上，选择“**aks-01**”。
1. 在 **aks-01** 页的垂直中心菜单中选择“**命名空间**”。
1. 在“**aks-01 \| 命名空间**”页上，选择“ **+ 创建**”，然后在下拉菜单中选择“**命名空间**”。
1. 在“**创建命名空间**”窗格的“**名称**”文本框中，输入 **dev-node** 并选择“**创建**”。
1. 在“**aks-01 \| 命名空间**”页上，选择“ **+ 创建**”，然后在下拉菜单中选择“**命名空间**”。
1. 在“**创建命名空间**”窗格的“**名称**”文本框中，输入 **dev-dotnet** 并选择“**创建**”。

### 任务 2：创建用于部署 Linux 映像的 Kubernetes 清单
在此任务中，你将创建一个 Kubernetes 清单，用于将 Linux 映像部署到 Linux 节点池

1. 在 Azure 门户中，选择 Cloud Shell 图标。
1. 确保 Cloud Shell 窗格左上角的下拉菜单中出现“**Bash**”。
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

   > **注意：** 部署将基于 Linux 容器映像在 AKS 群集中的 Linux 节点池中创建 Pod。 此外，该清单还包含一个服务，该服务将通过端口 80 上的公共 IP 地址提供对部署中的 Pod 的负载均衡访问。

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，通过运行以下命令替换文件 aks-deployment-l01.yaml 中的 **ACR_NAME** 占位符：

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

   > **注意：** 该部署将基于 Windows 容器映像在 AKS 群集中的 Windows 节点池中创建 Pod。 此外，该清单还包含一个服务，该服务将通过端口 80 上的公共 IP 地址提供对部署中的 Pod 的负载均衡访问。

1. 保存对文件的更改并关闭它，回到 Bash 提示符。
1. 在 Azure Cloud Shell 的 Bash 会话中，通过运行以下命令替换文件 aks-deployment-l01.yaml 中的 **ACR_NAME** 占位符：

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

   > **注意：** 命令的输出应包括所有 AKS 节点（本例中为四个）的列表。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令，在 **dev-node** 命名空间的相应 YAML 清单文件中创建定义的第一个部署：

   ```kubectl
   cd ~/aks-l01
   kubectl apply -f aks-deployment-l01.yaml -n=dev-node
   ```

   > **注意：** 继续执行下一步，无需等待部署完成。 预配所有资源可能需要几分钟时间。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令，创建在相应的 YAML 清单文件中定义的第二个部署：

   ```kubectl
   cd ~/aks-w01
   kubectl apply -f aks-deployment-w01.yaml -n=dev-dotnet
   ```

   > **注意：** 继续执行下一步，无需等待部署完成。 预配所有资源可能需要几分钟时间。

# 练习 4：查看部署并取消预配所有资源
在本练习中，你将查看部署的结果并取消预配所有资源。

### 任务 1：查看 AKS 部署和服务
在此任务中，你将查看这两个部署的结果，包括部署和服务对象。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令显示这两个部署的状态：

   ```kubectl
   kubectl get deployments -n=dev-node
   kubectl get deployments -n=dev-dotnet
   ```

   > **注意：** 在继续下一步之前，请验证两个部署是否都以就绪状态列出。 如果不是，请再等待一分钟，重新运行上述两个命令，然后再次检查部署状态。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令显示清单文件中包含的两个服务的状态：

   ```kubectl
   kubectl get services -n=dev-node
   kubectl get services -n=dev-dotnet
   ```

1. 验证列出的每个服务是否在 **EXTERNAL-IP** 列中包含值。 
1. 使用 Web 浏览器导航到你在上一步中确定的 IP 地址，然后验证生成的网页是否分别显示 **Hello World from Node** 和 **Hello World from .Net 7** 消息。

### 任务 2：删除所有资源
在此任务中，你将删除在本练习中预配的所有资源。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令显示本练习中预配的两个资源组中的资源列表：

   ```azurecli
   az resource list --resource-group 'acr-01-RG' --query "[].name" --output tsv
   az resource list --resource-group 'aks-01-RG' --query "[].name" --output tsv
   ```

   > **注意：** 验证这些是否是你想要删除的资源。 如果是，继续下一步。

1. 在 Azure Cloud Shell 的 Bash 会话中，运行以下命令删除本练习中预配的所有资源：

   ```azurecli
   az group delete --name 'acr-01-RG' --no-wait --yes
   az group delete --name 'aks-01-RG' --no-wait --yes
   ```

   > **注意：** 该命令会以异步方式执行（由 --nowait 参数强制执行），因此，即使两个命令都会立即返回到 Bash shell 提示符，也要花费几分钟时间才能删除资源组及其资源。

1. 关闭 Azure Cloud Shell 窗格。
