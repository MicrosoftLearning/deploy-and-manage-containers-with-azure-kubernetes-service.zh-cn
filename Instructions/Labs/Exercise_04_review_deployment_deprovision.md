---
Exercise:
  title: 练习：查看部署并取消预配所有资源
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# 练习 4 - 将应用程序部署到 Azure Kubernetes 服务 (AKS)

## 目标

此引导项目包括以下练习：

+ 练习 1：预配 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS)
+ 练习 2：生成 Linux 和 Windows 容器映像并将其存储在 ACR 中
+ 练习 3：将容器映像部署到 AKS
+ **练习 4：查看部署并取消预配所有资源**

在本练习中，你将查看上一练习中服务的部署并取消预配所有资源。

# 练习 4：查看部署并取消预配所有资源
在本练习中，你将查看部署的结果并取消预配所有资源。

>**注意**：为了完成本练习，你将需要一个 [Azure 订阅](https://azure.microsoft.com/free/)。
> 对于未指定的任何属性，请使用默认值。

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

1. 验证列出的每个服务是否在 EXTERNAL-IP 列中包含值。 
1. 使用 Web 浏览器导航到你在上一步中确定的 IP 地址，然后验证生成的网页是否分别显示 **Hello World from Node** 和 **Hello World from .Net 7** 消息。

### 任务 2：删除所有资源
在此任务中，你将删除在本实验室中预配的所有资源。

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
