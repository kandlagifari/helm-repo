# Part 1: Explore what we have deployed

**Step 1:** Verify namespaces
```shell
kubectl get ns

# NAME                 STATUS   AGE
# default              Active   3d9h
# dev-ns               Active   3m27s
# kube-node-lease      Active   3d9h
# kube-public          Active   3d9h
# kube-system          Active   3d9h
# local-path-storage   Active   3d9h
# prod-ns              Active   3m27s
```

We notice that we have 2 namespaces that will be used in that scenario **prod-ns** and **dev-ns**

**Step 2:** Verify chart templates
Our Helm chart is stored in /charts/mock-app. Let's explore its templates.

```shell
ls /charts/mock-app/templates

# configmap.yaml  deployment.yaml  hpa.yaml  service.yaml
```

The chart is composed of: deployment, configmap, service and hpa.

In our scenario, we will be focusing on the hpa.

HPA = Horizontal Pod Autoscaler


# Part 2: Add If condition to HPA template
The task involves introducing a condition into the Horizontal Pod Autoscaler (HPA) template to meet the following requirements:
- Deploy an HPA resource for releases in the **prod-ns** namespace.
- Do not deploy an HPA resource for releases in any other namespace

Deploy a **mock-app-prod** release in **prod-ns** namespace and a **mock-app-dev** release in **dev-ns** to check the solution.

**Step 1:** Update hpa.yaml template by adding the condition
```shell
{{- if eq .Release.Namespace "prod-ns" }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
.
.
.
{{- end }}
```

Deploy
```shell
helm -n prod-ns upgrade --install mock-app-prod /charts/mock-app/. --set environment=prod

# Release "mock-app-prod" does not exist. Installing it now.
# NAME: mock-app-prod
# LAST DEPLOYED: Tue Jun 11 15:52:23 2024
# NAMESPACE: prod-ns
# STATUS: deployed
# REVISION: 1
# TEST SUITE: None
```
```shell
helm -n dev-ns upgrade --install mock-app-dev /charts/mock-app/. --set environment=dev

# Release "mock-app-dev" does not exist. Installing it now.
# NAME: mock-app-dev
# LAST DEPLOYED: Tue Jun 11 15:52:41 2024
# NAMESPACE: dev-ns
# STATUS: deployed
# REVISION: 1
# TEST SUITE: None
```


# Part 3: Review multi-environment deployment
**Step 1:** Verify HPA resources deployed in dev-ns namespace
```shell
kubectl get hpa -n dev-ns

# No resources found in dev-ns namespace.
```

No HPA resource is deployed in dev-ns namespace

**Step 2:** Verify HPA resources deployed in prod-ns namespace
```shell
kubectl get hpa -n prod-ns

# NAME           REFERENCE                        TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
# mock-app-hpa   Deployment/mock-app-deployment   cpu: <unknown>/50%   1         3         1          2m18s
```

A HPA resource is deployed in prod-ns namespace
