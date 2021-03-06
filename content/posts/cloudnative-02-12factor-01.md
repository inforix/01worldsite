---
title: "云原生（02）之十二元素应用（01）"
date: 2020-04-17T13:24:59+08:00
draft: false
---

今天来介绍一下非常经典的十二要素应用（The Twelve-Factor App）。它的官网为：https://12factor.net/ 。这十二要素如同微服务届的“十戒”，个人认为Martin Fowler讲解了微服务的理念，而十二要素则是法则。

根据官网描述：

> 12-Factor为构建如下的 SaaS 应用提供了方法论：<br /><br>
> - 使用**标准化**流程自动配置，从而使新的开发者花费最少的学习成本加入这个项目。
> - 和操作系统之间尽可能的**划清界限**，在各个系统中提供**最大的可移植性**。
> - 适合**部署**在现代的**云计算平台**，从而在服务器和系统管理方面节省资源。
> - 将开发环境和生产环境的**差异降至最低**，并使用**持续交付**实施敏捷开发。
> - 可以在工具、架构和开发流程不发生明显变化的前提下实现**扩展。**
> <br><br>这套理论适用于任意语言和后端服务（数据库、消息队列、缓存等）开发的应用程序。

12-Factor App综合了他们关于SaaS应用几乎所有的经验和智慧，是开发此类应用的理想实践标准，并特别关注与应用程序如何保持良性成长，开发者之间如何进行有效的代码协作，以及避免软件污染。这一段话概括了它的目标和作用，非常经典。在我遵从十二要素开发云原生应用后，我逐渐发现它的妙处，惊叹他们的智慧！

十二要素包含哪些呢？请看下面的内容：

1. 一份基准代码，多份部署。通过版本控制软件在开发环境、预发布环境和生产环境部署的是同一份代码。

2. 明确地声明所有依赖的类库和工具。

3. 在环境中存储外部配置，严禁将外部配置硬编码在程序代码中。

4. 把后端服务当做附加资源，降低耦合性

5. 严格分离构建、发布和运行阶段，不允许在生产环境中修改代码

6. 以一个或多个无状态进程运行应用

7. 不依赖任何网络服务器创建网络服务，直接通过绑定端口提供网络服务

8. 进程管理应该借助操作系统的进程管理器，不用单独制定守护进程或者写入PID等文件

9. 进程可以快速启动，也可以优雅终止，提高健壮性

10. 尽可能的保持开发，预发布，生产环境都一致

11. 把日志当作事件流，不要做特殊化处理。

12. 后台管理任务和常驻进程使用相同的环境。

下篇会结合我们的健康打卡系统来详细描述这些规则。