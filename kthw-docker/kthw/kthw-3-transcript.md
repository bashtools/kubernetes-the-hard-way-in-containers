# KTHW 03 Compute Resources

![](../images/kthw-3.gif)

View the [screencast file](../screencasts/kthw-3.scr))

```
# ---------------------------------------------------------
# Kubernetes the Hard Way - using `mokctl` from My Own Kind
# ---------------------------------------------------------
# 03-compute-resources
# Create three masters and three workers and 1 more container for haproxy

alias mokctl="sudo mokctl"
mokctl build image --get-prebuilt-image
mokctl create cluster --skipmastersetup --skiplbsetup --with-lb kthw --masters 3 --workers 3
# List the containers
mokctl get cluster kthw
# Accessing the containers:
mokctl exec kthw-master-1
# we're in a Master!
exit
# Invoking the chooser
mokctl exec
5
# we're in a Worker!
exit
# ------------------------------------
# Next: Certificate Authority
# ------------------------------------
```
