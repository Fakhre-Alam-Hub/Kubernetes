# Kubernetes

### Create AWS EC2 instance

Download file `create-ec2-instance.yml`

a. Go to AWS Cloudformation.

b. Create new stack and import the file `create-ec2-instance.yml`.

c. Two EC2 instances will be created by the cloudformation template one master and one slave.



### Follow below steps to install kubelet kubeadm kubectl

Ways to install k8s

Kubeadm, Minikube, Kops, K8s in gcp


**Step1:**

**On Master & worker node**

```
sudo su

apt-get update  

apt-get install docker.io -y

service docker restart  

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

sudo apt update

sudo apt install -y kubelet kubeadm kubectl
```

**Step2:**

**On Master:**

  ```kubeadm init --pod-network-cidr=192.168.0.0/16```
   
   >Copy the token and paste it into the worker node.
   
**Step3:**

**On Master:**

```
mkdir -p $HOME/.kube
    
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**step4:**

**On Master:**

```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
```


Our Kubernetes installation and configuration are complete
