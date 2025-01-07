# 将你的 TypeScript 客户端打包成 Python 后端

> 原文：[https://towardsdatascience.com/packaging-your-typescript-client-into-a-python-backend-b087e50c5c1a?source=collection_archive---------6-----------------------#2024-04-05](https://towardsdatascience.com/packaging-your-typescript-client-into-a-python-backend-b087e50c5c1a?source=collection_archive---------6-----------------------#2024-04-05)

![](../Images/b458042bef9c23c961a0c8f73536f6cc.png)

图片由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 完整的实操指南

## 将你的 React 应用与 FastAPI Web 服务器结合

[](https://itay-bittan.medium.com/?source=post_page---byline--b087e50c5c1a--------------------------------)[![Itay Bittan](../Images/328e4d9b0b98b65e59e42983e9d1afaa.png)](https://itay-bittan.medium.com/?source=post_page---byline--b087e50c5c1a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b087e50c5c1a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b087e50c5c1a--------------------------------) [Itay Bittan](https://itay-bittan.medium.com/?source=post_page---byline--b087e50c5c1a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b087e50c5c1a--------------------------------) ·阅读时间 6 分钟·2024年4月5日

--

在本指南中，你将学习如何将一个简单的 TypeScript [React 应用程序](https://react.dev/) 打包成一个 **Python 包**，并通过你的 [FastAPI](https://fastapi.tiangolo.com/) Python Web 服务器提供服务。如果你想查看完整的代码，请查看 [客户端](https://github.com/itayB/vite-project) 和 [服务器](https://github.com/itayB/backend) 的代码库。让我们开始吧！

在开发过程中，你可能会使用两个不同的集成开发环境（IDE）：

1.  TypeScript 或 JavaScript React 应用窗口，运行在专用监听端口（例如：5173）上，用于提供客户端/前端页面。

1.  Python FastAPI，运行在另一个端口（例如：8080）上，用于提供 REST API。

换句话说，你有两个不同的服务器在本地运行。每当你想调用 FastAPI 服务器时，浏览器需要与两个不同的服务器进行交互。

![](../Images/ddea8edae4c7663617a54bd567a9a9d5.png)

本地开发（图示来自作者）

虽然在本地（`localhost`）运行时一切正常，但当你将代码部署到生产环境时，浏览器会遇到“跨源请求被阻止”的错误。在将代码部署到生产环境之前，最佳实践是将客户端页面和 REST API 都从同一个后端 Web 服务器提供服务。这样浏览器将只与一个后端交互，这对安全性、性能和简洁性更有益。

![](../Images/9b7ad97acecff8862df24587b69f1b25.png)

准备上线（图示来自作者）

## 1\. 创建一个简单的 React 应用

首先，在你的 `workspace` 目录中，使用 [vite](https://vitejs.dev/) 创建一个新的 TypeScript React 应用程序：

```py
~/workspace ➜ npm create vite@latest
✔ Project name: … vite-project
✔ Select a framework: › React
✔ Select a variant: › TypeScript
```

然后，进入新的项目目录，安装依赖并运行应用程序（[http://localhost:5173](http://localhost:5174/)）：

```py
~/workspace ➜ cd vite-project
~/workspace/vite-project ➜ npm install
~/workspace/vite-project ➜ npm run dev
```

你应该会看到类似的内容：

![](../Images/a3d3ba1a5cafb7f5ac88a61678ae6210.png)

第一个 Vite React 模板（图片来自作者）

现在，让我们对模板做一个小修改——我们将添加一个异步 HTTP 调用来获取未来 FastAPI 后端的状态：

```py
function App() {
  ...
  const [health, setHealth] = useState('');

  useEffect(() => {
    const getStatus = async () => {
      const response = await fetch('/v1/health-check/liveness', {
        method: 'GET',
      });
      let status: { [status: string]: string } = {};
      try {
        status = await response.json();
      } catch (err) {
        console.log(`failed to get backend status. ${err}`);
      }
      setHealth(status['status'] || 'unknown');
    };
    getStatus();
  }, []);

  return (
  ...
     <div>Backend Status: {health}</div>
  ...
  )
}
```

现在我们应该能看到类似这样的结果：

![](../Images/fbc82ab99eee369cf0e6acce971c153d.png)

使用后端调用（图片来自作者）

此时，**后端状态**是 `unknown`，因为我们尚未实现它。别担心，我们很快就会处理它。最后，让我们构建客户端，方便稍后进行打包：

```py
~/workspace/vite-project ➜ npm run build
```

打包输出应创建一个 `dist` 文件夹，其中包含最终优化后的代码，如下所示：

```py
└── dist/
    ├── assets/
    ├── static/
    └── index.html
```

## 2\. 构建 Python 包

此时，我们切换到 Python 环境。我更喜欢在 [虚拟环境](https://docs.python.org/3/library/venv.html) 中工作以实现隔离。在一个专用的虚拟环境中，我们将安装 `twine` 和 `build` 来创建我们的 Python 包：

```py
~/workspace/vite-project ➜ python3 -m venv venv
~/workspace/vite-project ➜ . venv/bin/activate
~/workspace/vite-project (venv) ➜ python -m pip install --upgrade pip
~/workspace/vite-project (venv) ➜ pip install twine==5.0.0 build==1.2.1
```

在根文件夹（`vite-project`）中创建一个新的 `setup.py` 文件，内容如下：

```py
from setuptools import setup
from pathlib import Path

cwd = Path(__file__).parent
long_description = (cwd / "README.md").read_text()

setup(
    name="vite-project",
    version="0.0.1",
    package_dir={"vite_project": "dist"},
    package_data={"vite_project": ["**/*.*"]},
    long_description=long_description,
    long_description_content_type="text/markdown",
)
```

然后运行以下命令来创建包：

```py
~/workspace/vite-project (venv) ➜ python setup.py sdist -d tmp
~/workspace/vite-project (venv) ➜ python -m build --wheel --outdir tmp
~/workspace/vite-project (venv) ➜ twine upload -u ${USERNAME} -p ${PASSWORD} --repository-url ${REPO_URL} tmp/*
```

上面的最后一行是可选的，如果你打算将你的包上传到远程仓库，例如 [PyPI](https://pypi.org/)、[JFrog Artifactory](https://jfrog.com/artifactory/) 等。

## 3\. 创建一个 FastAPI Python Web 服务器

最后一步是构建 Python 服务器并使用客户端包。为此，我们将：

+   创建一个新的 `backend` 目录。

+   创建一个新的虚拟环境。

+   安装相关的包和我们的客户端包：

```py
~/workspace/backend ➜ python3 -m venv venv
~/workspace/backend ➜ . venv/bin/activate
~/workspace/backend (venv) ➜ python -m pip install --upgrade pip
~/workspace/backend (venv) ➜ pip install fastapi==0.110.0 uvicorn==0.29.0
~/workspace/backend (venv) ➜ pip install ~/workspace/vite-project/tmp/vite-project-0.0.1.tar.gz
```

请注意，我们从之前创建的本地路径安装了我们的客户端包。如果你将包上传到远程仓库，你可以使用以下命令安装：

```py
~/workspace/backend (venv) ➜ pip install --extra-index-url https://${USERNAME}:${PASSWORD}@${REPO_URL} vite-project==0.0.1
```

接下来，让我们创建一个简单的 Python 服务器（2 个文件）：

## **__main__.py**

```py
from distutils.sysconfig import get_python_lib
from fastapi import FastAPI
from fastapi.responses import FileResponse
from fastapi.staticfiles import StaticFiles
from backend.health_router import router
from uvicorn import run

def create_app():
    app = FastAPI(
        title="Backend Server",
    )
    app.include_router(router)

    client_path = f"{get_python_lib()}/vite_project"
    app.mount("/assets", StaticFiles(directory=f"{client_path}/assets"), name="assets")
    app.mount("/static", StaticFiles(directory=f"{client_path}/static"), name="static")

    @app.get("/{catchall:path}")
    async def serve_react_app(catchall: str):
        return FileResponse(f"{client_path}/index.html")

    return app

def main():
    app = create_app()
    run(app, host="0.0.0.0", port=8080)

if __name__ == "__main__":
    main()
```

## health_router.py

```py
from typing import Literal
from typing_extensions import TypedDict
from fastapi import APIRouter, status

STATUS = Literal["success", "error", "partial", "unknown"]

class ReturnHealthcheckStruct(TypedDict):
    status: STATUS

router = APIRouter(
    prefix="/v1/health-check",
    tags=["Health Check"],
)

@router.get(
    "/liveness",
    summary="Perform a Liveness Health Check",
    response_description="Return HTTP Status Code 200 (OK)",
    status_code=status.HTTP_200_OK,
    response_model=ReturnHealthcheckStruct,
)
async def liveness() -> ReturnHealthcheckStruct:
    return {"status": "success"}
```

在上面的实现中，我们通过挂载 `static` 和 `assets` 文件夹以及任何其他客户端文件来支持从客户端应用程序提供静态文件，并由我们的 Python 服务器进行服务。

我们还创建了一个简单的 GET 端点 `v1/health-check/liveness`，它返回一个简单的 `{“status": “success"}` JSON 响应。这样我们就能确保服务器同时处理客户端静态文件和服务器端 RESTful API。

现在，如果我们访问 [localhost:8080](http://localhost:8080)，我们可以看到我们的客户端正在运行。注意下面的 **后端状态**，现在它是 `success`（而不是 `unknown`）。

![](../Images/dcee5308f6c616195bfc292a2ea1e253.png)

同时运行 Python 服务器与 React 应用程序（图片来自作者）

# 总结

在本教程中，我们创建了一个简单的React应用程序，该应用程序向后端发出单个请求。我们将这个客户端应用程序封装为一个Python包，并通过我们的FastAPI Python Web服务器提供服务。

采用这种方法可以让你在两个领域中利用最好的工具：前端使用TypeScript和React，后端使用Python和FastAPI。然而，我们希望保持这两个组件之间的高度内聚和低耦合。这样，你将获得所有的好处：

+   效率，通过将前端和后端分离到不同的代码库，每个部分可以由不同的团队进行开发。

+   稳定性和质量，通过锁定版本化的客户端包，并仅在服务器准备好支持新客户端版本时才进行更新。

+   安全性——浏览器仅与一个后端服务器进行交互。我们无需启用CORS或任何其他可能妥协安全性的解决方法。

+   简单性——通过使用单一服务器进行工作
