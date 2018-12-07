# Notice about fixed Kubernetes vulnerability CVE-2018-1002105 {#concept_a3g_1sf_1gb .concept}

Alibaba Cloud has fixed system vulnerability CVE-2018-1002105. This topic describes the impacts of this vulnerability and how to remove it.

## Background information {#section_hrq_zsf_1gb .section}

Engineers of the Kubernetes community have found security vulnerability CVE-2018-1002105. Kubernetes users can gain access to the backend service by forging the request and escalating the permission on the established API Server connection. Alibaba Cloud has fixed this vulnerability. To remove the vulnerability, you need to log on to the Container Service console and upgrade Kubernetes to the latest version.

For more information about the vulnerability CVE-2018-1002105, see [https://github.com/kubernetes/kubernetes/issues/71411](https://github.com/kubernetes/kubernetes/issues/71411).

## Affected Kubernetes versions: {#section_edc_ftf_1gb .section}

-   Kubernetes v1.0.x-1.9.x
-   Kubernetes v1.10.0-1.10.10 \(fixed in v1.10.11\)
-   Kubernetes v1.11.0-1.11.4 \(fixed in v1.11.5\)
-   Kubernetes v1.12.0-1.12.2 \(fixed in v1.12.3\)

## Affected configurations: {#section_bmw_h5f_1gb .section}

-   Kubernetes cluster, which runs on Container Service and uses an extension API server. Furthermore, the extension API server network is directly accessible to the cluster component, kube-apiserver.
-   Kubernetes cluster, which runs on Container Service and has opened permissions to interfaces such as pod exec, attach, and portforward. Then, users can use the vulnerability to obtain permissions to access all kubelet APIs of the cluster.

## Cluster configuration of Alibaba Cloud Container Service for Kubernetes {#section_pcs_r5f_1gb .section}

-   The API server of a Kubernetes cluster that runs on Container Service has RBAC enabled by default. That is, the API server denies anonymous user access through primary account authorization. Furthermore, the starting parameter of Kubelet is `anonymous-auth=false`, providing security access control against external attacks.
-   If your Kubernetes cluster has multiple RAM users, the RAM users may gain unauthorized access to the backend service through interfaces such as pod exec, attach, and portforward. If your cluster has no RAM users, you do not need to worry about the vulnerability.
-   RAM users do not have access to aggregate API resources by default without custom authorization from the primary account.

## Solution {#section_v3t_lvf_1gb .section}

Log on to the Container Service console to upgrade your cluster. For more information, see [Upgrade a cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Upgrade a cluster.md#).

-   If your cluster is V1.11.2, upgrade it to V1.11.5.
-   If your cluster is V1.10.4, upgrade it to V1.10.11 or V1.11.5.
-   If your cluster is V1.9 or earlier, upgrade it to V1.10.11 or V1.11.5. When you upgrade the cluster from V1.9 to V1.10 or V1.11, upgrade the flexvolume plugin through the console if your cluster uses cloud disk volumes.

    **Note:** In the Container Service console, select the target cluster and choose **More** \> **Addon Upgrade**. In the Addon Upgrade dialog box, select **flexvolume** and click **Upgrade**.


This vulnerability does not affect Serverless Kubernetes clusters. Serverless Kubernetes was upgraded before the vulnerability occurred.
