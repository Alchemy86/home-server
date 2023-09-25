# Home Server

## Prepare the node

### Set up USB Disk Storage
```
sudo fdisk -l
sudo mkfs.ext4 /dev/sdb
sudo mkdir /data
sudo mount /dev/sdb /data
sudo blkid
sudo nano /etc/fstab
-----------------------------------------------------------
>    # external hard drive
>    UUID="4e5f2df1-7e92-4671-81f0-bf3dcfeb2f77" /data
-----------------------------------------------------------
```

### Install K3S
```
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s - --disable=traefik
```

### Install Flux
```
export GITHUB_USER=<username>
export GITHUB_TOKEN=<personal_access_token>
flux bootstrap github --owner=$GITHUB_USER --repository=home-server --branch=main --path=./clusters/home-server --personal
kubectl create secret generic sops-age --namespace=flux-system --from-file=age.agekey
flux create secret git secrets-repo-ssh --url=ssh://git@github.com/Strayfe/home-server-secrets --ssh-key-algorithm=ecdsa --ssh-ecdsa-curve=p521
-- store this ssh key in github manually for now, will come up with an automation for this step or just reuse a constant
```

### (Optional) Install K9S
```
wget https://github.com/derailed/k9s/releases/download/v0.27.4/k9s_Linux_amd64.tar.gz
tar -zxvf k9s_Linux_amd64.tar.gz
sudo mv k9s /usr/local/bin/
sudo ln /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

### (Optional) Install Helm
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
