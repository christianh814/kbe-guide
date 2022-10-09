# GitOps Overview

GitOps is the latest operating framework to emerge from folks who have been implementing Cloud Native technologies. Distilling the term to it's core ideals, GitOps is a set of practices that leverages Git workflows to manage infrastructure and application configurations.

By using Git repositories as the source of truth, it allows the DevOps team to store the entire state of the cluster configuration in Git so that the trail of changes are visible and auditable.

In late 2019, the GitOps Working Group was founded to define not only what GitOps is; but also provide a home for best practices, whitepapers, and case studies. And in 2021, the working group released the [OpenGitOps Principles](https://opengitops.dev#principles) that can help guide organization looking to get the most out of their Cloud Native journey. They are as follows:

* Declarative - A system managed by GitOps must have its desired state expressed declaratively.
* Versioned and Immutable - Desired state is stored in a way that enforces immutability, versioning and retains a complete version history.
* Pulled Automatically - Software agents automatically pull the desired state declarations from the source.
* Continuously Reconciled - Software agents continuously observe actual system state and attempt to apply the desired state.

It's important to note that GitOps doesn't "replace" DevOps at all. In fact GitOps _is_ DevOps actualized. DevOps is the organization's culture, while GitOps is what that culture looks like in Practice.

# Argo CD

Argo CD is part of a larger ecosystem of DevOps tools that is governed by the [Argo Project](https://argoproj.github.io/). These tools were developed internally at Intuit, and later donated to the CNCF. The Argo Project now enjoys a vibrant community made up of various technology vendors and end users that has propelled it into popularity.

Argo CD was written with GitOps in mind, to deliver changes to a Kubernetes cluster at massive scale. It detects and prevents drift in your Kubernetes clusters by working with raw YAML stored in a Git repository and using the apply functionality in Kubernetes. Argo CD can also work with Helm and Kustomize to render the YAML produced by those tools before applying them to the Kubernetes cluster.

There is also a community made of members of the Argo Project called "Argo Labs". This project serves as an incubator for other tools related to Argo, which can be used together or standalone.