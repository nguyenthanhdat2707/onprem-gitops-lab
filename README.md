# cicd-gitops-lab

Desired state for the lab application. Source, image build and signing live in
`root/cicd-lab`; this repository holds the Helm chart and Argo CD configuration.
Do not commit registry credentials, signing keys or kubeconfig here.

The current digest in `environments/lab/` is the unsigned Stage 1 image.
Do not sync it until the Stage 2 signing pipeline verifies that exact digest,
or a later signed digest is reviewed into both Applications.

Promotion: merge the source change after review; get the verified digest from
`reports/signing-evidence.txt`; update both Applications in a reviewed GitOps
merge request; then sync them manually and check Synced/Healthy and HTTP 200.
See `argocd/README.md` for the cluster bootstrap boundary.
