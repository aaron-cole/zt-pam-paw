# zt-pam-paw - Vault installation

This requires parts - 1- pulling the images, 2- pulling the helm chart

Recommend way is to use the helm chart for vault.

References - https://developer.hashicorp.com/vault/docs/deploy/kubernetes/helm


---Images via oc-mirror---

1. Using an isc file download the images for vault
oc-mirror -c hashicorp-vault-isc.yaml --authfile ~/pull_secret.json file:///oc-mirror/hashicorp_vault --v2

2. take tar ball to disconnected environment.


---Helm Chart---
Step 1 - Download helm and extract
https://github.com/helm/helm/releases

Step 2 - add the hashicorp repository
./helm repo add hashicorp https://helm.releases.hashicorp.com

Step 3 - pull the version you want - in this instance i downloaded helm version 0.31 which correlates to what is in my oc-mirror ISC file.
./helm pull hashicorp/vault --version 0.31.0 --untar

Step 4 - package it up to take over
./helm package vault

Step 5  - take tarball to discconnected environment.

