# 30 Most Important Helm Chart Interview Questions for Kubernetes (K8s)

## Table of Contents
- [Introduction to Helm](#introduction-to-helm)
- [Basic Concepts](#basic-concepts)
- [Helm Commands](#helm-commands)
- [Chart Structure](#chart-structure)
- [Values and Templates](#values-and-templates)
- [Chart Repositories](#chart-repositories)
- [Advanced Topics](#advanced-topics)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

---

## Introduction to Helm

### 1. What is Helm and why is it used?

**Answer:** Helm is a package manager for Kubernetes, often referred to as "the Kubernetes package manager." It simplifies the deployment and management of applications on Kubernetes clusters.

**Key Benefits:**
- **Packaging**: Bundles Kubernetes manifests into a single package called a "Chart"
- **Versioning**: Manages application versions and enables rollbacks
- **Templating**: Uses Go templates to create dynamic Kubernetes manifests
- **Dependency Management**: Handles application dependencies automatically
- **Reusability**: Allows sharing and reusing configurations across environments

**Command:**
```bash
# Check Helm version
helm version

# Get help
helm help
```

---

### 2. What are the main components of Helm?

**Answer:** Helm consists of three main components:

1. **Helm CLI (Client)**: Command-line tool that interacts with Kubernetes clusters
2. **Charts**: Packages containing Kubernetes resource definitions
3. **Releases**: Instances of charts deployed to a Kubernetes cluster

**Note:** Helm 3 removed Tiller (server-side component from Helm 2) for improved security.

**Commands:**
```bash
# List all releases
helm list

# Show release history
helm history <release-name>
```

---

## Basic Concepts

### 3. What is a Helm Chart?

**Answer:** A Helm Chart is a collection of files that describe a related set of Kubernetes resources. It's essentially a package that contains:

- Kubernetes manifest templates
- Default configuration values
- Metadata about the chart
- Dependencies on other charts
- Documentation

**Chart Structure:**
```
mychart/
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default configuration values
├── charts/             # Chart dependencies
├── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml
│   ├── service.yaml
│   └── _helpers.tpl
└── README.md           # Documentation
```

**Commands:**
```bash
# Create a new chart
helm create mychart

# Package a chart into a versioned archive
helm package mychart/

# Inspect a chart
helm show chart mychart
helm show values mychart
helm show all mychart
```

---

### 4. What is the difference between Helm 2 and Helm 3?

**Answer:**

| Feature | Helm 2 | Helm 3 |
|---------|--------|--------|
| **Tiller** | Required (server-side component) | Removed (more secure) |
| **Security** | Less secure due to Tiller | Improved with RBAC |
| **Release Names** | Global across namespaces | Scoped to namespaces |
| **Chart Repository** | Stable repo by default | No default repo |
| **CRD Handling** | In templates/ | In crds/ directory |
| **JSON Schema Validation** | Not supported | Supported for values.yaml |
| **Secrets** | ConfigMaps | Kubernetes Secrets |

**Commands:**
```bash
# Helm 3: Install chart (namespace scoped)
helm install myrelease mychart -n mynamespace

# Helm 3: Add repository
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

---

### 5. What is a Helm Release?

**Answer:** A Helm Release is a specific instance of a chart deployed to a Kubernetes cluster. Each release has:

- A unique name
- A revision number
- Configuration values
- Status (deployed, failed, pending, etc.)

**Key Points:**
- Multiple releases of the same chart can coexist
- Each release maintains its own history
- Releases can be upgraded, rolled back, or deleted

**Commands:**
```bash
# Install a release
helm install myrelease mychart

# Install with custom name
helm install my-app-release mychart

# List all releases
helm list
helm list --all-namespaces
helm list -n mynamespace

# Get release status
helm status myrelease

# Get release values
helm get values myrelease

# Get release manifest
helm get manifest myrelease
```

---

## Helm Commands

### 6. What are the essential Helm commands you use daily?

**Answer:** Here are the most commonly used Helm commands:

#### Installation Commands:
```bash
# Install a chart
helm install <release-name> <chart>

# Install with custom values
helm install myrelease mychart -f custom-values.yaml

# Install with inline values
helm install myrelease mychart --set key=value

# Install in specific namespace
helm install myrelease mychart -n mynamespace --create-namespace

# Dry run (test without installing)
helm install myrelease mychart --dry-run --debug
```

#### Upgrade Commands:
```bash
# Upgrade a release
helm upgrade myrelease mychart

# Upgrade with new values
helm upgrade myrelease mychart -f new-values.yaml

# Upgrade or install if doesn't exist
helm upgrade --install myrelease mychart

# Upgrade with reset values
helm upgrade myrelease mychart --reset-values
```

#### Rollback Commands:
```bash
# Rollback to previous version
helm rollback myrelease

# Rollback to specific revision
helm rollback myrelease 2

# List release history
helm history myrelease
```

#### Uninstall Commands:
```bash
# Uninstall a release
helm uninstall myrelease

# Uninstall and keep history
helm uninstall myrelease --keep-history
```

#### Query Commands:
```bash
# List releases
helm list
helm ls --all

# Search for charts
helm search repo wordpress
helm search hub wordpress

# Show chart information
helm show chart mychart
helm show values mychart
helm show readme mychart
```

---

### 7. How do you install a Helm chart with custom values?

**Answer:** There are multiple ways to override default values:

**Method 1: Using values file**
```bash
helm install myrelease mychart -f custom-values.yaml
helm install myrelease mychart -f values1.yaml -f values2.yaml
```

**Method 2: Using --set flag**
```bash
helm install myrelease mychart --set key=value
helm install myrelease mychart --set key1=value1,key2=value2
helm install myrelease mychart --set image.tag=v2.0.0
```

**Method 3: Combining both**
```bash
helm install myrelease mychart -f values.yaml --set image.tag=latest
```

**Priority Order (highest to lowest):**
1. Values specified with `--set`
2. Values from `-f` files (last file wins)
3. Default `values.yaml` in the chart

**Example custom-values.yaml:**
```yaml
replicaCount: 3
image:
  repository: nginx
  tag: "1.21.0"
service:
  type: LoadBalancer
  port: 80
```

---

### 8. How do you upgrade and rollback Helm releases?

**Answer:**

**Upgrade:**
```bash
# Basic upgrade
helm upgrade myrelease mychart

# Upgrade with new values
helm upgrade myrelease mychart -f new-values.yaml

# Upgrade with timeout
helm upgrade myrelease mychart --timeout 5m

# Upgrade and wait for completion
helm upgrade myrelease mychart --wait

# Upgrade with force (recreate resources)
helm upgrade myrelease mychart --force

# Atomic upgrade (rollback on failure)
helm upgrade myrelease mychart --atomic

# Install if not exists, upgrade if exists
helm upgrade --install myrelease mychart
```

**Rollback:**
```bash
# View release history
helm history myrelease

# Rollback to previous version
helm rollback myrelease

# Rollback to specific revision
helm rollback myrelease 3

# Rollback with wait
helm rollback myrelease 2 --wait

# Rollback with cleanup on fail
helm rollback myrelease 2 --cleanup-on-fail
```

**Check status:**
```bash
# Get current status
helm status myrelease

# Compare revisions
helm diff revision myrelease 1 2  # requires helm-diff plugin
```

---

### 9. What is the difference between `helm install` and `helm upgrade --install`?

**Answer:**

**`helm install`:**
- Creates a new release only
- Fails if release already exists
- Use when deploying for the first time

```bash
helm install myrelease mychart
# Error if 'myrelease' already exists
```

**`helm upgrade --install`:**
- Installs if release doesn't exist
- Upgrades if release already exists
- Idempotent operation (safe to run multiple times)
- Commonly used in CI/CD pipelines

```bash
helm upgrade --install myrelease mychart
# Creates new release OR upgrades existing one
```

**Use Cases:**
- **CI/CD pipelines**: Use `upgrade --install` for idempotency
- **Manual deployments**: Use `install` for first-time deployment
- **Automation**: Prefer `upgrade --install` to avoid conditional logic

---

### 10. How do you debug Helm deployments?

**Answer:** Multiple debugging techniques:

**1. Dry Run:**
```bash
# Preview what will be deployed
helm install myrelease mychart --dry-run --debug

# Test template rendering
helm template myrelease mychart --debug
```

**2. Linting:**
```bash
# Validate chart syntax
helm lint mychart/

# Lint with values
helm lint mychart/ -f custom-values.yaml
```

**3. Get Rendered Manifests:**
```bash
# Get manifest for installed release
helm get manifest myrelease

# Render templates locally
helm template myrelease mychart
```

**4. Check Status and Logs:**
```bash
# Get release status
helm status myrelease

# Get release history
helm history myrelease

# Get all release information
helm get all myrelease

# Get release values
helm get values myrelease
```

**5. Kubernetes Debugging:**
```bash
# Check pods after deployment
kubectl get pods -n mynamespace

# Check pod logs
kubectl logs <pod-name>

# Describe resources
kubectl describe deployment <deployment-name>
```

**6. Enable Debug Mode:**
```bash
# Install with debug output
helm install myrelease mychart --debug --dry-run > output.yaml

# Set log level
helm install myrelease mychart --debug --v=5
```

---

## Chart Structure

### 11. What files are included in a Helm Chart and their purposes?

**Answer:**

**Essential Files:**

**1. Chart.yaml** - Chart metadata
```yaml
apiVersion: v2
name: mychart
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
keywords:
  - web
  - application
maintainers:
  - name: John Doe
    email: john@example.com
dependencies:
  - name: postgresql
    version: "10.x.x"
    repository: https://charts.bitnami.com/bitnami
```

**2. values.yaml** - Default configuration values
```yaml
replicaCount: 1
image:
  repository: nginx
  tag: "1.16.0"
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
```

**3. templates/** - Kubernetes manifest templates
```
templates/
├── deployment.yaml
├── service.yaml
├── ingress.yaml
├── configmap.yaml
├── _helpers.tpl      # Template helpers
├── NOTES.txt         # Post-installation notes
└── tests/
    └── test-connection.yaml
```

**4. charts/** - Chart dependencies

**5. .helmignore** - Files to ignore when packaging
```
.git/
.gitignore
*.tmp
.DS_Store
```

**Optional Files:**
- **README.md**: Documentation
- **LICENSE**: License information
- **values.schema.json**: JSON Schema for values validation
- **crds/**: Custom Resource Definitions

**Commands:**
```bash
# Create chart structure
helm create mychart

# Verify chart structure
helm lint mychart/

# Package chart
helm package mychart/
```

---

### 12. What is the purpose of Chart.yaml?

**Answer:** `Chart.yaml` contains metadata about the Helm chart.

**Key Fields:**

```yaml
# Required fields
apiVersion: v2              # Chart API version (v2 for Helm 3)
name: mychart              # Chart name
version: 1.0.0             # Chart version (SemVer)

# Recommended fields
description: A sample Helm chart
type: application          # application or library
appVersion: "1.16.0"      # Version of app being deployed

# Optional fields
keywords:
  - nginx
  - web
home: https://example.com
sources:
  - https://github.com/user/repo
maintainers:
  - name: John Doe
    email: john@example.com
    url: https://johndoe.com
icon: https://example.com/icon.png
deprecated: false
annotations:
  category: web

# Dependencies
dependencies:
  - name: redis
    version: "~15.x"
    repository: https://charts.bitnami.com/bitnami
    condition: redis.enabled
    tags:
      - database
    import-values:
      - child: config
        parent: redis-config
```

**Chart Types:**
- **application**: Default, deployable chart
- **library**: Provides utilities for other charts (not deployable)

**Commands:**
```bash
# Show chart metadata
helm show chart mychart

# Validate Chart.yaml
helm lint mychart/
```

---

### 13. What is values.yaml and how does it work?

**Answer:** `values.yaml` provides default configuration values for chart templates.

**Example values.yaml:**
```yaml
# Replica configuration
replicaCount: 2

# Image configuration
image:
  repository: nginx
  tag: "1.21.0"
  pullPolicy: IfNotPresent

# Service configuration
service:
  type: ClusterIP
  port: 80
  targetPort: 8080
  annotations: {}

# Ingress configuration
ingress:
  enabled: false
  className: nginx
  hosts:
    - host: example.com
      paths:
        - path: /
          pathType: Prefix

# Resources
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi

# Environment variables
env:
  - name: APP_ENV
    value: production
  - name: LOG_LEVEL
    value: info

# Config
config:
  database:
    host: postgres.default.svc.cluster.local
    port: 5432
```

**Using in Templates:**
```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
```

**Override Values:**
```bash
# Using custom file
helm install myrelease mychart -f custom-values.yaml

# Using --set
helm install myrelease mychart --set replicaCount=3

# Multiple overrides
helm install myrelease mychart \
  -f values-prod.yaml \
  --set image.tag=v2.0.0 \
  --set ingress.enabled=true
```

---

### 14. What are Helm template helpers (_helpers.tpl)?

**Answer:** `_helpers.tpl` contains reusable template definitions that can be shared across multiple templates.

**Example _helpers.tpl:**
```yaml
{{/*
Expand the name of the chart.
*/}}
{{- define "mychart.name" -}}
{{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" }}
{{- end }}

{{/*
Create a fully qualified app name.
*/}}
{{- define "mychart.fullname" -}}
{{- if .Values.fullnameOverride }}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- $name := default .Chart.Name .Values.nameOverride }}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}

{{/*
Common labels
*/}}
{{- define "mychart.labels" -}}
helm.sh/chart: {{ include "mychart.chart" . }}
{{ include "mychart.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}

{{/*
Selector labels
*/}}
{{- define "mychart.selectorLabels" -}}
app.kubernetes.io/name: {{ include "mychart.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}

{{/*
Create chart name and version as used by the chart label.
*/}}
{{- define "mychart.chart" -}}
{{- printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" }}
{{- end }}

{{/*
Return the appropriate apiVersion for deployment.
*/}}
{{- define "mychart.deployment.apiVersion" -}}
{{- if semverCompare ">=1.9-0" .Capabilities.KubeVersion.GitVersion -}}
apps/v1
{{- else -}}
apps/v1beta2
{{- end -}}
{{- end -}}
```

**Using Helpers in Templates:**
```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
spec:
  selector:
    matchLabels:
      {{- include "mychart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "mychart.selectorLabels" . | nindent 8 }}
```

**Benefits:**
- Code reusability
- Consistency across templates
- Easier maintenance
- Standard naming conventions

---

## Values and Templates

### 15. How do you use conditionals in Helm templates?

**Answer:** Helm uses Go template language for conditionals.

**Basic If/Else:**
```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ .Release.Name }}-ingress
spec:
  # ingress spec
{{- end }}
```

**If/Else If/Else:**
```yaml
apiVersion: v1
kind: Service
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      {{- if eq .Values.service.type "NodePort" }}
      nodePort: {{ .Values.service.nodePort }}
      {{- else if eq .Values.service.type "LoadBalancer" }}
      # LoadBalancer specific config
      {{- else }}
      # ClusterIP default
      {{- end }}
```

**Comparison Operators:**
```yaml
# Equality
{{- if eq .Values.env "production" }}
replicas: 3
{{- else }}
replicas: 1
{{- end }}

# Not equal
{{- if ne .Values.env "development" }}
  # production config
{{- end }}

# And
{{- if and .Values.ingress.enabled .Values.tls.enabled }}
  tls: true
{{- end }}

# Or
{{- if or .Values.useSSL .Values.useTLS }}
  secure: true
{{- end }}

# Not
{{- if not .Values.debug }}
  logLevel: info
{{- end }}
```

**Checking Existence:**
```yaml
{{- if .Values.resources }}
resources:
  {{- toYaml .Values.resources | nindent 2 }}
{{- end }}

# Check for empty
{{- if not (empty .Values.annotations) }}
annotations:
  {{- toYaml .Values.annotations | nindent 2 }}
{{- end }}
```

**With (scope change):**
```yaml
{{- with .Values.database }}
database:
  host: {{ .host }}
  port: {{ .port }}
  name: {{ .name }}
{{- end }}
```

---

### 16. How do you use loops in Helm templates?

**Answer:** Use `range` for iterating over lists and maps.

**Range over List:**
```yaml
# values.yaml
environments:
  - dev
  - staging
  - production

# template
{{- range .Values.environments }}
- name: {{ . }}
{{- end }}

# Output:
# - name: dev
# - name: staging
# - name: production
```

**Range with Index:**
```yaml
{{- range $index, $env := .Values.environments }}
- name: env-{{ $index }}
  value: {{ $env }}
{{- end }}
```

**Range over Map:**
```yaml
# values.yaml
labels:
  app: myapp
  version: v1.0
  tier: backend

# template
metadata:
  labels:
    {{- range $key, $value := .Values.labels }}
    {{ $key }}: {{ $value }}
    {{- end }}
```

**Nested Loops:**
```yaml
# values.yaml
services:
  - name: frontend
    ports:
      - 80
      - 443
  - name: backend
    ports:
      - 8080
      - 8443

# template
{{- range .Values.services }}
---
apiVersion: v1
kind: Service
metadata:
  name: {{ .name }}
spec:
  ports:
  {{- range .ports }}
  - port: {{ . }}
  {{- end }}
{{- end }}
```

**Environment Variables:**
```yaml
# values.yaml
env:
  - name: APP_ENV
    value: production
  - name: LOG_LEVEL
    value: info

# template
env:
{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value | quote }}
{{- end }}
```

**Range with Conditional:**
```yaml
{{- range .Values.services }}
{{- if .enabled }}
- name: {{ .name }}
  port: {{ .port }}
{{- end }}
{{- end }}
```

---

### 17. What are Helm template functions and how do you use them?

**Answer:** Helm provides numerous built-in functions for template manipulation.

**String Functions:**
```yaml
# Upper/Lower case
{{ .Values.name | upper }}
{{ .Values.name | lower }}

# Trim
{{ .Values.name | trim }}
{{ .Values.name | trimPrefix "pre-" }}
{{ .Values.name | trimSuffix "-suf" }}

# Replace
{{ .Values.name | replace "-" "_" }}

# Quote
{{ .Values.password | quote }}
{{ .Values.value | squote }}

# Default value
{{ .Values.name | default "default-name" }}

# Truncate
{{ .Values.name | trunc 63 }}

# Print format
{{ printf "%s-%s" .Release.Name .Chart.Name }}
```

**Type Conversion:**
```yaml
# To string
{{ .Values.port | toString }}

# To integer
{{ .Values.replica | toInt }}

# To JSON
{{ .Values.config | toJson }}

# To YAML
{{ .Values.config | toYaml }}

# To Base64
{{ .Values.secret | b64enc }}
{{ .Values.encoded | b64dec }}
```

**List Functions:**
```yaml
# Join
{{ .Values.list | join "," }}

# Sort
{{ .Values.list | sortAlpha }}

# Unique
{{ .Values.list | uniq }}

# Has element
{{ has "item" .Values.list }}

# First/Last
{{ first .Values.list }}
{{ last .Values.list }}
```

**Dict/Map Functions:**
```yaml
# Create dict
{{ dict "key1" "value1" "key2" "value2" }}

# Get value
{{ pluck "key" .Values.dict | first }}

# Merge
{{ merge .Values.dict1 .Values.dict2 }}

# Keys
{{ keys .Values.dict }}

# Values
{{ values .Values.dict }}
```

**Date Functions:**
```yaml
# Current date
{{ now | date "2006-01-02" }}

# Format date
{{ .Values.date | date "2006-01-02T15:04:05Z" }}
```

**Kubernetes Functions:**
```yaml
# Lookup (query K8s resources)
{{ lookup "v1" "Secret" "namespace" "secret-name" }}

# Capability check
{{ if .Capabilities.APIVersions.Has "networking.k8s.io/v1/Ingress" }}
  # Use v1 Ingress
{{ end }}
```

**Crypto Functions:**
```yaml
# Generate random
{{ randAlphaNum 10 }}
{{ uuidv4 }}

# Hash
{{ .Values.password | sha256sum }}
```

---

### 18. How do you handle secrets in Helm?

**Answer:** Multiple approaches for managing secrets:

**Method 1: Direct in values.yaml (Not Recommended)**
```yaml
# values.yaml
database:
  password: "mypassword"  # NOT SECURE!
```

**Method 2: Separate values file (Better)**
```yaml
# values-secrets.yaml (add to .gitignore)
database:
  password: "secure-password"
```
```bash
helm install myrelease mychart -f values.yaml -f values-secrets.yaml
```

**Method 3: Using --set (CI/CD)**
```bash
helm install myrelease mychart \
  --set database.password=${DB_PASSWORD}
```

**Method 4: External Secrets Operator**
```yaml
# values.yaml
externalSecrets:
  enabled: true
  backend: awsSecretsManager
  key: prod/database/password
```

**Method 5: Sealed Secrets**
```bash
# Install sealed-secrets controller
kubectl apply -f sealed-secrets-controller.yaml

# Create sealed secret
kubeseal --format yaml < secret.yaml > sealed-secret.yaml
```

**Method 6: Helm Secrets Plugin**
```bash
# Install plugin
helm plugin install https://github.com/jkroepke/helm-secrets

# Encrypt values
helm secrets encrypt values-secret.yaml

# Install with encrypted values
helm secrets install myrelease mychart -f values-secret.yaml
```

**Template Example:**
```yaml
# templates/secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: {{ include "mychart.fullname" . }}-secret
type: Opaque
data:
  password: {{ .Values.database.password | b64enc | quote }}
```

**Best Practices:**
- Never commit secrets to version control
- Use external secret management tools
- Rotate secrets regularly
- Use RBAC to restrict access
- Encrypt secrets at rest

---

## Chart Repositories

### 19. How do you work with Helm repositories?

**Answer:** Helm repositories store and share charts.

**Add Repository:**
```bash
# Add official stable repo
helm repo add stable https://charts.helm.sh/stable

# Add Bitnami repo
helm repo add bitnami https://charts.bitnami.com/bitnami

# Add custom repo
helm repo add myrepo https://my-chart-repo.example.com
```

**List Repositories:**
```bash
# List all configured repos
helm repo list
```

**Update Repositories:**
```bash
# Update all repos
helm repo update

# Update specific repo
helm repo update bitnami
```

**Remove Repository:**
```bash
helm repo remove stable
```

**Search Charts:**
```bash
# Search in added repos
helm search repo nginx

# Search in Artifact Hub
helm search hub wordpress

# Search with versions
helm search repo nginx --versions

# Search with regex
helm search repo "^nginx"
```

**Show Chart Info:**
```bash
# Show chart details
helm show chart bitnami/nginx

# Show default values
helm show values bitnami/nginx

# Show all info
helm show all bitnami/nginx

# Show readme
helm show readme bitnami/nginx
```

**Install from Repository:**
```bash
# Install from repo
helm install myrelease bitnami/nginx

# Install specific version
helm install myrelease bitnami/nginx --version 13.2.0

# Pull chart locally
helm pull bitnami/nginx
helm pull bitnami/nginx --untar
helm pull bitnami/nginx --version 13.2.0
```

---

### 20. How do you create and host your own Helm repository?

**Answer:** Multiple ways to host Helm repositories:

**Method 1: GitHub Pages**

1. Create `index.yaml`:
```bash
# Package charts
helm package mychart/

# Generate index
helm repo index . --url https://username.github.io/helm-charts/
```

2. Push to GitHub:
```bash
git add .
git commit -m "Add Helm charts"
git push origin main
```

3. Enable GitHub Pages in repo settings

4. Add repo:
```bash
helm repo add myrepo https://username.github.io/helm-charts/
```

**Method 2: ChartMuseum**
```bash
# Install ChartMuseum
docker run -d \
  -p 8080:8080 \
  -v $(pwd)/charts:/charts \
  ghcr.io/helm/chartmuseum:latest

# Upload chart
curl --data-binary "@mychart-0.1.0.tgz" \
  http://localhost:8080/api/charts

# Add repo
helm repo add local http://localhost:8080
```

**Method 3: Harbor Registry**
```bash
# Push chart to Harbor
helm registry login harbor.example.com
helm push mychart-0.1.0.tgz oci://harbor.example.com/library

# Install from Harbor
helm install myrelease oci://harbor.example.com/library/mychart --version 0.1.0
```

**Method 4: AWS S3**
```bash
# Install helm-s3 plugin
helm plugin install https://github.com/hypnoglow/helm-s3.git

# Initialize repo
helm s3 init s3://my-helm-charts/

# Add repo
helm repo add my-s3-repo s3://my-helm-charts/

# Push chart
helm s3 push mychart-0.1.0.tgz my-s3-repo
```

**Update Repository Index:**
```bash
# Regenerate index
helm repo index . --url https://my-repo.example.com --merge index.yaml
```

---

### 21. What is OCI registry support in Helm 3?

**Answer:** Helm 3.8+ supports OCI (Open Container Initiative) registries for storing charts.

**Benefits:**
- Use container registries (Docker Hub, Harbor, ACR, GCR, ECR)
- Unified storage for images and charts
- Better security and access control
- Standard registry authentication

**Commands:**

**Enable OCI Support:**
```bash
export HELM_EXPERIMENTAL_OCI=1  # For Helm < 3.8
# OCI is stable in Helm 3.8+
```

**Login to Registry:**
```bash
# Docker Hub
helm registry
