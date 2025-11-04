# 1. Explain the three big concepts of Helm: Charts, Repositories, and Releases.
## Answer:
**Chart**: A Helm package. It contains all the resource definitions needed to run an application, tool, or service inside a Kubernetes cluster. It's like a Homebrew formula, an Apt dpkg, or a Yum RPM file.

**Repository**: A place where charts can be collected and shared. It's simply a HTTP server that houses index.yaml and packaged .tgz chart files.

**Release**: A specific instance of a chart running in a Kubernetes cluster. When you run helm install, you create a new release. One chart can be installed multiple times into the same cluster, and each will be its own independent release.
# 2. What is the difference between helm template and helm install?
## Answer:
**helm template**: This command renders the chart templates locally and outputs the resulting raw Kubernetes YAML manifests to stdout. It does not install anything into the cluster. It's used for debugging, dry-running, or integrating with GitOps workflows (e.g., to see what would be deployed).

**helm install**: This command takes a chart, renders the templates, and sends the resulting manifests to the Kubernetes API server to create the resources, thus creating a new release.
# 3. What is Tiller and why is it deprecated in Helm 3?
## Answer:
**Tiller** was the server-side component of Helm 2. It lived inside the Kubernetes cluster and was responsible for interacting with the Kubernetes API server to manage the lifecycle of releases.

**Why it was deprecated:**
Tiller had broad RBAC permissions (often cluster-admin) to function, which posed a significant security risk. If compromised, it could be used to attack the entire cluster. 
Helm 3 removed Tiller entirely, adopting a client-only architecture. Now, the Helm client uses the user's own kubeconfig credentials, which aligns with standard Kubernetes RBAC and is much more secure.

# 4. What are the differences between Helm 2 and Helm 3?
## Answer:
Helm 2 used Tiller (a server-side component) for releases, which had security issues due to RBAC. 
Helm 3 removed Tiller, making it client-only, improved release management with CRDs, and uses three-way merge for upgrades.
