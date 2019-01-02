---
ID: 3895
post_title: >
  Bootstrapping the Kubernetes Control
  Plane
author: sher
post_excerpt: ""
layout: post
permalink: >
  http://codingbee.net/uncategorized/docker-cheatsheet
published: true
post_date: 2019-01-02 12:58:21
---
In this lab you will bootstrap the Kubernetes control plane across three compute instances and configure it for high availability. You will also create an external load balancer that exposes the Kubernetes API Servers to remote clients. The following components will be installed on each node: Kubernetes API Server, Scheduler, and Controller Manager.
<h2>Prerequisites</h2>
The commands in this lab must be run on each controller instance: <code>controller-0</code>, <code>controller-1</code>, and <code>controller-2</code>. Login to each controller instance using the <code>gcloud</code> command. Example:
<pre><code>gcloud compute ssh controller-0</code></pre>
<h3>Running commands in parallel with tmux</h3>
<a href="https://github.com/tmux/tmux/wiki">tmux</a> can be used to run commands on multiple compute instances at the same time. See the <a href="01-prerequisites.md#running-commands-in-parallel-with-tmux">Running commands in parallel with tmux</a> section in the Prerequisites lab.
<h2>Provision the Kubernetes Control Plane</h2>
Create the Kubernetes configuration directory:
<pre><code>sudo mkdir -p /etc/kubernetes/config</code></pre>
<h3>Download and Install the Kubernetes Controller Binaries</h3>
Download the official Kubernetes release binaries:
<pre><code>wget -q --show-progress --https-only --timestamping \
  "https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-apiserver" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-controller-manager" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kubectl"</code></pre>
Install the Kubernetes binaries:
<pre><code>{
  chmod +x kube-apiserver kube-controller-manager kube-scheduler kubectl
  sudo mv kube-apiserver kube-controller-manager kube-scheduler kubectl /usr/local/bin/
}</code></pre>
<h3>Configure the Kubernetes API Server</h3>
<pre><code>{
  sudo mkdir -p /var/lib/kubernetes/

  sudo mv ca.pem ca-key.pem kubernetes-key.pem kubernetes.pem \
    service-account-key.pem service-account.pem \
    encryption-config.yaml /var/lib/kubernetes/
}</code></pre>
The instance internal IP address will be used to advertise the API Server to members of the cluster. Retrieve the internal IP address for the current compute instance:
<pre><code>INTERNAL_IP=$(curl -s -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/ip)</code></pre>
Create the <code>kube-apiserver.service</code> systemd unit file:
<pre><code>cat &lt;&lt;EOF | sudo tee /etc/systemd/system/kube-apiserver.service
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-apiserver \\
  --advertise-address=${INTERNAL_IP} \\
  --allow-privileged=true \\
  --apiserver-count=3 \\
  --audit-log-maxage=30 \\
  --audit-log-maxbackup=3 \\
  --audit-log-maxsize=100 \\
  --audit-log-path=/var/log/audit.log \\
  --authorization-mode=Node,RBAC \\
  --bind-address=0.0.0.0 \\
  --client-ca-file=/var/lib/kubernetes/ca.pem \\
  --enable-admission-plugins=Initializers,NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \\
  --enable-swagger-ui=true \\
  --etcd-cafile=/var/lib/kubernetes/ca.pem \\
  --etcd-certfile=/var/lib/kubernetes/kubernetes.pem \\
  --etcd-keyfile=/var/lib/kubernetes/kubernetes-key.pem \\
  --etcd-servers=https://10.240.0.10:2379,https://10.240.0.11:2379,https://10.240.0.12:2379 \\
  --event-ttl=1h \\
  --experimental-encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\
  --kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \\
  --kubelet-client-certificate=/var/lib/kubernetes/kubernetes.pem \\
  --kubelet-client-key=/var/lib/kubernetes/kubernetes-key.pem \\
  --kubelet-https=true \\
  --runtime-config=api/all \\
  --service-account-key-file=/var/lib/kubernetes/service-account.pem \\
  --service-cluster-ip-range=10.32.0.0/24 \\
  --service-node-port-range=30000-32767 \\
  --tls-cert-file=/var/lib/kubernetes/kubernetes.pem \\
  --tls-private-key-file=/var/lib/kubernetes/kubernetes-key.pem \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF</code></pre>
