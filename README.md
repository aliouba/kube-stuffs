
## Generate letsencrypt keys

## Install certbot
```
sudo apt-get update -y 
sudo pip install -U pyopenssl
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:certbot/certbot 
sudo apt-get update -y
sudo apt-get install python-certbot-nginx -y
```


## Install pip
```
sudo apt-get update -y
sudo apt-get install python-pip -y
```


## Install Route 53 certbot plugin 
```
pip install certbot_dns_route53==0.22.2
pip install awscli
````


In the home folder create an .aws folder and inside that create a text file with the name credentials with the following contents.

[default]
aws_access_key_id=XXXXXX
aws_secret_access_key=XXXX/XXXXX


## Create wildcard certificates

```
certbot certonly -d apps.cloud.dakar.io -d *.apps.cloud.dakar.io --dns-route53 --logs-dir /home/vagrant/letsencrypt/log/ --config-dir /home/vagrant/letsencrypt/config/ --work-dir /home/vagrant/letsencrypt/work/ -m hello@beopenit.com --agree-tos --non-interactive --server https://acme-v02.api.letsencrypt.org/directory
```

# Create a cluster admin

```
kubectl create ns admin 

kubectl create sa admin -n admin

kubectl create clusterrolebinding admin-cluster-binding --clusterrole=cluster-admin --serviceaccount=admin:admin
```

# Deploy Ingress Controller

```

kubectl create serviceaccount --namespace kube-system tiller

kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'

helm init --service-account tiller --upgrade

helm install --name nginx-ingress stable/nginx-ingress --set rbac.create=true --namespace=kube-system

kubectl create secret tls wild.dakar.io --key privkey1.pem --cert fullchain1.pem

kubectl edit deploy nginx-ingress-controller  -n kube-system

```

Then, add - --default-ssl-certificate=default/wild.dakar.io

# Generate Certificate from letsencrypt

```
helm install --name cert-manager --namespace kube-system stable/cert-manager

kubecl create -f cert-manager/letsencryp-clusterissuer-staging.yaml

kubecl create -f cert-manager/certificate-letsencrypt-staging.yaml
```

Then, switch Staging to Prod.

# Load your own certificate manually via kube CLI

```

kubectl create secret tls foo-secret --key tls.key --cert tls.crt
```


