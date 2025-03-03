---
title: Chart 安装选项
description: Chart 安装选项
keywords:
  - rancher
  - rancher中文
  - rancher中文文档
  - rancher官网
  - rancher文档
  - Rancher
  - rancher 中文
  - rancher 中文文档
  - rancher cn
  - 安装指南
  - 高可用安装指南
  - Chart 安装选项
---

这个页面描述了 Rancher Helm Chart 的配置参考。

选择要安装的 Rancher 版本，请参阅[选择 Rancher 版本](/docs/rancher2.5/installation/resources/choosing-version/_index)。

有关启用实验性功能的信息，请参阅[这个页面](/docs/rancher2.5/installation/resources/feature-flags/_index)。

## 通用选项

| 选项                      | 默认值       | 描述                                                                    |
| :------------------------ | :----------- | :---------------------------------------------------------------------- |
| `hostname`                | " "          | `string` - 您的 Rancher Server 的 FQDN                                  |
| `ingress.tls.source`      | "rancher"    | `string` - 从哪里获取 ingress 的证书 - "rancher, letsEncrypt, secret"   |
| `letsEncrypt.email`       | " "          | `string` - 您的邮箱地址                                                 |
| `letsEncrypt.environment` | "production" | `string` - 可选项: "staging, production"                                |
| `privateCA`               | false        | `bool` - 如果您的证书是通过私有 CA 签发的，那么您需要设置这个值为`true` |

## 高级选项

