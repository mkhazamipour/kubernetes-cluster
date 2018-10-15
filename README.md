# Installation Demo
[![asciicast](https://asciinema.org/a/206649.png)](https://asciinema.org/a/206649)
# Kubernetes cluster

To setup k8s cluster you need to change some values.
  - configmaps are case sensitive. edit with caution.
# Files to edit

  - Change public key path for ansible user installation in nodes.
  - /k8s-cluster/intial.yml
```
- /root/publickey.pub
```
  - Change Master and Nodes ip address in hosts file.
  - /k8s-cluster/hosts
```
[masters]
master ansible_host=Master-IP ansible_user=root

[workers]
worker1 ansible_host=Nodes-IP ansible_user=root

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```
  - Replace your own domain with domain.org,also consider priv is your subdomain name you can change it too.
  - /k8s-cluster/certificate/certificate.yml
```
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: priv-domain-org
  namespace: registry
spec:
  secretName: priv-domain-org
  issuerRef:
    name: letsencrypt
    kind: ClusterIssuer
  commonName: priv.domain.org
  dnsNames:
  - priv.domain.org
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - priv.domain.org
```
  - Replace your own e-mail address for letsencrypt expiry notification.
  - /k8s-cluster/certificate/letsencrypt.yml
```
    # Email address used for ACME registration
    email: info@domain.org
```

  - Replace your own htpasswd values to authenticate with docker private registry. Docker will consider these values as docker login username and password.
  - /k8s-cluster/registry-with-authentication/configMap-auth.yml
```
data:
  htpasswd: |
    mort:$2y$05$bHJ/fhdHRekg3KKR/uOdD.1sVjEPbxEBxukc6YlfabWo5oADFeDtq

```
  - Replace your bucket-name, access-key and secret-key by values provided by DigitalOcean's space.
  - /k8s-cluster/registry-with-authentication/configMap-docker.yml
```
      s3:
        region: digitalocean-ams3
        regionendpoint: ams3.digitaloceanspaces.com
        bucket: bucket-name
        accesskey: access-key
        secretkey: secret-key
```
  - Change domain.org to your own domain name and priv to your subdomain. please consider the value of these two should be exact the same with values in certificate.yml.
  - /k8s-cluster/registry-with-authentication/registry-ingress.yml
```
spec:
  backend:
    serviceName: registry
    servicePort: 5000
  rules:
  - host: priv.domain.org
    http:
      paths:
      - path: /
        backend:
          serviceName: registry
          servicePort: 5000
  tls:
  - secretName: priv-domain-org
    hosts:
    - "priv.domain.org"
```
# How to run
Use ansible to run your cluster with one master and multi nodes.
Just run cluster with ansible-playsbook
```
ansible-playbook -i hosts main.yml
```
