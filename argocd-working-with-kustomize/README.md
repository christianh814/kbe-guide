# Working With Kustomize

[Kustomize](https://kustomize.io/) is a tool that traverses a Kubernetes manifest to add, remove or update configuration options without forking. It is available both as a standalone binary and as a native feature of `kubectl`.

The principals of `kustomize` are:

* Purely declarative approach to configuration customization
* Manage an arbitrary number of distinctly customized Kubernetes configurations
* Every artifact that kustomize uses is plain YAML and can be validated and processed as such
* As a "templateless" templating system; it encourages using YAML without forking the repo it.

It achieves this with a series of `bases` (where the YAML is stored) and `overlays` (directores where deltas are stored). The advantage of this is that you can have different versions of the same YAML without storing or copying the YAML in different places.

Argo CD has native built in support for Kustomize and will automatically detect the use of Kustomize without further configuration.

# Kustmoized Repository

We will still be working without [sample repository](https://github.com/christianh814/kbe-apps) in the `01-working-with-kustomize` directory. Looking in this directory you'll find a `base` directory. You'll note the usual Kubernetes manifests but with the addition of a `kustomization.yaml` file (which you can see [here](https://github.com/christianh814/kbe-apps/blob/main/01-working-with-kustomize/base/kustomization.yaml)) with the following.

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- test-deployment.yaml
- test-ns.yaml
- test-svc.yaml
```

This is a special file that signifies that you'll be using Kustomize. It lists all the files that takes a part of this `Kustomization`. 

If you look under the `overlays/default` directory, you'll see the following (and ONLY thing in this directory) `Kustomization` file:

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
- ../../base

patchesJson6902:
  - target:
      version: v1
      kind: Deployment
      name: welcome-php
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 3
```

The `bases` section says "load the files from this directory", which is the directory that has all the YAML for this application. The `patchesJson6902` file is "what to patch" (or "change") from the base directory. In this case, we are going to change the replica count from `1` to `3`.

# Using Argo CD with Kustomize

We can now create this application by specifying the repo and path to the overlay. You can do this with the Argo CD UI like before, or with the `argocd` cli. For example:

```
argocd app create kapp \
--repo https://github.com/christianh814/kbe-apps \
--path 01-working-with-kustomize/overlays/default \
--dest-namespace kapp \
--dest-server https://kubernetes.default.svc \
--self-heal \
--sync-policy automated \
--sync-retry-limit 5 \
--revision main
```

By specifying only the overlay, we are loading in all the YAML in `base` and changing the `replicas` count from `1` to `3`. Verify this by running: `kubectl get pods -n kapp`

```
$ kubectl get pods -n kapp
NAME                           READY   STATUS    RESTARTS   AGE
welcome-php-689d9cfb5b-d4s86   1/1     Running   0          65s
welcome-php-689d9cfb5b-jw9jd   1/1     Running   0          65s
welcome-php-689d9cfb5b-qpw47   1/1     Running   0          65s
```

We turned on autohealing, so any divergence will be changed back. You can test this out by trying to make a change using `kubectl` and seeing Argo CD changing it back to what is in Git.

# Why?

The biggest advantage of using Kustomize, is that you can use the same base set of YAMLs for your environment and overlay the deltas for subsequent environments. For example, if you have an application that gets deployed in 3 different environments (like dev, stage, prod); your layout can look like this:

```
.
├── base
└── overlays
    └── dev
    └── stage
    └── prod
```

In this set up, you will only store the deltas in the `overlays` directory. And you'll have 3 Argo CD Applications poining to the `overlays/dev`, `overlays/stage`, and `overlays/prod` directories (respectivley).

Read more about the avantages of using directories this way by reading the [following blog](https://codefresh.io/blog/stop-using-branches-deploying-different-gitops-environments/).