| 选项                           | 默认值                                              | 描述                                                                                                                                                                                                                                                  |
| :----------------------------- | :-------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| `additionalTrustedCAs`         | false                                               | `bool` - 请参阅 [附加授信 CAs](#附加授信-cas)                                                                                                                                                                                                         |
| `addLocal`                     | "auto"                                              | `string` - 使 Rancher 发现并且导入"local" Rancher Server 集群 [导入"local"集群](#导入local集群)                                                                                                                                                       |
| `antiAffinity`                 | "preferred"                                         | `string` - Rancher Pod 反亲和性规则 - "preferred, required"                                                                                                                                                                                           |
| `auditLog.destination`         | "sidecar"                                           | `string` - 发送审计日志到 sidecar 容器的 console，或发送到 hostPath 卷 - "sidecar, hostPath"                                                                                                                                                          |
| `auditLog.hostPath`            | "/var/log/rancher/audit"                            | `string` - 主机上的日志文件目标地址 (仅在 `auditLog.destination` 的值为 `hostPath`时可用)                                                                                                                                                             |
| `auditLog.level`               | 0                                                   | `int` - 设置[API 审计日志](/docs/rancher2.5/installation/resources/advanced/api-audit-log/_index)等级。0 代表关闭。[0-3]                                                                                                                              |
| `auditLog.maxAge`              | 1                                                   | `int` - 保留旧审计日志的最大天数 (仅在 `auditLog.destination` 的值为 `hostPath`时可用)                                                                                                                                                                |
| `auditLog.maxBackup`           | 1                                                   | `int` - 保留旧审计日志的最大文件个数 (仅在 `auditLog.destination` 的值为 `hostPath`时可用)                                                                                                                                                            |
| `auditLog.maxSize`             | 100                                                 | `int` - 在审计日志被轮换前的以 M 为单位的最大容量 (仅在 `auditLog.destination` 的值为 `hostPath`时可用)                                                                                                                                               |
| `busyboxImage`                 | "busybox"                                           | `string` - 用来收集审计日志的 busybox 镜像的地址。_注意：从 v2.2.0 开始可用_                                                                                                                                                                          |
| `certmanager.version`          | ""                                                  | `string` - set cert-manager compatibility                                                                                                                                                                                                             |
| `debug`                        | false                                               | `bool` - 设置 Rancher Server 的 debug 参数                                                                                                                                                                                                            |
| `extraEnv`                     | []                                                  | `list` - 设置 Rancher Server 的额外环境变量 _注意：从 v2.2.0 开始可用_                                                                                                                                                                                |
| `imagePullSecrets`             | []                                                  | `list` - 私有镜像仓库登录凭证的密文名称列表。                                                                                                                                                                                                         |
| `ingress.extraAnnotations`     | {}                                                  | `map` - 加到 ingress 中的额外 annotation，从而自定义 ingress                                                                                                                                                                                          |
| `ingress.configurationSnippet` | ""                                                  | `string` - 添加额外的 Nginx 配置。可以用来配置代理。_注意：从 v2.0.15, v2.1.10 和 v2.2.4 开始可用_                                                                                                                                                    |
| `ingress.extraAnnotations`     | {}                                                  | `map` - 额外的 annotations，用于自定义 ingress                                                                                                                                                                                                        |
| `ingress.enabled`              | true                                                | 当设置为 false 时，Helm 不会安装 Rancher ingress。将该选项设为 false，需要你自己部署 ingress。_从 v2.5.6 版起可用_。                                                                                                                                  |
| `letsEncrypt.ingress.class`    | ""                                                  | `string` - cert-manager acmesolver ingress 的可选 ingress 类，用于响应 Let's Encrypt ACME challenges。选项：Traefik、nginx。                                                                                                                          |     |
| `proxy`                        | ""                                                  | `string` - 给 Rancher 配置 HTTP[S] 代理                                                                                                                                                                                                               |
| `noProxy`                      | 127.0.0.0/8,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16 | `string` - 通过逗号分隔的不使用代理的 hostnames 或 ip 地址列表。                                                                                                                                                                                      |
| `rancherImage`                 | "rancher/rancher"                                   | `string` - Rancher 镜像的地址                                                                                                                                                                                                                         |
| `rancherImageTag`              | same as chart version                               | `string` - rancher/rancher 镜像标签                                                                                                                                                                                                                   |
| `rancherImagePullPolicy`       | “IfNotPresent”                                      | `string` - 覆盖 rancher server 镜像的 imagePullPolicy - “Always”、“Never”、“IfNotPresent”                                                                                                                                                             |
| `replicas`                     | 3                                                   | `int` - Rancher Pod 的副本数量                                                                                                                                                                                                                        |
| `resources`                    | {}                                                  | `map` - Rancher pod 的资源预留和资源限制。                                                                                                                                                                                                            |
| `restrictedAdmin`              | `false`                                             | _在 Rancher v2.5 中可用_ `bool` - 当该选项设置为 "true" 时，初始 Rancher 用户对本地 Kubernetes 集群的访问受到限制，以防止权限升级。更多信息，请参见[restricted-admin 角色](/docs/rancher2.5/admin-settings/rbac/global-permissions/_index#受限管理员) |
| `systemDefaultRegistry`        | ""                                                  | `string` - 全部系统组件相关的 Docker 镜像的私有仓库地址。例如：`http://registry.example.com/`_注意：从 v2.3.0 开始可用_                                                                                                                               |
| `tls`                          | "ingress"                                           | `string` - 请参阅 [外部 TLS Termination](#外部-tls-termination) - "ingress, external"                                                                                                                                                                 |
| `useBundledSystemChart`        | `false`                                             | `bool` - 选择是否用打包在 Rancher Server 容器内的`system-charts`。这个参数是针对离线环境使用的。_注意：从 v2.3.0 开始可用_                                                                                                                            |

## API 审计日志

启用[API 审计日志]（/docs/rancher2.5/installation/api-auditing/\_index）。

你可以像收集任何容器日志一样收集此日志。为 Rancher Server 集群上的 `System` 项目启用[日志]（/docs/rancher2.5/logging/\_index）。

```plain
--set auditLog.level=1
```

默认情况下，启用`审计日志`将在 Rancher pod 中创建一个 sidecar 容器。这个容器（`rancher-audit-log`）会将日志流传输到 `stdout`。您可以像收集任何容器日志一样收集此日志。将 sidecar 用作审计日志时， `hostPath` ， `maxAge` ， `maxBackups` 和 `maxSize`选项不适用。建议使用您的操作系统或 Docker 守护进程的日志轮换功能来控制磁盘空间的使用。请为 Rancher Server 启用[日志服务](/docs/rancher2.5/logging/_index)。

将 `auditLog.destination` 的值设置为 `hostPath`，可以将日志转发至与主机系统共享的卷，而不是传输到 Sidecar 容器。将目标设置为 `hostPath` 时，您可能需要调整其他 auditLog 参数以进行日志轮换。

## 设置额外环境变量

_自 v2.2.0 起可用_

您可以使用 `extraEnv` 为 Rancher Server 设置额外的环境变量。该列表使用与容器清单定义相同的 `name` 和 `value` 键。记住需要给值加上引号。

```plain
--set 'extraEnv[0].name=CATTLE_TLS_MIN_VERSION'
--set 'extraEnv[0].value=1.0'
```

## TLS 设置

当您在 Kubernetes 集群内安装 Rancher 时，TLS 会在集群的 ingress controller 上卸载。支持的 TLS 设置取决于使用的 ingress controller。

参见[TLS 设置](/docs/rancher2.5/installation/resources/tls-settings/_index)以了解更多信息和选项。

## 导入 `local` 集群

默认情况下，Rancher Server 将检测并导入正在运行的 `local` 集群。有权访问 `local` 集群的用户具有对 Rancher Server 管理的所有集群的 `root` 访问权限。

如果在你的环境中存在这个问题，你可以在初次安装时将此选项设置为 `false`。

> 注意事项: 如果您关闭 addLocal，大多数 Rancher v2.5 功能将无法使用，包括 EKS provisioner。

如果这在您的环境中是一个问题，您可以在初始安装时将此选项设置为“false”。此选项仅在第一次安装 Rancher 时有效。参阅 [Issue 16522](https://github.com/rancher/rancher/issues/16522) 获取更多信息。

```plain
--set addLocal="false"
```

## 自定义 Ingress

要自定义或使用 Rancher Server 的其他 Ingress，您可以设置自己的 Ingress annotations。

设置自定义证书颁发者的示例：

```plain
--set ingress.extraAnnotations.'certmanager\.k8s\.io/cluster-issuer'=ca-key-pair
```

使用 `ingress.configurationSnippet` 设置静态代理头的示例。该值像模板一样进行解析，因此可以使用变量。

```plain
--set ingress.configurationSnippet='more_set_input_headers X-Forwarded-Host {{ .Values.hostname }};'
```

## HTTP 代理

Rancher 需要 Internet 访问才能使用某些功能 (helm charts)。使用 `proxy` 设置你的代理服务器。

在 `noProxy` 列表中添加例外的 IP。确保添加了 Pod 集群 IP 范围（默认：`10.42.0.0/16`），Service 集群 IP 范围（默认：`10.43.0.0/16`），内部集群域（默认：`.svc, .cluster.local`）和任何 worker 集群 `controlplane` 节点。Rancher 在这个列表中支持 CIDR 符号范围。

```plain
--set proxy="http://<username>:<password>@<proxy_url>:<proxy_port>/"
--set noProxy="127.0.0.0/8\,10.0.0.0/8\,172.16.0.0/12\,192.168.0.0/16\,.svc\,.cluster.local"
```

## 附加授信 CAs

如果您有私有镜像仓库（registries）、应用商店（catalogs） 或拦截证书的代理，则可能需要向 Rancher 添加额外的受信任的 CA。

```plain
--set additionalTrustedCAs=true
```

创建完 Rancher deployment 后，将 pem 格式的 CA 证书复制到一个名为 `ca-additional.pem` 的文件中，并使用 `kubectl` 在 `cattle-system` 命名空间中创建 `tls-ca-additional` secret。

```plain
kubectl -n cattle-system create secret generic tls-ca-additional --from-file=ca-additional.pem=./ca-additional.pem
```

## 私有镜像仓库(Registry)和离线安装

有关使用私有 registry 安装 Rancher 的详细信息，请参阅

- [离线环境安装指南](/docs/rancher2.5/installation/other-installation-methods/air-gap/_index)。

## 外部 TLS Termination

我们建议将负载均衡器配置为 4 层均衡器，将普通 80/tcp 和 443/tcp 转发到 Rancher 管理集群节点。集群上的 Ingress Controller 会将端口 80 上的 http 通信重定向到端口 443 上的 https。

您可以在 Rancher 集群（ingress）外部的 L7 负载均衡器上终止 SSL/TLS。使用 `--set tls=external` 选项，将负载均衡器指向所有 Rancher 集群节点上的端口 http 80。这将在 http 端口 80 上公开 Rancher 接口。请注意，允许直接连接到 Rancher 集群的客户端将不会被加密。如果您选择这样做，我们建议您将网络级别上的直接访问限制为仅用于您的负载均衡器。

> **注意事项:** 如果您使用的是专用 CA 签名的证书，请添加 `--set privateCA=true` 并参阅[添加 TLS 密文 - 使用私有的 CA 签名证书](/docs/rancher2.5/installation/resources/tls-secrets/_index)来完成给 Rancher 添加 CA 证书。

您的负载均衡器必须支持长期存在的 Websocket 连接，并且需要插入代理标头，以便 Rancher 可以正确路由链接。

### 使用 NGINX v0.25 为外部 TLS 配置 Ingress

在 NGINX v0.25 中，关于转发头和外部 TLS Termination，NGINX 的行为已[更改](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md#0220)。因此，在将外部 TLS Termination 配置与 NGINX v0.25 结合使用的情况下，必须编辑 `cluster.yml` 来启用用于 ingress 的 `use-forwarded-headers` 选项:

```yaml
ingress:
  provider: nginx
  options:
    use-forwarded-headers: 'true'
```

### 必要的 Headers

- `Host`
- `X-Forwarded-Proto`
- `X-Forwarded-Port`
- `X-Forwarded-For`

### 建议的超时时间

- Read Timeout: `1800 seconds`
- Write Timeout: `1800 seconds`
- Connect Timeout: `30 seconds`

### 健康检查

Rancher 将对 `/healthz` 端点上的健康检查做出 `200` 响应。

### NGINX 配置示例

此 NGINX 配置已在 NGINX 1.14 上进行了测试。

> **注意事项:** 此 NGINX 配置只是一个示例，可能不适合您的环境。完整文档请参阅[NGINX 负载均衡 - HTTP 负载均衡](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)。

- 将 `IP_NODE1`，`IP_NODE2` 和 `IP_NODE3` 替换为集群中节点的 IP 地址。
- 将两个出现的 `FQDN` 替换为 Rancher 的 DNS 名称。
- 将 `/certs/fullchain.pem` 和 `/certs/privkey.pem` 分别替换为服务器证书和服务器证书密钥的位置。

```
worker_processes 4;
worker_rlimit_nofile 40000;

events {
    worker_connections 8192;
}

http {
    upstream rancher {
        server IP_NODE_1:80;
        server IP_NODE_2:80;
        server IP_NODE_3:80;
    }

    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen 443 ssl http2;
        server_name FQDN;
        ssl_certificate /certs/fullchain.pem;
        ssl_certificate_key /certs/privkey.pem;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Forwarded-Port $server_port;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://rancher;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            # This allows the ability for the execute shell window to remain open for up to 15 minutes. Without this parameter, the default is 1 minute and will automatically close.
            proxy_read_timeout 900s;
            proxy_buffering off;
        }
    }

    server {
        listen 80;
        server_name FQDN;
        return 301 https://$server_name$request_uri;
    }
}
```
