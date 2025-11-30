- In this project, I built a complete GitOps workflow using Argo CD to manage a Kubernetes application end-to-end. 
- I started by installing Argo CD, connecting it securely to a GitHub repository, and defining all Kubernetes manifests declaratively. From there, I deployed the application through Git alone—Argo CD continuously reconciled the desired state from Git and applied changes to the cluster. 
- I tested updates by modifying the replica count, committing and pushing the change, then watching Argo CD sync the new version and Kubernetes scale out automatically. 
- I also performed a full rollback using git revert, allowing Argo CD to restore the previous application state with no manual kubectl commands. 
- This experience reinforced my understanding of declarative infrastructure, immutable deployments, and the operational discipline behind real-world GitOps workflows."



## Overview

### This project demonstrates a full GitOps workflow implemented with Argo CD and Kubernetes.

> The objective was to operationalize the Globomantics application using declarative manifests stored in Git, managed and deployed through Argo CD’s reconciliation loop.

> I completed the full lifecycle:
Install → Configure → Deploy → Update → Rollback → Observe,
all driven entirely through Git.

### Key Outcomes
```
1. Built a Complete GitOps Environment with Argo CD

Installed Argo CD into a Kubernetes cluster (argocd namespace).

Exposed and accessed the Argo CD API/UI through port-forward.

Installed and authenticated the Argo CD CLI for application management.

Connected a private GitHub repository using a fine-grained PAT for secure read/write access.
```

```
2. Authored Declarative Kubernetes Manifests

Created the full application specification using:

Deployment (replicas, labels, containers, ports)

Service (ClusterIP)

Namespace configuration

Organized manifests under a dedicated manifests/ directory for GitOps alignment.
```

```
3. Implemented GitOps Deployment Workflow

Created an Argo CD Application pointing to:

Repo: globomantics-gitops

Path: /manifests

Cluster: https://kubernetes.default.svc

Namespace: globomantics

Synced the application, allowing Argo CD to apply manifests to the cluster.

Verified health and synchronization status via argocd app get.
```

```
4. Performed Git-Driven Updates (Scaling Replicas)

Modified the Deployment (scaling replicas from 2 → 4).

Committed and pushed changes to GitHub.

Synced via Argo CD to apply the update.

Validated correct behavior through:

kubectl get deploy -n globomantics

kubectl get pods
```

```
5. Executed a Full GitOps Rollback

Reverted the last commit using:

git revert HEAD -n
git commit -m "Reverted last commit"


Pushed the rollback to Git, and synced with Argo CD.

Confirmed the Deployment scaled back to 2 replicas.

Reviewed both Argo CD app logs and container runtime logs for verification.
```

```
6. Observed the GitOps Pipeline in Action

Examined Argo CD logs to understand the reconciliation, sync behavior, and history.

Used Kubernetes logs to confirm application health after both updates and rollbacks.

Demonstrated how Git commits directly map to cluster state changes.
```

### What I Learned

- [x] This lab reinforced the real value of GitOps:

- [x] Git is the source of truth, not kubectl.

- [x] Argo CD continuously reconciles the desired state with the live cluster state.

- [x] Application updates and rollbacks are version-controlled, safe, and auditable.

- [x] Declarative deployments reduce drift and ensure consistency across environments.

- [x] Sync, health checks, and rollback are predictable and reproducible.


```
I now have hands-on experience with:

Argo CD Application management

Declarative Kubernetes configurations

Secure Git repository integration

Git-based rollback strategies

Observability through Argo CD + Kubernetes logs
```



#### MISC

• Implemented a complete GitOps pipeline using Argo CD by deploying a Kubernetes-based Globomantics application, creating declarative manifests (Deployments, Services), and configuring Argo CD applications that continuously reconciled cluster state with Git.

• Automated end-to-end application lifecycle management by pushing updates to Git (e.g., replica scaling), triggering controlled Argo CD syncs, validating health via CLI/UI, and debugging Kubernetes workloads using logs, events, and resource inspections.

• Executed reliable rollbacks using Git commit history (git revert), allowing Argo CD to safely restore previous application state without manual kubectl intervention—demonstrating strong understanding of immutable infrastructure and declarative operations.

• Built a fully operational GitOps environment by installing and configuring Argo CD on a Kubernetes cluster, integrating GitHub repositories with fine-grained tokens, and managing application sources securely.

• Authored Kubernetes manifests (Deployments, Services) for the Globomantics application and aligned the Git repository structure to support GitOps workflows using Argo CD’s app create, sync, and automated reconciliation model.

• Implemented version-controlled application updates by modifying Kubernetes manifests (e.g., scaling replicas), committing/pushing changes to GitHub, and performing Argo CD syncs to reconcile desired vs. live state with full health verification.

• Performed safe, auditable GitOps rollbacks using Git (git revert) and validated recovery through Argo CD logs, deployment state, and Kubernetes runtime logs—reinforcing configuration drift prevention and declarative infrastructure best practices.