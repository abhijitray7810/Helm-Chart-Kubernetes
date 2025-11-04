# 1. How do you create a conditional block in a Helm template?
## Answer:
Using the if/else construct.
```bash
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-svc
spec:
  type: {{ .Values.service.type }}
  {{- if eq .Values.service.type "LoadBalancer" }}
  loadBalancerIP: {{ .Values.service.loadBalancerIP }}
  {{- end }}
```
In this example, the loadBalancerIP field is only included if the service type is set to "LoadBalancer".
# 2. How do you iterate over a list or map in a template?
## Answer:
### Using the range function.
#### List Example:
```bash
env:
{{- range .Values.envVars }}
  - name: {{ .name }}
    value: {{ .value | quote }}
{{- end }}
```
#### Map Example:
```bash
env:
{{- range $key, $value := .Values.envMap }}
  - name: {{ $key }}
    value: {{ $value | quote }}
{{- end }}
```
# 3.  What are named templates (define and template) and how are they used?
## Answer:
Named templates (defined with {{ define "mytemplate" }} ... {{ end }}) are reusable template snippets. They are stored in files like templates/_helpers.tpl. You include them in other templates using {{ include "mytemplate" . }}.

Why include is preferred over template: The include function allows you to pass the current context (.), whereas the template function does not
# 4. What is the tpl function and when would you use it?
## Answer:
### In values.yaml:

```yaml
annotation: "The release is: {{ .Release.Name }}"
```
### In the template:

```yaml
metadata:
  annotations:
    description: {{ tpl .Values.annotation . | quote }}
# Renders to: "The release is: my-release"
```
Without tpl, {{ .Release.Name }} would be treated as a literal string.
