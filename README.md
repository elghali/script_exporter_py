# script_exporter_py

This is a prometheus exporter that collects metrics using a python script. The script's main function is to expose the number of files in a directory specified by `dir_path` parameter.
Usage
---
```
git clone https://github.com/elghali/script_exporter_py.git
cd script_exporter_py
python3 script-exporter-py [dir_path]

[Try it](https://loclhost:9469)

```

## Docker

`docker run -it -v "[dir_path]:[dir_path]" -p 9469:9469 -e dir_path="[dir_path]" -d elghali/script_exporter_py:1.0`

## K8s
To try the exporter on K8s we can use the below sample deployment or visit [examples](https://github.com/elghali/script_exporter_py/examples) for more examples.
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: script-exporter-py
  namespace: script-exporter
  labels:
    app.kubernetes.io/name: script-exporter-py
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: script-exporter-py
  template:
    metadata:
      labels:
        app.kubernetes.io/name: script-exporter-py
    spec:
      containers:
        - name: script-exporter-py
          image: elghali/script_exporter_py:1.0
          env:
          - name: dir_path
            value: /ni/path_to_dir
          ports:
            - name: http
              containerPort: 9469
              protocol: TCP
          livenessProbe:
            failureThreshold: 3
            httpGet:
              path: /
              port: 9469
              scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 10
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /
              port: 9469
              scheme: HTTP
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 10
          volumeMounts:
            - mountPath: /ni
              name: ni-etl-root
      volumes:
        - name: ni-etl-root
          nfs:
            path: /script-exporter/ni
            server: 10.0.1.12
            

```