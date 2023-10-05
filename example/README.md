```console
$ helm version
version.BuildInfo{Version:"v3.12.3", GitCommit:"3a31588ad33fe3b89af5a2a54ee1d25bfe6eaa5e", GitTreeState:"clean", GoVersion:"go1.20.7"}

$ helm create example
Creating example

$ rm -r example/charts/ example/templates/*

$ echo '{{ include "helmet.app" . }}' > example/templates/app.yaml

$ yq e '. += ({"dependencies": [{"name": "helmet", "version": "~0.9.1", "repository": "https://companyinfo.github.io/helm-charts", "import-values": ["defaults"]}]})' < example/Chart.yaml | sponge example/Chart.yaml

$ helm dependency build example
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "helmet" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 1 charts
Downloading helmet from repo https://companyinfo.github.io/helm-charts
Deleting outdated charts

$ helm template example
---
# Source: example/templates/app.yaml
apiVersion: v1
kind: Service
metadata:
  name: release-name-example
  namespace: "default"
  labels:
    app.kubernetes.io/name: example
    helm.sh/chart: example-0.1.0
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/managed-by: Helm
spec:
  type: ClusterIP
  sessionAffinity: None
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
  selector:
    app.kubernetes.io/name: example
    app.kubernetes.io/instance: release-name
---
# Source: example/templates/app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: release-name-example
  namespace: "default"
  labels:
    app.kubernetes.io/name: example
    helm.sh/chart: example-0.1.0
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: example
      app.kubernetes.io/instance: release-name
  strategy:
    type: RollingUpdate
  template:
    metadata:
      annotations:
        helm.sh/revision: "1"
      labels:
        app.kubernetes.io/name: example
        helm.sh/chart: example-0.1.0
        app.kubernetes.io/instance: release-name
        app.kubernetes.io/managed-by: Helm
    spec:
      restartPolicy: Always
      containers:
        - name: example
          image: docker.io/hello-world:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 8080
              name: http
              protocol: TCP
          resources:
            limits: {}
            requests: {}
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: example
                    app.kubernetes.io/instance: release-name
                topologyKey: kubernetes.io/hostname
              weight: 1
      serviceAccountName: default

$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
Helm v3.13.0 is available. Changing from version v3.12.3.
Downloading https://get.helm.sh/helm-v3.13.0-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /opt/tooling/helm/bin
helm installed into /opt/tooling/helm/bin/helm

$ helm version
version.BuildInfo{Version:"v3.13.0", GitCommit:"825e86f6a7a38cef1112bfa606e4127a706749b1", GitTreeState:"clean", GoVersion:"go1.20.8"}

$ helm dependency build example
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "helmet" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 1 charts
Downloading helmet from repo https://companyinfo.github.io/helm-charts
Deleting outdated charts

$ helm template example
---
# Source: example/templates/app.yaml
apiVersion: v1
kind: Service
metadata:
  name: release-name-example
  namespace: "default"
  labels:
    app.kubernetes.io/name: example
    helm.sh/chart: example-0.1.0
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/managed-by: Helm
spec:
  type: ClusterIP
  sessionAffinity: None
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
  selector:
    app.kubernetes.io/name: example
    app.kubernetes.io/instance: release-name
```
