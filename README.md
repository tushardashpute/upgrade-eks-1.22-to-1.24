**Scope**

Upgrade an existing Amazon EKS cluster from version 1.22 to 1.24 while ensuring minimal disruption to the running workloads and maintaining the cluster's integrity and reliability.

**Approach**


![Uploading image.png…]()


**Control Plane Upgrade to 1.23:**

 - Initiate the upgrade process by first upgrading the control plane from version 1.22 to 1.23.

This step will involve minimal downtime as control plane components are upgraded while maintaining the worker nodes on version 1.22.

 - Monitor the control plane upgrade closely to ensure successful completion.

 - Addon Upgrades (Kube-Proxy, VPC-CNI, CoreDNS, EBS-CSI Driver):

  After successfully upgrading the control plane to version 1.23, focus on upgrading essential addons.
  Upgrade Kube-Proxy, VPC-CNI, CoreDNS, and EBS-CSI driver to versions compatible with EKS 1.23.
  Ensure that the addons are upgraded one by one, and their functionality is validated at each step.

**Control Plane Upgrade to 1.24:**

 - With the upgraded addons in place and validated, proceed to upgrade the control plane to version 1.24.

Similar to the first control plane upgrade, this step will ensure that the worker nodes remain unaffected on version 1.22.
Pay close attention to upgrade logs and any potential issues that might arise during this process.

 - Addon Upgrades (Post Control Plane Upgrade):

After completing the control plane upgrade to version 1.24, revisit the addon upgrades.
Ensure that the addons (Kube-Proxy, VPC-CNI, CoreDNS, EBS-CSI driver) are compatible with EKS 1.24 and perform necessary upgrades.
Validate the functionality and performance of the addons after the upgrade.

**Worker Node Upgrades:**

With the control plane and addons upgraded to version 1.24 and validated, proceed to upgrade the worker nodes.
Upgrade the worker nodes directly from version 1.22 to version 1.24.
Implement a phased approach to minimize any potential disruption. Consider draining nodes, updating, and gradually resuming workloads.

