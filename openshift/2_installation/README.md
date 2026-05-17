# zt-pam-paw
Solution designed to meet Zero trust, PAM and PAW Requirements for Linux Admins


---Openshift installation---
references:
https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/disconnected_environments/installing-disconnected-environments
https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html-single/installing_on_any_platform/index
https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html-single/installing_on_vmware_vsphere/index#installing-restricted-networks-vsphere

setup for UPI installation- 
6 machines:
- 3 control-plane/masters
- 3 worker nodes

#If doing on VMs on VMware I had to ensure the 2 following items:
1) My Rendevous host needed more memory for the install - 
- https://access.redhat.com/solutions/7133039

2) Ensure in vmware the following is set on VMware VMs
disk.EnableUUID=1

-DNS Requirements:
1) I had IPs for my 6 hosts, and DNS entries for the machine names of them
2) Need an INGRESS IP with forward/reverse for *.apps.{cluster domain} - like *.apps.ocp.example.com - where ocp.example.com is the cluster domain
3) Need an API IP with forward/revers to api.{cluster domain}
4) CNAME for api-int.{cluster domain}


- Download the oc binary
https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html-single/installing_on_any_platform/index#cli-installing-cli-linux_installing-platform-agnostic

-Create install_config.yaml and ensure configuration - and create a backup - it gets consumed when we use it


-Create agent_config.yaml and ensure configuration - and create a backup - it gets consumed when we use it


-Since FIPS matters to me, extract the openshift-install-fips command - server i'm doing this on is in FIPS mode too - so it matters also.

1) find the image from your quay, I used the quay webui - it should be something like "openshift/release-image@sha256:xxxxxxxx" - copy the podman pull command for it

2) extract the openshift-install-fips command from it using oc binary:
oc adm release extract -a {/location/to/auth.json} \  # I can't remember, but i believe since i did this as root - you may not need it if you logged in already 
  --command=openshift-install-fips \
  --to {/location/to_save_command} \
  --idms-file=/opt/quay/working-dir/cluster-resources/idms-oc-mirror.yaml \
  hostname.example.com:8443/openshift/release-image@sha256:adasdfsdfsxxx (what you copied from #1)

3) Create your image to use to bootstrap the nodes:
./openshift-install-fips agent create image ( optional but i like to see "--log-level debug")

4) boot machines to images and let them do the rest for the install


--- Initial Configuration ---
1) login to webui and get login token
2) login to cli
3) Navigate to the working-dir/cluster-resources
4) Patch operator hub
oc path OperatorHub cluster --type jason -p '{{"op": "add", "path": "/spec/disableAllDefaultSources", "value": true}}'
5) oc apply -f updateService.yaml




