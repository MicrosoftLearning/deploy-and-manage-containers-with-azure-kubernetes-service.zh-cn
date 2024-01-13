---
Exercise:
  title: 练习：生成 Linux 和 Windows 容器映像并将其存储在 Azure 容器注册表
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# 练习 2 - 将应用程序部署到 Azure Kubernetes 服务 (AKS)

## 目标

此引导项目包括以下练习：

+ 练习 1：预配 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS)。
+ **练习 2：生成 Linux 和 Windows 容器映像并将其存储在 Azure 容器注册表。**
+ 练习 3：将容器映像部署到 Azure Kubernetes 服务。
+ 练习 4：查看部署并取消预配所有资源。

在本练习中，你将生成 Linux 和 Windows 容器映像并将其存储在 Azure 容器注册表。

## 练习 2：生成 Linux 和 Windows 容器映像并将其存储在 ACR 中
在本练习中，你将生成基于 Linux 和 Windows 的 Docker 映像，并将其推送到之前在本实验室中创建的 Azure 容器注册表中。


>注意：为了完成本练习，你将需要一个 [Azure 订阅](https://azure.microsoft.com/free/)****。
> 对于未指定的任何属性，请使用默认值。


### 任务 1：生成 Linux 容器映像并将其存储在 ACR 中
在此任务中，你将使用 ACR 任务生成 Linux 容器映像，并将其自动推送到 ACR 中。

1. 在 Azure 门户中，选择 Cloud Shell 图标。
1. 如果系统提示选择“Bash”或“PowerShell”，请选择“Bash”。 
1. 如果出现提示，请选择“**创建存储**”，然后等待，直到出现 Azure Cloud Shell 窗格。 
1. 确保 Cloud Shell 窗格左上角的下拉菜单中出现“Bash”****。
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

   > **注意：** 跟踪生成进度并确保它成功完成。 此过程应该会在 1 分钟内完成。

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
1. 在“容器注册表”页上的垂直中心菜单中，选择“存储库”，并验证 hellofromnode 和 hellofromdotnet 是否显示在存储库列表中。************