_Release Notes 1.23_

  The Kubernetes in-tree to container storage interface (CSI) volume migration feature is enabled. This feature enables the replacement of existing Kubernetes in-tree storage plugins for Amazon EBS with a corresponding Amazon EBS CSI driver. For more information, see Kubernetes 1.17 Feature: Kubernetes In-Tree to CSI Volume Migration Moves to Beta on the Kubernetes blog.
  
  The feature translates in-tree APIs to equivalent CSI APIs and delegates operations to a replacement CSI driver. With this feature, if you use existing StorageClass, PersistentVolume, and PersistentVolumeClaim objects that belong to these workloads, there likely won't be any noticeable change. The feature enables Kubernetes to delegate all storage management operations from the in-tree plugin to the CSI driver. If you use Amazon EBS volumes in an existing cluster, install the Amazon EBS CSI driver in your cluster before you update your cluster to version 1.23. If you don't install the driver before updating an existing cluster, interruptions to your workloads might occur. If you plan to deploy workloads that use Amazon EBS volumes in a new 1.23 cluster, install the Amazon EBS CSI driver in your cluster before deploying the workloads your cluster. For instructions on how to install the Amazon EBS CSI driver on your cluster, see Amazon EBS CSI driver. For frequently asked questions about the migration feature, see Amazon EBS CSI migration frequently asked questions.

  
  Kubernetes stopped supporting dockershim in version 1.20 and removed dockershim in version 1.24. For more information, see Kubernetes is Moving on From Dockershim: Commitments and Next Steps in the Kubernetes blog. Amazon EKS will end support for dockershim starting in Amazon EKS version 1.24. Starting with Amazon EKS version 1.24, Amazon EKS official AMIs will have containerd as the only runtime.
  
  Even though Amazon EKS version 1.23 continues to support dockershim, we recommend that you start testing your applications now to identify and remove any Docker dependencies. This way, you are prepared to update your cluster to version 1.24. For more information about dockershim removal, see Amazon EKS ended support for Dockershim.
  
  Kubernetes graduated IPv4/IPv6 dual-stack networking for Pods, services, and nodes to general availability. However, Amazon EKS and the Amazon VPC CNI plugin for Kubernetes don't support dual-stack networking. Your clusters can assign IPv4 or IPv6 addresses to Pods and services, but can't assign both address types.
  
  Kubernetes graduated the Pod Security Admission (PSA) feature to beta. The feature is enabled by default. For more information, see Pod Security Admission in the Kubernetes documentation. PSA replaces the Pod Security Policy (PSP) admission controller. The PSP admission controller isn't supported and is scheduled for removal in Kubernetes version 1.25.
  
  The PSP admission controller enforces Pod security standards on Pods in a namespace based on specific namespace labels that set the enforcement level. For more information, see Pod Security Standards (PSS) and Pod Security Admission (PSA) in the Amazon EKS best practices guide.
  
  The kube-proxy image deployed with clusters is now the minimal base image maintained by Amazon EKS Distro (EKS-D). The image contains minimal packages and doesn't have shells or package managers.
  
  Kubernetes graduated ephemeral containers to beta. Ephemeral containers are temporary containers that run in the same namespace as an existing Pod. You can use them to observe the state of Pods and containers for troubleshooting and debugging purposes. This is especially useful for interactive troubleshooting when kubectl exec is insufficient because either a container has crashed or a container image doesn't include debugging utilities. An example of a container that includes a debugging utility is distroless images. For more information, see Debugging with an ephemeral debug container in the Kubernetes documentation.
  
  Kubernetes graduated the HorizontalPodAutoscaler autoscaling/v2 stable API to general availability. The HorizontalPodAutoscaler autoscaling/v2beta2 API is deprecated. It will be unavailable in 1.26.
  
  The goaway-chance option in the Kubernetes API server helps prevent HTTP/2 client connections from being stuck on a single API server instance, by randomly closing a connection. When the connection is closed, the client will try to reconnect, and will likely land on a different API server as a result of load balancing. Amazon EKS version 1.23 has enabled goaway-chance flag. If your workload running on Amazon EKS cluster uses a client that is not compatible with HTTP GOAWAY, we recommend that you update your client to handle GOAWAY by reconnecting on connection termination.

**For the complete Kubernetes 1.23 changelog, see https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md#changelog-since-v1220.**



Release Notes 1.24




Starting with Kubernetes 1.24, new beta APIs aren't enabled in clusters by default. By default, existing beta APIs and new versions of existing beta APIs continue to be enabled. Amazon EKS follows the same behavior as upstream Kubernetes 1.24. The feature gates that control new features for both new and existing API operations are enabled by default. This is in alignment with upstream Kubernetes. For more information, see KEP-3136: Beta APIs Are Off by Default on GitHub.

Support for Container Runtime Interface (CRI) for Docker (also known as Dockershim) is removed from Kubernetes 1.24. Amazon EKS official AMIs have containerd as the only runtime. Before moving to Amazon EKS 1.24 or higher, you must remove any reference to bootstrap script flags that aren't supported anymore. You must also make sure that IP forwarding is enabled for your worker nodes. For more information, see Amazon EKS ended support for Dockershim.

If you already have Fluentd configured for Container Insights, then you must migrate Fluentd to Fluent Bit before updating your cluster. The Fluentd parsers are configured to only parse log messages in JSON format. Unlike dockerd, the containerd container runtime has log messages that aren't in JSON format. If you don't migrate to Fluent Bit, some of the configured Fluentd's parsers will generate a massive amount of errors inside the Fluentd container. For more information on migrating, see Set up Fluent Bit as a DaemonSet to send logs to CloudWatch Logs.

In Kubernetes 1.23 and earlier, kubelet serving certificates with unverifiable IP and DNS Subject Alternative Names (SANs) are automatically issued with unverifiable SANs. These unverifiable SANs are omitted from the provisioned certificate. In version 1.24 and later clusters, kubelet serving certificates aren't issued if any SAN can't be verified. This prevents kubectl exec and kubectl logs commands from working. For more information, see Certificate signing considerations before upgrading your cluster to Kubernetes 1.24.

