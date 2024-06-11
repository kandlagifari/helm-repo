# Part 1: Explore what we have deployed

**Step 1:** Check the deployed release
```shell
helm list -n dev-ns

# NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                APP VERSION
# mock-app        dev-ns          1               2024-06-11 15:17:50.578783769 +0000 UTC deployed        mock-app-2.0.0       1.16.0
```

We have a single release named **mock-app** deployed in the target namespace.

**Step 2:** Verify the deployed resources
```shell
kubectl get all -n dev-ns

# NAME                                      READY   STATUS    RESTARTS   AGE
# pod/mock-app-deployment-6d55db676-thg5k   1/1     Running   0          87s

# NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
# service/mock-app-service   ClusterIP   10.102.188.3   <none>        5000/TCP   88s

# NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/mock-app-deployment   1/1     1            1           87s

# NAME                                            DESIRED   CURRENT   READY   AGE
# replicaset.apps/mock-app-deployment-6d55db676   1         1         1       87s

# NAME                                               REFERENCE                        TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
# horizontalpodautoscaler.autoscaling/mock-app-hpa   Deployment/mock-app-deployment   cpu: <unknown>/50%   1         3         1          87s
```

**Step 3:** Verify the content of the deployed container
```shell
export PORT=5000
export SERVICE_IP=$(kubectl get svc -n dev-ns -l app=mock-app -o jsonpath='{.items[0].spec.clusterIP}')
```
```shell
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

# Hello Killercoda Folks! You recieved this message: This application is not needed. Uninstall it please !
```

As evident from the displayed message, we no longer require the application and plan to remove it.


# Part 2: Uninstall the mock-app application release

**Step 1:** Uninstall mock-app release
```shell
helm uninstall mock-app -n dev-ns

# release "mock-app" uninstalled
```

**Step 2:** Check the deployed release
```shell
helm list -n dev-ns

# NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
```

No release is currently deployed in the target namespace.

**Step 3:** Verify the deployed resources
```shell
kubectl get all -n dev-ns

# No resources found in dev-ns namespace.
```

No resources are deployed in target namespace. The application release is uninstalled successfully.