<h3>Configure the Kubernetes Controller Manager</h3>
Move the <code>kube-controller-manager</code> kubeconfig into place:
<pre><code>sudo mv kube-controller-manager.kubeconfig /var/lib/kubernetes/</code></pre>
Create the <code>kube-controller-manager.service</code> systemd unit file:
<pre><code>cat &lt;&lt;EOF | sudo tee /etc/systemd/system/kube-controller-manager.service
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-controller-manager \\
  --address=0.0.0.0 \\
  --cluster-cidr=10.200.0.0/16 \\
  --cluster-name=kubernetes \\
  --cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\
  --cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\
  --kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
  --leader-elect=true \\
  --root-ca-file=/var/lib/kubernetes/ca.pem \\
  --service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\
  --service-cluster-ip-range=10.32.0.0/24 \\
  --use-service-account-credentials=true \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF</code></pre>
<h3>Configure the Kubernetes Scheduler</h3>
Move the <code>kube-scheduler</code> kubeconfig into place:
<pre><code>sudo mv kube-scheduler.kubeconfig /var/lib/kubernetes/</code></pre>
Create the <code>kube-scheduler.yaml</code> configuration file:
<pre><code>cat &lt;&lt;EOF | sudo tee /etc/kubernetes/config/kube-scheduler.yaml
apiVersion: componentconfig/v1alpha1
kind: KubeSchedulerConfiguration
clientConnection:
  kubeconfig: "/var/lib/kubernetes/kube-scheduler.kubeconfig"
leaderElection:
  leaderElect: true
EOF</code></pre>
Create the <code>kube-scheduler.service</code> systemd unit file:
<pre><code>cat &lt;&lt;EOF | sudo tee /etc/systemd/system/kube-scheduler.service
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-scheduler \\
  --config=/etc/kubernetes/config/kube-scheduler.yaml \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF</code></pre>
<h3>Start the Controller Services</h3>
<pre><code>{
  sudo systemctl daemon-reload
  sudo systemctl enable kube-apiserver kube-controller-manager kube-scheduler
  sudo systemctl start kube-apiserver kube-controller-manager kube-scheduler
}</code></pre>
<blockquote>Allow up to 10 seconds for the Kubernetes API Server to fully initialize.</blockquote>
<h3>Enable HTTP Health Checks</h3>
A <a href="https://cloud.google.com/compute/docs/load-balancing/network">Google Network Load Balancer</a> will be used to distribute traffic across the three API servers and allow each API server to terminate TLS connections and validate client certificates. The network load balancer only supports HTTP health checks which means the HTTPS endpoint exposed by the API server cannot be used. As a workaround the nginx webserver can be used to proxy HTTP health checks. In this section nginx will be installed and configured to accept HTTP health checks on port <code>80</code> and proxy the connections to the API server on <code>https://127.0.0.1:6443/healthz</code>.
<blockquote>The <code>/healthz</code> API server endpoint does not require authentication by default.</blockquote>
Install a basic web server to handle HTTP health checks:
<pre><code>sudo apt-get install -y nginx</code></pre>
<pre><code>cat &gt; kubernetes.default.svc.cluster.local &lt;&lt;EOF
server {
  listen      80;
  server_name kubernetes.default.svc.cluster.local;

  location /healthz {
     proxy_pass                    https://127.0.0.1:6443/healthz;
     proxy_ssl_trusted_certificate /var/lib/kubernetes/ca.pem;
  }
}
EOF</code></pre>
<pre><code>{
  sudo mv kubernetes.default.svc.cluster.local \
    /etc/nginx/sites-available/kubernetes.default.svc.cluster.local

  sudo ln -s /etc/nginx/sites-available/kubernetes.default.svc.cluster.local /etc/nginx/sites-enabled/
}</code></pre>
<pre><code>sudo systemctl restart nginx</code></pre>
<pre><code>sudo systemctl enable nginx</code></pre>
<h3>Verification</h3>
<pre><code>kubectl get componentstatuses --kubeconfig admin.kubeconfig</code></pre>
<pre><code>NAME                 STATUS    MESSAGE              ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-2               Healthy   {"health": "true"}
etcd-0               Healthy   {"health": "true"}
etcd-1               Healthy   {"health": "true"}</code></pre>
Test the nginx HTTP health check proxy:
<pre><code>curl -H "Host: kubernetes.default.svc.cluster.local" -i http://127.0.0.1/healthz</code></pre>
<pre><code>HTTP/1.1 200 OK
Server: nginx/1.14.0 (Ubuntu)
Date: Sun, 30 Sep 2018 17:44:24 GMT
Content-Type: text/plain; charset=utf-8
Content-Length: 2
Connection: keep-alive

