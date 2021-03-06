# Create a service {#concept_vgg_n1n_vdb .concept}

This topic describes how to create a Kubernetes service with Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Background information {#section_uwt_ory_qwb .section}

A Kubernetes service, known as a service in this and related topics of Alibaba Cloud Container Service for Kubernetes, is an abstract object that defines a logical set of pods and a policy through which to access the pods. Usually, a label selector determines which set of pods are targeted by a service.

In a Kubernetes cluster, each pod has its own IP address, and the pods of a deployment can be removed at any time. However, this action changes the IP addresses of the pods. As a result, directly using IP addresses of pods is ineffective as the scenario does not provide high availability. By comparison, a Kubernetes service decouples the relationship between the frontend and the backend. Specifically, a Kubernetes service is a loose coupling service solution where the operations of the backend do not impact the frontend.

For more information, see [Kubernetes service](https://kubernetes.io/docs/concepts/services-networking/service).

## Limits {#section_6xu_evy_fi7 .section}

To create a service of the Server Load Balancer type, you can select an existing SLB instance for the service. Multiple services can reuse the same SLB instance. For reused SLB instances, the following limits apply:

-   If an existing SLB instance is reused, a new listener is created for the SLB instance and the original listener of the SLB instance is overridden.
-   Only the SLB instances that you manually create by using the console or API can be reused. If an SLB instance is automatically created by the system for a service, it cannot be reused by any other service. Otherwise, the reused SLB instance may be removed incidentally.
-   The multiple services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts will occur.
-   The name of the listener and virtual server group of a reused SLB instance cannot be modified.
-   An SLB instance cannot be reused by services across Kubernetes clusters.

## Prerequisites {#section_s2y_x1n_vdb .section}

A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US//Create a Kubernetes cluster.md#).

## Step 1: Create a deployment {#section_ahh_z1n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Applications** \> **Deployments**. Then click **Create by Template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211022_en-US.png)

3.  Select the target cluster and namespace, and select a custom template or a sample template from the **Resource Type** drop-down list. Then, click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211023_en-US.png)

    In this example, the sample template specifies an Nginx deployment.

    ``` {#codeblock_3at_46h_duv}
    apiVersion: apps/v1beta2 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
       name: nginx-deployment-basic
       labels:
         app: nginx
    spec:
       replicas: 2
       selector:
         matchLabels:
           app: nginx
       template:
         metadata:
           labels:
             app: nginx
         spec:
           containers:
           - name: nginx
             image: nginx:1.7.9                # replace it with your exactly <image_name:tags>
             ports:
             - containerPort: 80                                          ##This port must be exposed in a service.
    ```

4.  Click **Kubernetes Dashboard** to view the running status of this deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211024_en-US.png)


## Step 2: Create a service {#section_efy_x1n_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Ingresses and Load Balancing** \> **Service**.
3.  Select the target cluster and namespace. Then, click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211025_en-US.png)

4.  In the displayed dialog box, set service parameters.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211026_en-US.png)

    -   **Name:** Enter the service name. In this example, the service name is set to nginx-svc.
    -   **Type:** Select the service type, namely, the service access method.
        -   **Cluster IP**: Exposes the service by using the internal IP address of your cluster. If you select this service type, the service is accessible only within the cluster. This is the default service type.
        -   **Node port**: Exposes the service by using the IP address and the static port \(NodePort\) of each node. A node port service routes to a cluster IP service that is automatically created. You can access the node port service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
        -   **Server Load Balancer**: Alibaba Cloud Server Load Balancer \(SLB\) service. To create a service of this type, you can select an existing SLB instance for the service, or set the system to automatically create an SLB instance for the service.

            With this type of service, you can set an Internet or intranet access method for your application. An SLB instance can route to a node port service and a cluster IP service.

    -   **Related**: Select the backend object to associate with the service. In this example, the nginx-deployment-basic deployment created in the preceding step is associated with the service. If you do not associate the service with any objects, the system does not create any corresponding endpoint objects. In this case, you can manually associate the service with your own specific endpoints. For more information, see [Services without selectors](https://kubernetes.io/docs/concepts/services-networking/service/#services-without-selectors).
    -   **Port Mapping**: Add a service port number and a container port number. The container port number that you set must be the same as the port number of the container exposed by the pod.
    -   **annotation**: Add an annotation to the service. You can set SLB parameters. For example, to control the service traffic, you can set the peak bandwidth of the service to 20 Mbit/s by setting this parameter as`service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20`. For more information, see [Access services by using Server Load Balancer](reseller.en-US/User Guide for Kubernetes Clusters/Network management/Access services by using Server Load Balancer.md#).
    -   **Tag**: Add a tag to the service to identify the service.
5.  Click **Create**. The nginx-svc service is then displayed in the service list.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211027_en-US.png)

6.  Enter the external endpoint of the nginx-svc service in your browser to access the service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16662/156466009211028_en-US.png)


