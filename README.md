# Setup Kubernetes Cluster with Ansible
Setup kubernetes cluster with prometheus and Grafana as monitoring and private Docker registry all backed with glusterfs file storage.
## System and Requirements:
Tested on Ubuntu 16.04 with Ansible 2.7.*

```bash
sudo add-apt-repository ppa:ansible/ansible-2.7
sudo apt-get update
sudo apt-get install ansible
```

Atleast Three VPS with 4GB RAM, 2vCPU for Master, Node and GlusterFS server.

Use **ubuntu** user to interact with Cluster after successful installation
## Preflights:
Generate SSH-KEY on **Master** and add it to all **Servers** including Master, Nodes and GlusterFS.

Edit **registry-with-authentication/configMap-docker.yaml** with your own desired S3 Compatible storage.

Edit **hosts** With IPs and Addresses.
## Installation
```bash
ansible-playbook -i hosts main.yaml
```
# Cautions:
**Certificate Names** Can only contain alphabetic characters and hyphens.

**grafana_admin_pw** Must start with an alphabetic character.
# Cluster Flow chart
![mermaid](https://user-images.githubusercontent.com/23025217/58875432-c6f12700-86e0-11e9-978a-52ac080090ba.JPG)
