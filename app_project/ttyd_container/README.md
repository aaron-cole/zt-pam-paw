# zt-pam-paw 
Solution designed to meet Zero trust, PAM and PAW Requirements for Linux Admins

This requires 2 items - 1) creation of the TTYD container.  2) pulling the oauth2-proxy if not included in the original oc-mirror (like me)

Step 1) build container

Step 2) pull oauth2-proxy and save to take to disconnected environment
podman pull quay.io/oauth2-proxy/oauth2-proxy:latest
podman save quay.io/oauth2-proxy/oauth2-proxy:latest -o oauth2-proxy.tar


