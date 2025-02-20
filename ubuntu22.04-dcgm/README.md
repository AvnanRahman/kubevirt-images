# Preparation
sudo apt install wget libguestfs-tools

# Download Cloud Image or use an image that has a node exporter installed.
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

# Rename File (Optional)
mv jammy-server-cloudimg-amd64.img 22.04-jammy-server-cloudimg-amd64.img

# Resize Image (dcgm +-1 GB)
```
qemu-img resize 22.04-jammy-server-cloudimg-amd64.img +5G
cp 22.04-jammy-server-cloudimg-amd64.img 22.04-jammy-server-cloudimg-expanded.img

virt-resize --expand /dev/sda3 22.04-jammy-server-cloudimg-amd64.img 22.04-jammy-server-cloudimg-expanded.img

virt-filesystems --long -h --all -a 22.04-jammy-server-cloudimg-expanded.img
```
# Install Node Exporter
```
virt-customize -a 22.04-jammy-server-cloudimg-expanded.img \
  --install wget \
  --run-command 'wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz' \
  --run-command 'tar -xzf node_exporter-1.8.2.linux-amd64.tar.gz' \
  --run-command 'mv node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/' \
  --run-command 'rm -rf node_exporter-1.8.2.linux-amd64*'
```
```
virt-customize -a 22.04-jammy-server-cloudimg-expanded.img \
  --run-command 'useradd -r -s /bin/false node_exporter' \
  --copy-in node_exporter.service:/etc/systemd/system/ \
  --run-command 'systemctl enable node_exporter.service'
```

# Install DCGM
```
virt-customize -a 22.04-jammy-server-cloudimg-expanded.img \
  --run-command 'wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb' \
  --run-command 'sudo dpkg -i cuda-keyring_1.1-1_all.deb' \
  --run-command 'sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ /"' \
  --run-command 'sudo apt-get update && sudo apt-get install -y datacenter-gpu-manager'
```

# Install DCGM-Exporter
```

```

# Remove Machine ID
```
virt-customize -a 22.04-jammy-server-cloudimg-expanded.img \
  --run-command 'truncate -s 0 /etc/machine-id'
```

# Build image
docker build -t kubevirt-images:ubuntu22.04-dcgm .

# Push image to docker.io
docker login  
docker tag kubevirt-images:ubuntu22.04-dcgm username_docker/kubevirt-images:ubuntu22.04-dcgm  
docker push username_docker/kubevirt-images:ubuntu22.04-dcgm

# Test image - change image in vm.yaml 
kubectl apply -f vm.yaml
kubectl apply -f svc.yaml