When upgrading an Amazon EKS 1.23 cluster that uses Fluent Bit, you must make sure that it's running k8s/1.3.12 or later. You can do this by reapplying the latest applicable Fluent Bit YAML file from GitHub. For more information, see Setting up Fluent Bit in the Amazon CloudWatch User Guide.




You can use Topology Aware Hints to indicate your preference for keeping traffic in zone when cluster worker nodes are deployed across multiple availability zones. Routing traffic within a zone can help reduce costs and improve network performance. By default, Topology Aware Hints are enabled in Amazon EKS 1.24. For more information, see Topology Aware Hints in the Kubernetes documentation.

The PodSecurityPolicy (PSP) is scheduled for removal in Kubernetes 1.25. PSPs are being replaced with Pod Security Admission (PSA). PSA is a built-in admission controller that uses the security controls that are outlined in the Pod Security Standards (PSS) . PSA and PSS are both beta features and are enabled in Amazon EKS by default. To address the removal of PSP in version 1.25, we recommend that you implement PSS in Amazon EKS. For more information, see Implementing Pod Security Standards in Amazon EKS on the AWS blog.

The client.authentication.k8s.io/v1alpha1 ExecCredential is removed in Kubernetes 1.24. The ExecCredential API was generally available in Kubernetes 1.22. If you use a client-go credential plugin that relies on the v1alpha1 API, contact the distributor of your plugin on how to migrate to the v1 API.

For Kubernetes 1.24, we contributed a feature to the upstream Cluster Autoscaler project that simplifies scaling Amazon EKS managed node groups to and from zero nodes. Previously, for the Cluster Autoscaler to understand the resources, labels, and taints of a managed node group that was scaled to zero nodes, you needed to tag the underlying Amazon EC2 Auto Scaling group with the details of the nodes that it was responsible for. Now, when there are no running nodes in the managed node group, the Cluster Autoscaler calls the Amazon EKS DescribeNodegroup API operation. This API operation provides the information that the Cluster Autoscaler requires of the managed node group's resources, labels, and taints. This feature requires that you add the eks:DescribeNodegroup permission to the Cluster Autoscaler service account IAM policy. When the value of a Cluster Autoscaler tag on the Auto Scaling group powering an Amazon EKS managed node group conflicts with the node group itself, the Cluster Autoscaler prefers the value of the Auto Scaling group tag. This is so that you can override values as needed. For more information, see Autoscaling.

If you intend to use Inferentia or Trainium instance types with Amazon EKS 1.24, you must upgrade to the AWS Neuron device plugin version 1.9.3.0 or later. For more information, see Neuron K8 release [1.9.3.0] in the AWS Neuron Documentation.

Containerd has IPv6 enabled for Pods, by default. It applies node kernel settings to Pod network namespaces. Because of this, containers in a Pod bind to both IPv4 (127.0.0.1) and IPv6 (::1) loopback addresses. IPv6 is the default protocol for communication. Before updating your cluster to version 1.24, we recommend that you test your multi-container Pods. Modify apps so that they can bind to all IP addresses on loopback interfaces. The majority of libraries enable IPv6 binding, which is backward compatible with IPv4. When it’s not possible to modify your application code, you have two options:

Run an init container and set disable ipv6 to true (sysctl -w net.ipv6.conf.all.disable ipv6=1).

Configure a mutating admission webhook to inject an init container alongside your application Pods.

If you need to block IPv6 for all Pods across all nodes, you might have to disable IPv6 on your instances.

The goaway-chance option in the Kubernetes API server helps prevent HTTP/2 client connections from being stuck on a single API server instance, by randomly closing a connection. When the connection is closed, the client will try to reconnect, and will likely land on a different API server as a result of load balancing. Amazon EKS version 1.24 has enabled goaway-chance flag. If your workload running on Amazon EKS cluster uses a client that is not compatible with HTTP GOAWAY, we recommend that you update your client to handle GOAWAY by reconnecting on connection termination.

Risks
ServiceAccount will no longer support automatic secret token creation
This issue has been evaluated and resolution is providedhere
