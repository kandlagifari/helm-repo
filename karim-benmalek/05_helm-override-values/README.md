# Part 1: Explore what we have deployed

**Step 1:** Verify the deployed release
```shell
helm list -n dev-ns

# NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                APP VERSION
# mock-app        dev-ns          1               2024-06-11 15:23:34.572317983 +0000 UTC deployed        mock-app-1.0.0       1.16.0 
```

We have a single release named **mock-app** deployed in the target namespace.

**Step 2:** Verify the deployed resources
```shell
kubectl get all -n dev-ns

# NAME                                       READY   STATUS    RESTARTS   AGE
# pod/mock-app-deployment-7b8b5cd6cd-6j8mr   1/1     Running   0          2m14s

# NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
# service/mock-app-service   ClusterIP   10.100.121.122   <none>        5000/TCP   2m14s

# NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/mock-app-deployment   1/1     1            1           2m14s

# NAME                                             DESIRED   CURRENT   READY   AGE
# replicaset.apps/mock-app-deployment-7b8b5cd6cd   1         1         1       2m14s
```

**Step 3:** Verify the content of the deployed container
```shell
export PORT=5000
export SERVICE_IP=$(kubectl get svc -n dev-ns -l app=mock-app -o jsonpath='{.items[0].spec.clusterIP}')
```

```shell
curl -s http://${SERVICE_IP}:${PORT} -w "\n"

# Hello Killercoda Folks! You recieved this message: You will override this message
```

**Step 4:** Verify the printed message
```shell
helm get values --all mock-app -n dev-ns

# COMPUTED VALUES:
# appName: mock-app
# image:
#   repository: benmalekarim/mock-app
#   tag: v1.0.0
# message: You will override this message
```

The received message is passed through the default values file. The message value is `You will override this message` .


# Part 2: Override helm values using inline values

Upgrade the release to use the message `You are overriding the message using an inline value. Good job !` instead of the previous one.

**NOTE:** The chart is packaged and stored in /charts folder

**Step 1:**  Override helm values
```shell
helm -n dev-ns upgrade --install mock-app /charts/mock-app-1.0.0.tgz --set message="You are overriding the message using an inline value. Good job !"

# Release "mock-app" has been upgraded. Happy Helming!
# NAME: mock-app
# LAST DEPLOYED: Tue Jun 11 15:29:39 2024
# NAMESPACE: dev-ns
# STATUS: deployed
# REVISION: 2
# TEST SUITE: None
```

**Step 2:** Verify the printed message
```shell
helm get values --all mock-app -n dev-ns

# COMPUTED VALUES:
# appName: mock-app
# image:
#   repository: benmalekarim/mock-app
#   tag: v1.0.0
# message: You are overriding the message using an inline value. Good job !
```

The message is overridden using the inline value passed through the upgrade. The current message value is `You are overriding the message using an inline value. Good job !` .


# Part 3: Update the current message value using the values file approach

By adopting this method, you'll have the ability to easily monitor the latest configurations directly from your Git repository, which is highly recommended.

Firstly, create a **values.yaml** file within the **/charts** folder. Within this file, set the value of the message to `You are overriding the message using a values file. You rock !` .

Then, upgrade the release to use the new message by specifying the values file, rather than the previous one.

**Step 1:** Upgrade helm charts
```shell
echo "message: You are overriding the message using a values file. You rock it!" >> /charts/values.yaml
```

```shell
helm -n dev-ns upgrade --install mock-app /charts/mock-app-1.0.0.tgz  --values /charts/values.yaml

# Release "mock-app" has been upgraded. Happy Helming!
# NAME: mock-app
# LAST DEPLOYED: Tue Jun 11 15:33:45 2024
# NAMESPACE: dev-ns
# STATUS: deployed
# REVISION: 4
# TEST SUITE: None
```

**Step 2:** Verify the printed message
```shell
helm get values --all mock-app -n dev-ns

# COMPUTED VALUES:
# appName: mock-app
# image:
#   repository: benmalekarim/mock-app
#   tag: v1.0.0
# message: You are overriding the message using a values file. You rock it!
```

The message is overridden using the custom values file passed through the upgrade. The current message value is `You are overriding the message using a values file. You rock it!` .
