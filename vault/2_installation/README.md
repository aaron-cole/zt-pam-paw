# zt-pam-paw - Vault installation

-- Installation after moving to discconnected environment.

Recommend way is to use the helm chart for vault.

References - https://developer.hashicorp.com/vault/docs/deploy/kubernetes/helm

--- oc-mirror upload ---

--You have logged into the quay instances in this ssh session and have an auth.json token 

1. Using an isc file that was used to download the images for vault
oc-mirror --v2 -c hashicorp-vault-isc.yaml \
  --from file:///opt/quay/hashicorp \ #/dir/to/tarball 
  docker://quayserver.example.com:8443 \
  --dest-tls-verify=false \
  --cache-dir=/opt/quay/cache


--helm chart deployment--
Step 1 - helm tarball should have been brought over extract - recommend to place in your $PATH

Step 2 - create a values file and get items from your quay

Step 3 - login to the openshift cli

Step 4 - apply the helm chart
/usr/local/bin/helm update vault ./vault-0.31.0.tgz -f values.yaml --namespace vault --create-namespace # (replace the ./vault-0.31.0.tgz with vault tarball brought over)

