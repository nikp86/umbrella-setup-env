# umbrella-setup-env

#1.Install kubectl on Ubuntu 22.04 LTS

curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version

#2.Install Docker on Ubuntu 22.04 LTS
sudo apt-get install  ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl status docker
sudo groupadd docker
sudo usermod -aG docker $USER && newgrp docker
sudo systemctl enable docker

#3.Install cri-dockerd on Ubuntu 22.04 LTS
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.15/cri-dockerd_0.3.15.3-0.ubuntu-jammy_amd64.deb
sudo dpkg -i cri-dockerd_0.3.15.3-0.ubuntu-jammy_amd64.deb

#4.Install conntrack package on Ubuntu 22.04 LTS
sudo apt-get install -y conntrack

#5.Install crictl package on Ubuntu 22.04 LTS
VERSION="v1.24.2"
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz
sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin
rm -f crictl-$VERSION-linux-amd64.tar.gz

#6.Download and Install Minikube on Ubuntu 22.04 LTS
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube
sudo mv minikube /usr/local/bin/
minikube version

Install containernetworking-plugins:
CNI_PLUGIN_VERSION="v1.6.0"
CNI_PLUGIN_TAR="cni-plugins-linux-amd64-$CNI_PLUGIN_VERSION.tgz" # change arch if not on amd64
CNI_PLUGIN_INSTALL_DIR="/opt/cni/bin"
curl -LO "https://github.com/containernetworking/plugins/releases/download/$CNI_PLUGIN_VERSION/$CNI_PLUGIN_TAR"
sudo mkdir -p "$CNI_PLUGIN_INSTALL_DIR"
sudo tar -xf "$CNI_PLUGIN_TAR" -C "$CNI_PLUGIN_INSTALL_DIR"
rm "$CNI_PLUGIN_TAR"

minikube start --driver=none
kubectl get node

minikube addons enable ingress
minikube addons enable ingress-dns

#7Install Helm - https://helm.sh/docs/intro/install/ 

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version


HINT - Check the ingress service (kubectl -n umbrella get svc), edit the ingress service by adding externalIP to point to the minikube ip:
kubectl edit svc -n ingress-nginx

Example:

  spec:
    clusterIP: 10.101.189.214
    clusterIPs:
    - 10.101.189.214
    externalIPs:
    - 172.29.134.247


