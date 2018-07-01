# kube-stuffs

# Create a cluster admin

kubectl create ns admin 

kubectl create sa admin -n admin

kubectl create clusterrolebinding admin-cluster-binding --clusterrole=cluster-admin --serviceaccount=admin:admin

# Deploy Ingress Controller

kubectl create serviceaccount --namespace kube-system tiller

kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'

helm init --service-account tiller --upgrade

helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true -n kube-system

# Generate Certificate from letsencrypt

helm install --name cert-manager --namespace kube-system stable/cert-manager

kubecl create -f cert-manager/letsencryp-clusterissuer-staging.yaml

kubecl create -f cert-manager/certificate-letsencrypt-staging.yaml

Then, switch Staging to Prod.

# Load your own certificate manually via kube CLI

kubectl create secret tls foo-secret --key tls.key --cert tls.crt



