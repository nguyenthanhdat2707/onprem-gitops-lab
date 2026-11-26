# Argo CD bootstrap boundary

Argo CD is not installed in the lab yet. Pin the lab bootstrap to v3.6.3.
The official namespace-scoped manifest excludes CRDs, so install the CRDs
from the same release first, then its namespace-scoped manifest in `argocd`.
These are cluster and shared-environment changes requiring review before
execution:

```sh
kubectl create namespace argocd
kubectl apply --server-side -k 'https://github.com/argoproj/argo-cd/manifests/crds?ref=v3.6.3'
kubectl apply -n argocd --server-side -f 'https://raw.githubusercontent.com/argoproj/argo-cd/v3.6.3/manifests/namespace-install.yaml'
kubectl apply -f argocd/controller-role.yaml
kubectl apply -f argocd/project.yaml
```

The controller can write
Deployments and Services only in `demo-app`; the AppProject allows only
this GitOps repository and that destination.

Argo CD needs a read-only deploy key for the private GitLab repository and
the verified SSH host key for `10.20.0.10`. Store the private key in an Argo CD
repository Secret, never in Git. Confirm the Stage 1 image pull Secret
`demo-app/gitlab-registry` before the first sync.

Apply `environments/lab/api.yaml` and `frontend.yaml` only after the pinned
digest has been signed and verified. They intentionally have no automated
sync policy, so promotion is explicit. The source CI has no cluster credentials.
