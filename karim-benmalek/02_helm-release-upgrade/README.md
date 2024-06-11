# Part 1: Explore what we have deployed

**Step 1:** Check the deployed release
```shell
helm list -n dev-ns

# NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                APP VERSION
# mock-app        dev-ns          1               2024-06-11 14:57:00.902108755 +0000 UTC deployed        mock-app-2.1.0       1.16.0
```

```shell
helm list -n dev-ns -oyaml

# - app_version: 1.16.0
#   chart: mock-app-2.1.0
#   name: mock-app
#   namespace: dev-ns
#   revision: "1"
#   status: deployed
#   updated: 2024-06-11 14:57:00.902108755 +0000 UTC
```

We have a single release named **mock-app** deployed in the target namespace.

**Step 2:** Check the version of the deployed chart

```shell
export CHART_VERSION=$(helm list -n dev-ns -oyaml | yq e '.[0].chart | split("-") | .[-1]' -)
```
```shell
echo "Deployed Chart Version is: $CHART_VERSION"

# Deployed Chart Version is: 2.1.0
```

**Step 3:** Verify the deployed resources
```shell
kubectl get all -n dev-ns

# NAME                                       READY   STATUS    RESTARTS   AGE
# pod/mock-app-deployment-5c7679799c-rcx4h   1/1     Running   0          5m16s

# NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
# service/mock-app-service   ClusterIP   10.111.30.207   <none>        5000/TCP   5m16s

# NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/mock-app-deployment   1/1     1            1           5m16s

# NAME                                             DESIRED   CURRENT   READY   AGE
# replicaset.apps/mock-app-deployment-5c7679799c   1         1         1       5m16s

# NAME                                               REFERENCE                        TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
# horizontalpodautoscaler.autoscaling/mock-app-hpa   Deployment/mock-app-deployment   cpu: <unknown>/50%   1         3         1          5m16s
```

**Step 4:** Verify the content of the deployed container
```shell
export PORT=5000
export SERVICE_IP=$(kubectl get svc -n dev-ns -l app=mock-app -o jsonpath='{.items[0].spec.clusterIP}')
```
```shell
curl -s http://${SERVICE_IP}:${PORT} -w "\n"
Hello Killercoda Folks! You recieved this message: Your Chart Version is: 2.1.0
```


# Part 2: Explore the latest version of the mock-app chart

**Step 1:** Check helm repositories added to the scenario
```shell
helm repo list

# NAME            URL                                                  
# mock-app-repo   https://benmalekarim.github.io/helm-scenarios-charts/
```
We notice that we have a single repo named: **mock-app-repo**

**Step 2:** Search helm charts of the helm repository
```shell
helm search repo mock-app-repo

# NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                
# mock-app-repo/mock-app                  2.2.0           1.16.0          A Helm chart for Kubernetes
# mock-app-repo/mock-app-blue-green       1.0.0           1.16.0          A Helm chart for Kubernetes
# mock-app-repo/mock-app-canary           1.0.0           1.16.0          A Helm chart for Kubernetes
```

**Step 3:** Check the latest version of the mock-app chart
```shell
helm search repo mock-app-repo -oyaml

# - app_version: 1.16.0
#   description: A Helm chart for Kubernetes
#   name: mock-app-repo/mock-app
#   version: 2.2.0
# - app_version: 1.16.0
#   description: A Helm chart for Kubernetes
#   name: mock-app-repo/mock-app-blue-green
#   version: 1.0.0
# - app_version: 1.16.0
#   description: A Helm chart for Kubernetes
#   name: mock-app-repo/mock-app-canary
#   version: 1.0.0
```
```shell
export LATEST_CHART_VERSION=$(helm search repo mock-app-repo -oyaml | yq e '.[0].version' -)
```
```shell
echo "Latest mock-app chart version is: $LATEST_CHART_VERSION"

# Latest mock-app chart version is: 2.2.0
```

We have observed that the latest deployed mock-app release (version 2.1.0) doesn't correspond to the most recent chart version (version 2.2.0).


# Part 3: Upgrade the deployed release in order to use the latest one

**Step 1:** Upgrade helm release
```shell
helm upgrade --namespace dev-ns mock-app mock-app-repo/mock-app

# Release "mock-app" has been upgraded. Happy Helming!
# NAME: mock-app
# LAST DEPLOYED: Tue Jun 11 15:09:19 2024
# NAMESPACE: dev-ns
# STATUS: deployed
# REVISION: 2
# TEST SUITE: None
```

**Step 2:** Check the upgraded release
```shell
helm list -n dev-ns

# NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                APP VERSION
# mock-app        dev-ns          2               2024-06-11 15:09:19.59942952 +0000 UTC  deployed        mock-app-2.2.0       1.16.0
```

As we can see, the current deployed release is in version 2.2.0 which match with the latest chart version. The release is upgraded successfully.
