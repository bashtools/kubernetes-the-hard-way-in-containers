# KTHW 05 Generating Kubernetes Configuration Files for Authentication

![](../images/kthw-5.gif)

View the [screencast file](../screencasts/kthw-5.scr)

```
# ---------------------------------------------------------
# Kubernetes the Hard Way - using `mokctl` from My Own Kind
# ---------------------------------------------------------
# 05-generating-kubernetes-configuration-files-for-authentication
# Create all required 'kubeconfig' files

# We need to log back into the docker container, 'kthw', then
# paste the command blocks

docker exec -ti kthw bash

# Kubernetes Public IP Address

# Set the public address variable to the load balancer address
KUBERNETES_PUBLIC_ADDRESS=$(grep kthw-lb /certs/cluster-list.txt | awk '{ print $NF; }')
echo $KUBERNETES_PUBLIC_ADDRESS
# Need to be in the directory where the certs are
cd /certs

# The kubelet Kubernetes Configuration File

for instance in kthw-worker-1 kthw-worker-2 kthw-worker-3; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443 \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-credentials system:node:${instance} \
    --client-certificate=${instance}.pem \
    --client-key=${instance}-key.pem \
    --embed-certs=true \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig=${instance}.kubeconfig

  kubectl config use-context default --kubeconfig=${instance}.kubeconfig
done
ls -lh *.kubeconfig

# The kube-proxy Kubernetes Configuration File

{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443 \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-credentials system:kube-proxy \
    --client-certificate=kube-proxy.pem \
    --client-key=kube-proxy-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-proxy \
    --kubeconfig=kube-proxy.kubeconfig

  kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
}
ls -lh *.kubeconfig

# The kube-controller-manager Kubernetes Configuration File

{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.pem \
    --client-key=kube-controller-manager-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config use-context default --kubeconfig=kube-controller-manager.kubeconfig
}
ls -lh *.kubeconfig

# The kube-scheduler Kubernetes Configuration File

{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=kube-scheduler.pem \
    --client-key=kube-scheduler-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-scheduler \
    --kubeconfig=kube-scheduler.kubeconfig

  kubectl config use-context default --kubeconfig=kube-scheduler.kubeconfig
}
ls -lh *.kubeconfig

# The admin Kubernetes Configuration File

{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=admin.kubeconfig

  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem \
    --embed-certs=true \
    --kubeconfig=admin.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=admin \
    --kubeconfig=admin.kubeconfig

  kubectl config use-context default --kubeconfig=admin.kubeconfig
}
ls -lh *.kubeconfig

# Distribute the Kubernetes Configuration Files

# We need to log out of this container then copy the certs to the
# kubernetes nodes
exit
# All the kubeconfigs should be in the kthw-certs directory
ls kthw-certs/*.kubeconfig
# They are, good!
# Now to copy them, workers first:
cd kthw-certs
for instance in kthw-worker-1 kthw-worker-2 kthw-worker-3; do
  sudo docker cp ${instance}.kubeconfig ${instance}:/root
  sudo docker cp kube-proxy.kubeconfig ${instance}:/root
done
# Copy to the masters
for instance in kthw-master-1 kthw-master-2 kthw-master-3; do
  sudo docker cp admin.kubeconfig ${instance}:/root
  sudo docker cp kube-controller-manager.kubeconfig ${instance}:/root
  sudo docker cp kube-scheduler.kubeconfig ${instance}:/root
done
# All done :)

# ---------------------------------------------------
# Next: Generating the Data Encryption Config and Key
# ---------------------------------------------------
```
