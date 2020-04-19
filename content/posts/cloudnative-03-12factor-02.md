---
title: "云原生（03）之十二元素应用（02）"
date: 2020-04-19T19:13:18+08:00
draft: false
---
今天通过健康打卡程序来讲解一下十二要素。健康打卡程序是通过Node.js来开发，并自动部署在Kubernetes平台上。

1. 一份基准代码，多份部署。通过版本控制软件在开发环境、预发布环境和生产环境部署的是同一份代码。

健康打卡程序是通过本地化部署的GitLab来做版本控制的，并且保持几个环境的状态都是一致的。

2. 明确地声明所有依赖的类库和工具。

Node.js开发的应用主要是依赖Node.js，每个系统上安装的Node.js运行均一致。而且Node.js工程是通过package.json来声明依赖。

```json
{
  "name": "checkin2",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "test": "mocha",
    "start": "node ./bin/www"
  },
  "dependencies": {
    "axios": "^0.19.2",
    "cas-authentication": "0.0.8",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.9",
    "express": "~4.16.0",
    "express-session": "^1.17.0",
    "express-validator": "^6.3.1",
    "http-errors": "~1.6.2",
    "i18n": "^0.8.5",
    "moment": "^2.24.0",
    "morgan": "~1.9.0",
    "oracledb": "^4.2.0",
    "pug": "^2.0.4",
    "qrcode": "^1.4.4",
    "querystring": "^0.2.0",
    "tablefilter": "^0.6.107"
  },
  "devDependencies": {
    "chai": "^4.2.0"
  }
}
```

3. 在环境中存储外部配置，严禁将外部配置硬编码在程序代码中。

在健康打卡程序中，我们把相关配置放到一个config.js文件中。在该文件里读取环境变量，如果环境变量中不存在定义，则使用默认值。

```javascript
module.exports = {
  user          : process.env.NODE_ORACLEDB_USER,

  // Get the password from the environment variable
  // NODE_ORACLEDB_PASSWORD.  The password could also be a hard coded
  // string (not recommended), or it could be prompted for.
  // Alternatively use External Authentication so that no password is
  // needed.
  password      : process.env.NODE_ORACLEDB_PASSWORD,

  // For information on connection strings see:
  // https://oracle.github.io/node-oracledb/doc/api.html#connectionstrings
  connectString : process.env.NODE_ORACLEDB_CONNECTIONSTRING,

  // Setting externalAuth is optional.  It defaults to false.  See:
  // https://oracle.github.io/node-oracledb/doc/api.html#extauth
  externalAuth  : process.env.NODE_ORACLEDB_EXTERNALAUTH ? true : false,
  poolMin: 10,
  poolMax: 10,
  poolIncrement: 0
};
```

而在Kubernetes上部署时，也是在环境变量中声明与此相关的参数。

```yaml
envFrom:
  - secretRef:
    name: yqsbenv
```

```yaml
# k -n yqsb get secret yqsbenv -o yaml
apiVersion: v1
data:
  NODE_ORACLEDB_CONNECTIONSTRING: xxxxxxx
  NODE_ORACLEDB_PASSWORD: xxxxxxx
  NODE_ORACLEDB_USER: xxxxxx
  WECHAT_AESKEY: xxxxxxx
  WECHAT_AGENTID: xxxxxx
  WECHAT_APPID: xxxxxx
  WECHAT_SECRET: xxxxxx
  WECHAT_TOKEN: xxxxxx
kind: Secret
type: Opaque
```

4. 把后端服务当做附加资源，降低耦合性

本程序使用Oracle作为后端数据库，通过node-oradb模块来连接。

5. 严格分离构建、发布和运行阶段，不允许在生产环境中修改代码

本程序通过自动CI/CD机制，在GitLab Runner中构建Docker镜像，并在Kubernetes中部署镜像，不存在在生产环境中修改代码的问题。

6. 以一个或多个无状态进程运行应用

本程序的持久化数据保存在Oracle数据库中，运行多个Pod均无需做出改动，所以在部署文件中，有5个副本。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: yqsb
    version: "20200201"
  name: yqsb
  namespace: yqsb
spec:
  minReadySeconds: 10
  replicas: 5
```

7. 不依赖任何网络服务器创建网络服务，直接通过绑定端口提供网络服务

本程序不依赖Apache或者Tomcat等中间件，完全由Node.js中的http模块来创建侦听在3000端口上的程序。

```javascript
var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTP server.
 *
var server = http.createServer(app);

/**
 * Listen on provided port, on all network interfaces.
 */
server.listen(port);
```

8. 进程管理应该借助操作系统的进程管理器，不用单独制定守护进程或者写入PID等文件

本程序不需要写PID，也不需要守护进程，在制作Docker镜像时，让进程随镜像启动而运行。

```Dockerfile
EXPOSE 3000
CMD ["npm", "start"]
```

9. 进程可以快速启动，也可以优雅终止，提高健壮性

本程序启动比较快，在启动过程中，仅有一项额外操作：初始化Oracle数据库连接池。

本程序也处理了优雅终止问题：

```javascript
process.on('SIGTERM', () => {
  console.log('Received SIGTERM');

  shutdown();
  process.exit(1);
});

process.on('SIGINT', () => {
  console.log('Received SIGINT');

  shutdown();
  process.exit(1)
});
```

10. 尽可能的保持开发，预发布，生产环境都一致

本程序开发通过Visual Studio Code，每个环境都安装Node.js即可。而发布环境的Docker镜像使用Node.js镜像作为源镜像，从而保证了各个环境的一致。

11. 把日志当作事件流，不要做特殊化处理。

本程序使用了Console.log和Console.debug来处理日志。在实际中，也可以使用log4js等模块处理。

12. 后台管理任务和常驻进程使用相同的环境。

本程序不存在后台管理任务，所以也就不适用于本条规则。

以上就是十二要素在健康打开程序中的应用方法。希望对大家有所帮助。
