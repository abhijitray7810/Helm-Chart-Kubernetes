# 1. Describe the standard directory structure of a Helm Chart.
## Answer:
```bash
mychart/
├── Chart.yaml          # Metadata about the chart (name, version, etc.)
├── values.yaml         # Default configuration values for the chart
├── charts/             # Directory containing sub-charts (dependencies)
├── templates/          # Directory of template files that become K8s manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   └── _helpers.tpl    # Helper templates/partials
└── templates/NOTES.txt # Optional: Instructions displayed after install
```
# 2. Explain the structure of a Helm chart
## Answer:
**Chart.yaml:** Metadata like name, version, description.

**values.yaml:** Default configuration values.

**templates/:** YAML templates for K8s resources (e.g., deployment.yaml).

**charts/:** Subcharts for dependencies.

**requirements.yaml** (Helm 2) or **Chart.yaml** dependencies (Helm 3+): For managing child charts.
# 3. What is the purpose of the **Chart.yaml** file?


