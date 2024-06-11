**Step 1:** Install helm by running the script
```shell
./install-helm.sh

# Downloading https://get.helm.sh/helm-v3.15.1-linux-amd64.tar.gz
# Verifying checksum... Done.
# Preparing to install helm into /usr/local/bin
# helm installed into /usr/local/bin/helm
```

**Step 2:** Verify Helm version
```shell
helm version

# version.BuildInfo{Version:"v3.15.1", GitCommit:"e211f2aa62992bd72586b395de50979e31231829", GitTreeState:"clean", GoVersion:"go1.22.3"}
```

**Step 3:** Deploy a release using helm
```shell
helm repo add mock-app-repo https://benmalekarim.github.io/helm-scenarios-charts/

# "mock-app-repo" has been added to your repositories
```

```shell
helm install mock-app mock-app-repo/mock-app --version 2.1.0

# NAME: mock-app
# LAST DEPLOYED: Tue Jun 11 14:51:54 2024
# NAMESPACE: default
# STATUS: deployed
# REVISION: 1
# TEST SUITE: None
```

**Step 4:** Verify the deployed resources
```shell
kubectl get all

# NAME                                       READY   STATUS    RESTARTS   AGE
# pod/mock-app-deployment-5c7679799c-8kr7c   1/1     Running   0          26s

# NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
# service/kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP    3d8h
# service/mock-app-service   ClusterIP   10.107.203.254   <none>        5000/TCP   27s

# NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/mock-app-deployment   1/1     1            1           27s

# NAME                                             DESIRED   CURRENT   READY   AGE
# replicaset.apps/mock-app-deployment-5c7679799c   1         1         1       26s

# NAME                                               REFERENCE                        TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
# horizontalpodautoscaler.autoscaling/mock-app-hpa   Deployment/mock-app-deployment   cpu: <unknown>/50%   1         3         1          27s
```

**Step 5:** Verify Deployment
```shell
kubectl port-forward --address 0.0.0.0 deployment/mock-app-deployment 5000:5000

# Forwarding from 0.0.0.0:5000 -> 5000
# Handling connection for 5000
# Handling connection for 5000
```

![Alt text](./pics/01_app-testing.png)
