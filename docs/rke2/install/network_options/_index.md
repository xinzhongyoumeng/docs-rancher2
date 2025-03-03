---
title: 网络选项
description: 默认情况下，RKE2 将 Canal 作为 cni 运行，VXLAN 作为默认后端，Canal 在主要组件启动并运行后通过 Helm Chart安装，可以通过修改 helm chart 选项进行自定义。
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
  - RKE2
  - 网络选项
---

RKE2 需要一个 CNI 插件来连接 pod 和 services。Canal CNI 插件是默认的，从一开始就被支持。从 RKE2 v1.21 开始，有两个额外支持的 CNI 插件：Calico 和 Cilium。 所有的 CNI 插件都是在主要组件运行后通过 Helm Chart 安装的，可以通过修改 Helm Chart 的选项进行自定义。

本页主要介绍设置 RKE2 时可用的网络选项：

- [安装 CNI 插件](#安装-cni-插件)
- [Dual-stack 配置](#dual-stack-配置)
- [使用 Multus](#使用-multus)

## 安装 CNI 插件

接下来的标签告知如何部署每个 CNI 插件并覆盖默认选项。

#### Canal CNI 插件

Canal 意味着使用 Flannel 处理节点间流量，使用 Calico 处理节点内流量和网络策略。默认情况下，它将使用 vxlan 封装在节点之间创建一个 overlay 网络。Canal 默认部署在 RKE2 中的，因此不需要配置就可以激活它。要覆盖默认的 Canal 选项，你应该创建一个 HelmChartConfig 资源。HelmChartConfig 资源必须与其对应的 HelmChart 的名称和命名空间相匹配。例如，要覆盖 flannel 接口，你可以应用以下配置:

```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-canal
  namespace: kube-system
spec:
  valuesContent: |-
    flannel:
      iface: "eth1"
```

关于 Canal 配置的全部选项的更多信息，请参考[rke2-charts](https://github.com/rancher/rke2-charts/blob/main-source/packages/rke2-canal/charts/values.yaml)。

目前 RKE2 的 Window 安装中不支持 Canal。

#### Cilium CNI 插件

从 RKE2 v1.21 开始，Cilium 可以作为 CNI 插件被部署。要做到这一点，请将 `cilium` 作为 `--cni` 标志的值。要覆盖默认选项，请使用 HelmChartConfig 资源。HelmChartConfig 资源必须符合对应的 HelmChart 的名称和命名空间。例如，要启用 eni:

```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-canal
  namespace: kube-system
spec:
  valuesContent: |-
    flannel:
      iface: "eth1"
```

更多关于 Cilium chart 的可用值，请参考[rke2-charts 资源库](https://github.com/rancher/rke2-charts/blob/main-source/packages/rke2-cilium/charts/values.yaml)。

目前 RKE2 的 Windows 安装中不支持 Cilium。

#### Calico CNI 插件

从 RKE2 v1.21 开始，Calico 可以作为 CNI 插件被部署。要做到这一点，请将 `calico` 作为 `--cni` 标志的值。要覆盖默认选项，请使用 HelmChartConfig 资源。HelmChartConfig 资源必须符合对应的 HelmChart 的名称和命名空间。例如，要改变 mtu：

```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-calico
  namespace: kube-system
spec:
  valuesContent: |-
    installation:
      calicoNetwork:
        mtu: 9000
```

关于 Calico chart 的可用值的更多信息，请参考[rke2-charts 资源库](https://github.com/rancher/rke2-charts/blob/main/charts/rke2-calico/rke2-calico/v3.19.2-204/values.yaml)

## Dual-stack 配置

IPv4/IPv6 dual-stack 网络可以为 Pod 和 Service 同时分配 IPv4 和 IPv6 地址。RKE2 从 v1.21 版开始支持该功能，但默认情况下并未激活。为了正确激活它，RKE2 和所选的 CNI 插件都必须进行相应的配置。要在 dual-stack 模式下配置 RKE2，只需为 pods 和 service 设置一个有效的 IPv4/IPv6 dual-stack cidr。要做到这一点，请使用标志 `--cluster-cidr` 和 `--service-cidr`，例如。

```bash
--cluster-cidr 10.42.0.0/16,2001:cafe:42:0::/56
--service-cidr 10.43.0.0/16,2001:cafe:42:1::/112
```

每个 CNI 插件都需要不同的配置来实现 dual-stack：

#### Canal CNI 插件

Canal 目前不支持 dual-stack。要跟踪这方面的进展，请查看[dual-stack in canal issue](https://github.com/rancher/rke2/issues/1883)

#### Cilium CNI 插件

使用 HelmChartConfig 启用 ipv6 参数：

```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: rke2-cilium
  namespace: kube-system
spec:
  valuesContent: |-
    cilium:
      ipv6:
        enabled: true
```

#### Calico CNI 插件

Calico 自动检测 dual-stack 的 RKE2 配置，不需要任何额外配置。当以 dual-stack 模式部署时，它会创建两个不同的 ippool 资源。注意，当使用 dual-stack 时，Calico 利用 BGP 而不是 VXLAN 封装。目前 RKE2 的 windows 系统不支持 dual-stack 和 BGP。

## 使用 Multus

从 RKE2 v1.21 开始，可以部署 Multus CNI meta-plugin。请注意，这是为高级用户准备的。

[Multus CNI](https://github.com/k8snetworkplumbingwg/multus-cni)是一个 CNI 插件，能够将多个网络接口附加到 pod 上。Multus 并不取代 CNI 插件，相反，它充当了 CNI 插件的复用器。Multus 在某些用例中很有用，特别是当 pod 是网络密集型的，需要额外的网络接口来支持数据平面加速技术，如 SR-IOV。

Multus 不能独立部署。它总是需要至少一个传统的 CNI 插件，以满足 Kubernetes 集群的网络要求。该 CNI 插件成为 Multus 的默认插件，并将被用来为所有的 pod 提供主接口。

要启用 Multus，请将`multus`作为第一个值传给`--cni`标志，然后是你想和 Multus 一起使用的插件名称（如果你将只用自己的默认插件，则为`none`）。注意，Multus 必须总是在列表的第一个位置。例如，要使用 Multus 和 `canal` 作为默认插件，你可以指定 `--cni=multus,canal` 或 `--cni=multus --cni=canal`。

关于 Multus 的更多信息，请参考[multus-cni](https://github.com/k8snetworkplumbingwg/multus-cni/tree/master/docs)文档。

### 使用 Multus 与 containernetworking 插件

任何 CNI 插件都可以作为 Multus 的次要 CNI 插件，以提供连接到一个 pod 的额外网络接口。然而，最常见的是使用由 containernetworking 团队维护的 CNI 插件（bridge、host-device、macvlan 等）作为 Multus 的辅助 CNI 插件。这些 containernetworking 插件会在安装 Multus 时自动部署。关于这些插件的更多信息，请参阅 [containernetworking plugins](https://www.cni.dev/plugins/current) 文档。

要使用这些插件中的任何一个，需要创建一个适当的 NetworkAttachmentDefinition 对象来定义二级网络的配置。然后，该定义被 pod 注释所引用，Multus 将使用这些注释来为该 pod 提供额外的接口。[multus-cni 存储库](https://github.com/k8snetworkplumbingwg/multus-cni/blob/master/docs/quickstart.md#storing-a-configuration-as-a-custom-resource)中提供了将 Macvlan cni 插件与 Mu 一起使用的示例。

### 使用 Multus 与 SR-IOV （实验性）

:::note 注意：
这是一个实验性的功能，在 v1.21.2+rke2r1 中引入。
:::

将 SR-IOV CNI 与 Multus 一起使用可以帮助解决数据平面加速的用例，在 Pod 中提供一个额外的接口，可以实现非常高的吞吐量。SR-IOV 并非在所有环境中都有效，并且必须满足一些要求才能将节点视为具有 SR-IOV 功能的节点：

- 物理网卡必须支持 SR-IOV（例如通过检查/sys/class/net/$NIC/device/sriov_totalvfs）
- 主机操作系统必须激活 IOMMU 虚拟化
- 主机操作系统包括能够进行 SR-IOV 的驱动程序（如 i40e，vfio-pci 等）

SR-IOV CNI 插件不能作为 Multus 的默认 CNI 插件使用；它必须与 Multus 和传统的 CNI 插件一起部署。SR-IOV CNI 的 helm chart 可以在 `rancher-charts` helm repo 中找到。更多信息见[Rancher Helm Charts 文档](https://rancher.com/docs/rancher/v2.x/en/helm-charts/)。

在安装完 SR-IOV CNI chart 后，将部署 SR-IOV operator。然后，用户必须指定集群中的哪些节点具有 SR-IOV 能力，给它们贴上`feature.node.kubernetes.io/network-sriov.cable=true`：

```
kubectl label node $NODE-NAME feature.node.kubernetes.io/network-sriov.capable=true
```

一旦贴上标签，sriov-network-config Daemonset 将部署一个 Pod 到节点上，以收集网络接口的信息。这些信息可以通过 `sriovnetworknodestates` 自定义资源定义获得。部署几分钟后，每个节点将有一个 `sriovnetworknodestates` 资源，节点的名称是资源名称。

关于如何使用 SR-IOV operator 的更多信息，请参考[sriov-network-operator](https://github.com/k8snetworkplumbingwg/sriov-network-operator/blob/master/doc/quickstart.md#configuration)
