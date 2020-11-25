---
title: 在命名空间中启用Istio
description: description
keywords:
  - rancher 2.0中文文档
  - rancher 2.x 中文文档
  - rancher中文
  - rancher 2.0中文
  - rancher2
  - rancher教程
  - rancher中国
  - rancher 2.0
  - rancher2.0 中文教程
  - subtitles1
  - subtitles2
  - subtitles3
  - subtitles4
  - subtitles5
  - subtitles6
---

您需要在每个您希望被 Istio 跟踪或控制的命名空间中手动启用 Istio。当在命名空间中启用 Istio 时，Envoy sidecar 代理将被自动注入到命名空间中部署的所有新工作负载中。

此命名空间设置将只影响命名空间中的新工作负载。任何先前存在的工作负载将需要重新部署，以利用 sidecar 自动注入。

**前提条件：**要在命名空间中启用 Istio，集群必须安装 Istio。

1. 在 Rancher **集群资源管理器中**，打开 kubectl shell。
1. 然后运行`kubectl label namespace <namespace> istio-injection=enabled`。

**结果:** 命名空间现在有`istio-injection=enabled`标签。在该命名空间部署的所有新工作负载将默认注入 Istio sidecar。

### 验证自动 Istio Sidecar 喷射已启用

要验证 Istio 是否已启用，请在命名空间中部署 hello-world 工作负载。进入工作负载并点击 pod 名称。在**容器**部分，你应该看到`istio-proxy`容器。

### 排除工作负载被 Istio Sidecar 注入的可能性

如果您需要将工作负载排除在 Istio sidecar 的注入之外，请在工作负载上使用以下注解。

```yaml
sidecar.istio.io/inject: “false”
```

将注解添加到工作负载中。

1. 从**集群资源管理器**视图中，使用侧边导航选择工作负载的**视图**页。
1. 转到不应该有 sidecar 的工作负载，并编辑为 yaml。
1. 在工作负载上添加以下键和值`sidecar.istio.io/inject: false`作为注解。
1. 点击**Save**。

**结果：** Istio sidecar 将不会被注入到工作负载中。

> **注意：** 如果您部署的 Job 存在未完成的问题，您需要使用提供的步骤将此注释添加到您的 pod 中。由于 Istio Sidecars 无限期地运行，即使任务完成后，也不能认为作业已经完成。

### 后续步骤

[选择节点]({{<baseurl>}}/rancher/v2.x/en/cluster-admin/tools/istio/setup/node-selectors)