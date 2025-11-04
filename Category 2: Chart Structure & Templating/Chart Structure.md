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
## Answer:
The Chart.yaml file is the main file containing the metadata about the chart. Essential fields include:

**apiVersion:** Chart API version (e.g., v2 for Helm 3).

**name:** The name of the chart.

**version:** A SemVer 2 version (e.g., 1.2.3).

**appVersion:** The version of the application contained within (e.g., 2.1.0).

**description:** A single-sentence description.

**type:** application or library.

**dependencies:** A list of chart dependencies.

# 4. What is the purpose of the values.yaml file?
## Answer:
The values.yaml file provides the default configuration values for the chart. It's the primary mechanism for customizing a chart without modifying its templates. Users can override these values via a custom values file or the --set flag during helm install or helm upgrade.

# 5. Explain how Helm templates work. What is the syntax?
## Answer:
Helm uses the Go template language, extended by the Sprig library of utility functions.

**Delimiters:** {{ ... }} enclose template actions.

**Accessing Values:** Values from values.yaml are accessed using the .Values object. For example, .Values.image.repository.

**Control Structures:** Includes loops (range), conditionals (if/else), and with statements.

**Built-in Objects:** .Values, .Chart, .Release, .Capabilities, and .Template provide context.

**Pipelines (|):** Used to chain functions, e.g., {{ .Values.name | upper | quote }}.
