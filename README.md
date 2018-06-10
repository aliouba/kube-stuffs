# kube-stuffs

# Create a cluster admin

kubectl create ns admin 

kubectl create sa admin -n admin

kubectl create clusterrolebinding admin-cluster-binding --clusterrole=cluster-admin --serviceaccount=admin:admin

# Deploy Ingress Controller

helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true -n kube-system

# Generate Certificate from letsencrypt

helm install --name cert-manager --namespace kube-system stable/cert-manager

kubecl create -f cert-manager/letsencryp-clusterissuer-staging.yaml

kubecl create -f cert-manager/certificate-letsencrypt-staging.yaml

Then, switch Staging to Prod.
