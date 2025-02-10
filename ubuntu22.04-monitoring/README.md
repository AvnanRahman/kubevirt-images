# Download Cloud Image
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

# Rename File (Optional)
mv jammy-server-cloudimg-amd64.img 22.04-jammy-server-cloudimg-amd64.img

# Install Node Exporter
apt install libguestfs-tools
```
virt-customize -a 22.04-jammy-server-cloudimg-amd64.img \  
  --install wget \  
  --run-command 'wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz' \  
  --run-command 'tar -xzf node_exporter-1.8.2.linux-amd64.tar.gz' \  
  --run-command 'mv node_exporter-1.8.2.linux-amd64/node_exporter /usr/local/bin/' \  
  --run-command 'rm -rf node_exporter-1.8.2.linux-amd64*'  
```
```
virt-customize -a 22.04-jammy-server-cloudimg-amd64.img \  
  --run-command 'useradd -r -s /bin/false node_exporter' \  
  --copy-in node_exporter.service:/etc/systemd/system/ \  
  --run-command 'systemctl enable node_exporter.service'
```
```
virt-customize -a 22.04-jammy-server-cloudimg-amd64.img \
  --run-command 'truncate -s 0 /etc/machine-id'
```

# Build image
docker build -t kubevirt-images:ubuntu22.04 .

# Push image to docker.io
docker login  
docker tag kubevirt-images:ubuntu22.04 username_docker/kubevirt-images:ubuntu22.04  
docker push username_docker/kubevirt-images:ubuntu22.04

# Test image - change image in vm.yaml 
kubectl apply -f vm.yaml