ok</code></pre>
<blockquote>Remember to run the above commands on each controller node: <code>controller-0</code>, <code>controller-1</code>, and <code>controller-2</code>.</blockquote>
<h2>RBAC for Kubelet Authorization</h2>
In this section you will configure RBAC permissions to allow the Kubernetes API Server to access the Kubelet API on each worker node. Access to the Kubelet API is required for retrieving metrics, logs, and executing commands in pods.
<blockquote>This tutorial sets the Kubelet <code>--authorization-mode</code> flag to <code>Webhook</code>. Webhook mode uses the <a href="https://kubernetes.io/docs/admin/authorization/#checking-api-access">SubjectAccessReview</a> API to determine authorization.</blockquote>
<pre><code>gcloud compute ssh controller-0</code></pre>
Create the <code>system:kube-apiserver-to-kubelet</code> <a href="https://kubernetes.io/docs/admin/authorization/rbac/#role-and-clusterrole">ClusterRole</a> with permissions to access the Kubelet API and perform most common tasks associated with managing pods:
<pre><code>cat &lt;&lt;EOF | kubectl apply --kubeconfig admin.kubeconfig -f -
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: system:kube-apiserver-to-kubelet
rules:
  - apiGroups:
      - ""
    resources:
      - nodes/proxy
      - nodes/stats
      - nodes/log
      - nodes/spec
      - nodes/metrics
    verbs:
      - "*"
EOF</code></pre>
The Kubernetes API Server authenticates to the Kubelet as the <code>kubernetes</code> user using the client certificate as defined by the <code>--kubelet-client-certificate</code> flag.

Bind the <code>system:kube-apiserver-to-kubelet</code> ClusterRole to the <code>kubernetes</code> user:
<pre><code>cat &lt;&lt;EOF | kubectl apply --kubeconfig admin.kubeconfig -f -
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: system:kube-apiserver
  namespace: ""
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:kube-apiserver-to-kubelet
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: kubernetes
EOF</code></pre>
<h2>The Kubernetes Frontend Load Balancer</h2>
In this section you will provision an external load balancer to front the Kubernetes API Servers. The <code>kubernetes-the-hard-way</code> static IP address will be attached to the resulting load balancer.
<blockquote>The compute instances created in this tutorial will not have permission to complete this section. Run the following commands from the same machine used to create the compute instances.</blockquote>
<h3>Provision a Network Load Balancer</h3>
Create the external load balancer network resources:
<pre><code>{
  KUBERNETES_PUBLIC_ADDRESS=$(gcloud compute addresses describe kubernetes-the-hard-way \
    --region $(gcloud config get-value compute/region) \
    --format 'value(address)')

  gcloud compute http-health-checks create kubernetes \
    --description "Kubernetes Health Check" \
    --host "kubernetes.default.svc.cluster.local" \
    --request-path "/healthz"

  gcloud compute firewall-rules create kubernetes-the-hard-way-allow-health-check \
    --network kubernetes-the-hard-way \
    --source-ranges 209.85.152.0/22,209.85.204.0/22,35.191.0.0/16 \
    --allow tcp

  gcloud compute target-pools create kubernetes-target-pool \
    --http-health-check kubernetes

  gcloud compute target-pools add-instances kubernetes-target-pool \
   --instances controller-0,controller-1,controller-2

  gcloud compute forwarding-rules create kubernetes-forwarding-rule \
    --address ${KUBERNETES_PUBLIC_ADDRESS} \
    --ports 6443 \
    --region $(gcloud config get-value compute/region) \
    --target-pool kubernetes-target-pool
}</code></pre>
<h3>Verification</h3>
Retrieve the <code>kubernetes-the-hard-way</code> static IP address:
<pre><code>KUBERNETES_PUBLIC_ADDRESS=$(gcloud compute addresses describe kubernetes-the-hard-way \
  --region $(gcloud config get-value compute/region) \
  --format 'value(address)')</code></pre>
Make a HTTP request for the Kubernetes version info:
<pre><code>curl --cacert ca.pem https://${KUBERNETES_PUBLIC_ADDRESS}:6443/version</code></pre>
<blockquote>output</blockquote>
<pre><code>{
  "major": "1",
  "minor": "12",
  "gitVersion": "v1.12.0",
  "gitCommit": "0ed33881dc4355495f623c6f22e7dd0b7632b7c0",
  "gitTreeState": "clean",
  "buildDate": "2018-09-27T16:55:41Z",
  "goVersion": "go1.10.4",
  "compiler": "gc",
  "platform": "linux/amd64"
}</code></pre>
Next: <a href="09-bootstrapping-kubernetes-workers.md">Bootstrapping the Kubernetes Worker Nodes</a>