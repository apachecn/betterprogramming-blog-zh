# 用 Docker 设置 Flask 和 Next.js 应用程序

> 原文：<https://betterprogramming.pub/setup-flask-nextjs-application-with-docker-97e82a897573>

![](img/ec98b4fc259758974dd1ff5595ff7477.png)

照片由 [Jesus Kiteque](https://unsplash.com/@jesuskiteque?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近，作为一名兼职人员，我一直致力于推出 passporr.com。Passporr.com 是一个平台，允许国际学生搜索和找到任何与他们的学习相关的东西。它可以通过向国际学生提供免费工具和社区问答知识库来帮助他们。

我用 Flask (Python web framework)、Next.js (React framework)搭建平台，把所有东西都包在 Docker 里。

在构建 passporr 之前，我找不到一个好的教程来教我如何使用 Docker 服务 Flask 和 React.js 应用程序。所以我决定写一个。

在这篇文章中，我将分享我如何使用 Docker 和`docker-compose`建立我的本地开发。我还会分享我是如何用`docker-machine`直接部署到 DigitalOcean 的。

这篇文章的重点是我如何设置代码库来与 Docker 和`docker-compose`一起工作。在以后的文章中，我将更深入地讨论 Flask 和 Next.js。

# 那么我们要建造什么呢？

![](img/1d26b3d2e9216669cc0d83f6ee9b9b9c.png)

示例应用程序的基本功能

我将在这里展示的应用程序包括:

烧瓶应用程序(后端 API)

*   用于身份验证的端点
*   获取、发布、放置用户的端点

Next.js 应用程序(前端)

*   匿名用户可访问的路线(主页、组件页、登录页)
*   安全路线(个人资料页面)

# 将申请归档

如果你去 GitHub 并克隆 [repo](https://github.com/martindavid/flask-react-docker-app) ，你会看到代码库由三个主要文件夹组成:`api`、`client`和`nginx`。

在每个文件夹中，您会发现一个 docker 文件，它为每个服务构建了容器。您还会看到一个名为`Dockerfile-prod`的文件。`Dockerfile-prod`是一个 Docker 文件，我们将使用它来部署到生产环境中。当我们讨论部署时，我们将回到该文件。

# 烧瓶应用图像

```
*# Base Image*
FROM python:3.7.2-slim

*# Install netcat*
RUN apt-get update && \
    apt-get -y install netcat && \
    apt-get clean

*# set working directory*
WORKDIR /usr/src/app

*# add and install requirements*
COPY ./requirements.txt /usr/src/app/requirements.txt
RUN pip install -r requirements.txt

*# add app*
COPY . /usr/src/app

*# run server*
CMD python manage.py run -h 0.0.0.0
```

对于开发映像，我使用`python:3.7.2-slim`作为基础映像，并使用 Flask 内置的 web 服务器运行应用程序。如果你查看`api`文件夹中的另一个文件，你会发现我使用`gunicorn`服务 Flask 应用程序的`Dockerfile-prod`文件。

除了 Flask 应用程序映像，在`api/project`文件夹中，您会发现一个名为`db`的文件夹，其中包含一个用于创建数据库的 SQL 文件和一个用于 Postgres 的 docker 文件。

```
FROM postgres:11.1-alpine

ADD create.sql /docker-entrypoint-initdb.d
```

# Next.js 应用程序图像

Next.js 应用程序的 Dockerfile

```
FROM node:10.16.0-alpine

WORKDIR usr/src/app
ENV PATH /usr/src/app/node_modules/.bin:$PATH

*# install and cache app dependencies*
COPY package.json /usr/src/app/package.json

RUN npm install

EXPOSE 3000
CMD ["npm", "run", "dev"]
```

Next.js 应用程序的图像非常简单。我使用了`node:10.16.0-alpine`作为基本映像，并运行了`dev`脚本来运行热重装。

# Nginx 图像

为了连接 Flask API 和 Next.js 应用程序，我使用 Nginx。这一部分展示了我如何为 Nginx 设置配置。

```
server {
  listen 8080;

  location / {
    proxy_pass        http://client:3000;
    proxy_redirect    default;
    proxy_set_header  Upgrade $http_upgrade;
    proxy_set_header  Connection "upgrade";
    proxy_set_header  Host $host;
    proxy_set_header  X-Real-IP $remote_addr;
    proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Host $server_name;
    proxy_set_header  X-Forwarded-Proto $scheme;
  }

  location /api {
    proxy_pass        http://api:5000;
    proxy_redirect    default;
    proxy_set_header  Host $host;
    proxy_set_header  Upgrade $http_upgrade;
    proxy_set_header  Connection "upgrade";
    proxy_set_header  X-Real-IP $remote_addr;
    proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Host $server_name;
    proxy_set_header  X-Forwarded-Proto $scheme;
  }
}
```

从上面的 Nginx 配置中，我们可以看到对`/api`的调用被重新路由到 Flask 应用程序，它在端口`5000`上。请求的其余部分被路由到 Next.js 应用程序。我使用端口`8080`作为 Nginx 监听的默认端口，以避免与机器上的其他端口冲突。

除了上面的配置，下面是 Nginx 的 docker 文件，非常简单。

```
FROM nginx:1.15.8-alpine

RUN rm /etc/nginx/conf.d/default.conf
COPY /dev.conf /etc/nginx/conf.d
```

最后，为了一次运行所有的服务，我使用`docker-compose`来编排所有的服务。

```
version: '3.7'

services:
  api:
    build:
      context: ./api
      dockerfile: Dockerfile
    volumes:
      - './api:/usr/src/app'
    ports:
      - 5002:5000
    environment:
      - FLASK_CONFIG=development
      - FLASK_ENV=development
      - APP_SETTINGS=project.config.DevelopmentConfig
      - DATABASE_URL=postgres://postgres:postgres@tutorial-db:5432/dev_db
      - DATABASE_TEST_URL=postgres://postgres:postgres@tutorial-db:5432/test_db
      - SECRET_KEY=ZQbn05PDeA7v11
    depends_on:
      - tutorial-db

  tutorial-db:
    build:
      context: ./api/project/db
      dockerfile: Dockerfile
    ports:
      - 5436:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres

  nginx:
    build:
      context: ./nginx
      dockerfile: Dockerfile
    restart: unless-stopped
    ports:
      - 8080:8080
    depends_on:
      - api
      - client

  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    volumes:
      - './client:/usr/src/app'
      - '/usr/src/app/node_modules'
    ports:
      - 3008:3000
    environment:
      - NODE_ENV=development
      - REACT_APP_SERVICE_URL=http://localhost:8080
      - CHOKIDAR_USEPOLLING=true
    depends_on:
      - api
```

在上面的`docker-compose.yml`文件中，我们将运行四个服务(`api`、`tutorial-db`、`nginx`和`client`)。

您可以从 [http://localhost:8080](http://localhost:8080/) 打开主应用程序，或者从 [http://localhost:5002](http://localhost:5002/) 单独访问 Flask 应用程序，或者从 [http://localhost:3008](http://localhost:3008/) 访问 Next.js 应用程序。您也可以从端口`5436`访问 Postgres 数据库。

在您设置好一切之后，您可以通过运行`docker-compose up -d --build`来运行整个配置。

# 将应用程序部署到数字海洋

使用`docker-machine`，您可以轻松地将您的应用程序直接部署到云提供商，如 DigitalOcean 或 AWS。在这篇文章中，我将展示如何将它部署到数字海洋。(有关部署到 AWS 的更多信息，请点击此处的。)在执行以下步骤之前，请确保您已经:

1.  数字海洋账户。如果需要，使用这个[链接](https://m.do.co/c/8756bd95a305)创建一个。如果你是学生，你也可以利用 [GitHub 教育包](https://education.github.com/pack#offers)在 DigitalOcean 上获得 50 美元的平台积分。
2.  数字海洋的个人访问令牌

# 创建新的 Docker-Engine 实例

首先要做的是在 DigitalOcean 上创建一个 Docker-machine 实例。

```
docker-machine create --driver digitalocean --digitalocean-access-token <your_personal_access_token> <name-for-your-docker-machine>
```

成功创建后，您可以使用`docker-machine ls`进行检查。

```
NAME                     ACTIVE   DRIVER         STATE     URL                              SWARM   DOCKER     ERRORS
<docker-machine-name>    -        digitalocean   Running   tcp://<docker-machine-ip>:2376           v19.03.1
```

# 部署应用程序

以下命令将把您连接到 DigitalOcean 中的实例，您可以使用`docker-compose`部署应用程序:

```
# Activate the docker-machine. Replace <docker-machine-name> with the actual docker-machine name from the previous step.
$ docker-machine env <docker-machine-name># Activate shell configuration
$ eval $(docker-machine env <docker-machine-name>)# Run docker-compose
$ eval $(docker-machine env <docker-machine-name>)# Check if all containers run successfully
$ docker ps
```

确保有三个容器在那里运行。您也可以从`http://<docker-machine-ip>`访问应用程序。

# 摘要

从开发之初就使用 Docker，用它来推送到生产，帮助我快速开发应用。

我也更有信心，因为我的应用程序在开发和生产中都有相同的环境设置。

我在这里展示的从本地机器部署的步骤对于团队环境或更健壮的应用程序可能不太理想。在这些情况下，您可能需要尝试使用 CI/CD 设置的选项。