# Part 1: Explore what we have deployed

**Step 1:** Check the deployed release
```shell
helm list -n dev-ns

# NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                APP VERSION
# mock-app        dev-ns          2               2024-06-11 15:11:34.445536719 +0000 UTC deployed        mock-app-2.0.0       1.16.0
```

We have a single release named **mock-app** deployed in the target namespace.

**Step 2:** Verify the deployed resources
```shell
kubectl get all -n dev-ns

# NAME                                       READY   STATUS    RESTARTS   AGE
# pod/mock-app-deployment-68976ddc68-p8vcw   1/1     Running   0          100s

# NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
# service/mock-app-service   ClusterIP   10.104.33.30   <none>        5000/TCP   102s

# NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/mock-app-deployment   1/1     1            1           102s

# NAME                                             DESIRED   CURRENT   READY   AGE
# replicaset.apps/mock-app-deployment-6459f7c684   0         0         0       102s
# replicaset.apps/mock-app-deployment-68976ddc68   1         1         1       100s

# NAME                                               REFERENCE                        TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
# horizontalpodautoscaler.autoscaling/mock-app-hpa   Deployment/mock-app-deployment   cpu: <unknown>/50%   1         3         1          102s
```

**Step 3:** Verify the content of the deployed container
```shell
export PORT=5000
export SERVICE_IP=$(kubectl get svc -n dev-ns -l app=mock-app -o jsonpath='{.items[0].spec.clusterIP}')
```
```shell
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

# Hello Killercoda Folks! You recieved this message: New version SOMETHING WENT WRONG !
```

As evident from the displayed message, an issue has arisen within the business logic of the newly deployed version of the application.


# Part 2: Rollback to the previous version of the mock-app application

**Step 1:** Check the history of mock-app release
```shell
helm history mock-app -n dev-ns

# REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
# 1               Tue Jun 11 15:11:33 2024        superseded      mock-app-1.9.0  1.16.0          Install complete
# 2               Tue Jun 11 15:11:34 2024        deployed        mock-app-2.0.0  1.16.0          Upgrade complete
```

We have identified that two revisions of the mock-app application were deployed, with the current chart version being 2.0.0.

Given the issue we are encountering, we aim to rollback to the previous version until we resolve the issues with the current version.

**Step 2:** Rollback to previous version
```shell
helm rollback -n dev-ns mock-app

# Rollback was a success! Happy Helming!
```

```shell
helm history mock-app -n dev-ns

# REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
# 1               Tue Jun 11 15:11:33 2024        superseded      mock-app-1.9.0  1.16.0          Install complete
# 2               Tue Jun 11 15:11:34 2024        superseded      mock-app-2.0.0  1.16.0          Upgrade complete
# 3               Tue Jun 11 15:16:00 2024        deployed        mock-app-1.9.0  1.16.0          Rollback to 1
```

**Step 3:** Verify the content of the deployed container
```shell
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

# Hello Killercoda Folks! You recieved this message: Old version EVERYTHING IS GOOD !
```

We have observed that upon rollback, the application is restored to a previous version where everything is functioning correctly.
