# Python 3.9 Runtime with glibc support.

This is a Kyma Serverless compatible Python runtime, based on `debian:bullseye-slim` base image to provide support for glibc.

### Image

`melsayed/python93-runtime-glibc:ff7c9df4`

### Usage

- Pull the default Python39 Runtime ConfigMap from Kyma into a file
```bash
kubectl get configmap -n default dockerfile-python-39 -o json | jq -r ".data.Dockerfile" > dockerfile-python-39-glibc.yaml
```

- Edit the file and replace the the base image with the `python93-runtime-glibc` image

```git
-       FROM eu.gcr.io/kyma-project/function-runtime-python39:PR-13398
+       FROM melsayed/python93-runtime-glibc:ff7c9df4
        USER root
        ENV KUBELESS_INSTALL_VOLUME=/kubeless
```

- Create a new ConfigMap with the updated file
```bash
kubectl create configmap dockerfile-python-39-glibc --from-file=Dockerfile=dockerfile-python-39-glibc.yaml
```

- Create a function that will use the new ConfigMap
```bash
cat <<EOF |  kubectl apply -f -
---
apiVersion: serverless.kyma-project.io/v1alpha1
kind: Function
metadata:
  creationTimestamp: null
  name: pyhello
  namespace: default
spec:
  runtime: python39
  baseDockerFile: dockerfile-python-39-glibc
  source: |
    def main(event, context):
        return "glibc FTW"
EOF
```


**Note:** It's also possible to use this runtime with git functions.