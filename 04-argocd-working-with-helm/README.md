# Working With Helm

[Helm](https://helm.sh/) is considered the defacto package manager for Kubernetes applications. You can define, install, and update your pre-packaged applications or comsume a prebuilt packed application from a trusted repository. This is a way to bundle up, and deliver prebuilt Kubernetes applications.

The main components of Helm are:

* `Chart` - Which is a a package consisting of related Kubernetes YAML files used to deploy something (Application/Application Stack/etc).
* `Repository` - Is a place where Charts can be stored, shared and distributed.
* `Release` - Is a specific instance of a Chart deployed on a Kubernetes cluster.

Helm works by the user providing parameters (most of the time via a YAML file) against a Helm chart via the CLI. These parameters get injected into the Helm template YAML to produce a consumable YAML that us deployed to the Kubernetes cluster.

![helm-overview](img/helm.jpg)

# Argo CD and Helm

Argo CD has native support for Helm built in. You can directly call a Helm chart repo and provide the values directly in the [Application](https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/#applications) manifest. Also, you can interact and manage Helm on your cluster directly with the Argo CD UI or the `argocd` CLI.

There are two ways to deploy a Helm chart with Argo CD. The first is to use an Argo CD Application, and specify the parameters. We will be deploying [this application](https://raw.githubusercontent.com/christianh814/kbe-apps/main/02-working-with-helm/app/quarkus-app.yaml). Taking a look at `.spec.source.helm`, you'll see that you can provide the parameters directly in this manifest. Snippet:

```yaml
spec:
  source:
    helm:
      parameters:
        - name: build.enabled
          value: "false"
        - name: deploy.route.tls.enabled
          value: "true"
        - name: image.name
          value: quay.io/redhatworkshops/gitops-helm-quarkus:latest
    chart: quarkus
    repoURL: https://redhat-developer.github.io/redhat-helm-charts
    targetRevision: 0.0.3
```

Let's break this `.spec.source.helm` section down a bit:

* `parameters` - This section is where you'll enter the parameters you want to pass to the Helm chart. These are the same values that you'd have in your `Values.yaml` file.
* `chart` - This is the name of the chart you want to deploy from the Helm Repository.
* `repoURL` - This is the URL of the Helm Repository.
* `targetRevision` - This is the version of the chart you want to deploy.

This can be used to deploy the Helm chart on to your cluster, which is like using `helm install ...`.

> **NOTE** What actually happens is that Argo CD runs `helm template ... | kubectl apply -f -`.

Deploy this manifest by running `kubectl apply`

```
kubectl apply -f \
https://raw.githubusercontent.com/christianh814/kbe-apps/main/02-working-with-helm/app/quarkus-app.yaml
```

Once you've applied this Argo CD Application manifest, it should deploy the specified Helm chart. Looking in the Argo CD WebUI, you should see the following:

![argocd-helm-app](img/argocd-helm-app.png)

Pay special attention to the Helm (⎈) logo, indicating that it's a Helm chart being deployed.