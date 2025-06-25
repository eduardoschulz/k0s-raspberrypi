# k0s-raspberrypi
[![Platform](https://img.shields.io/badge/platform-Raspberry%20Pi%204-red.svg)](https://www.raspberrypi.com/)
[![k0s Version](https://img.shields.io/badge/k0s-stable-green.svg)](https://docs.k0sproject.io/)

## ⚙️ Raspberry Pi setup:
The following configurations provide good performance for the rpi4.
```
#/boot/firmware/config.txt
#overclock configuration
over_voltage=6 
arm_freq=2000
gpu_freq=600

sdram_freq=550

#other stuff
boot_delay=0
dtparam=watchdog=on
gpu_mem=32
```

You also need to add this to be able to use containers.
```
#/boot/firmware/cmdline.txt
cgroup_memory=1 cgroup_enable=memory
```
### 🔄 Update your Pi
```bash
sudo apt update
sudo apt upgrade
```

## 🚀 Installing k0s
```bash
# 📝Check https://docs.k0sproject.io/stable/install/ for up to date information
curl --proto '=https' --tlsv1.2 -sSf https://get.k0s.sh | sudo sh
sudo k0s install controller --single #This creates a single controlplane/worker node
sudo systemctl enable --now k0scontroller
sudo k0s status #may take a while
```

## 🧰 Configure kubectl
```bash
mkdir -p ~/.kube
sudo k0s kubeconfig admin > ~/.kube/config
sudo apt install kubernetes-client
# if you don't want to install kubectl you can alternatively use this:
sudo k0s kubectl
```
## 🧪 Test Deployment: Nginx
Create a deployment and service to test your setup:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```




