# zt-pam-paw
Solution designed to meet Zero trust, PAM and PAW Requirements for Linux Admins


---The mirror registry Installation---
References:
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/disconnected_environments/installing-mirroring-creating-registry
- https://docs.redhat.com/en/documentation/red_hat_quay/3.15/html-single/securing_red_hat_quay/index#ssl-tls-quay-overview


1) Downlod the mirror-registry tarball and take to disconnected environment

2) Install - ***as non-root user***
```bash
./mirror-registry install \
  --quayHostname <host_example_com> \
  --quayRoot /opt/quay
```  
3) Use the username/password to log in.
```bash
podman login -u init \
  -p <password> \
  <host_example_com>:8443 \
  --tls-verify=false
```
--Info on replacing self signed certs
https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/disconnected_environments/installing-mirroring-creating-registry#mirror-registry-ssl-cert-replace_installing-mirroring-creating-registry


***note*** if you don't use TMPFS filesystem for /tmp then you need to do this too:
- Copy the /usr/lib/tmpfiles.d/podman.conf to /etc/tmpfiles.d/
- We need to add in the following 2 lines to our copied file so that way containers can be restarted on reboot without issues.
```bash
R! /tmp/storage-run-*/containers/
R! /tmp/storage-run-*/libpod/tmp/
```


---The openshift mirror prep (performed on upstream connected server)---
References: 
- https://access.redhat.com/articles/7048171
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/disconnected_environments/about-installing-oc-mirror-v2


Download oc-mirror and untar
- tar xvzf oc-mirror.rhel9.tar.gz
- chmod +x oc-mirror 
- mv oc-mirror /usr/local/bin/ (or somewhere in your path)


oc-mirror -c isc.yml --authfile ~/pull_secret.json file:///oc-mirror --v2



---The openshift mirror deployment (performed in disconnected environment)---
1) on target you need oc-mirror - so that should be copied over and put on target server
- tar xvzf oc-mirror.rhel9.tar.gz
- chmod +x oc-mirror 
- mv oc-mirror /usr/local/bin/ (or somewhere in your path)


***Note
I have found that mirroring the registry is easier to done as root not by whatever user (quay) setup to run quay.
The below is based on the assumption you are root:


1) Use the username/password to log in to your local quay - this stores the auth token for the mirror to be successfull
podman login -u init \
  -p <password> \
  <host_example_com>:8443 \
  --tls-verify=false

2) Apply the Mirror Registry to your new registry
oc-mirror --v2 -c isc.yml --from file:///opt/quay docker://<host_example_com>:8443 --dest-tls-verify=false --cache-dir /opt/quay/cache (optional "--log-level debug" for issues) 

