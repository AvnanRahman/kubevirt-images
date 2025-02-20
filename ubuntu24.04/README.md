# Download Cloud Image
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img

# Rename File (Optional)
mv noble-server-cloudimg-amd64.img 24.04-noble-server-cloudimg-amd64.img

# Build image
docker build -t kubevirt-images:ubuntu24.04 .

# Push image to docker.io
docker login  
docker tag kubevirt-images:ubuntu24.04 username_docker/kubevirt-images:ubuntu24.04  
docker push username_docker/kubevirt-images:ubuntu24.04

# Test image - change image in vm.yaml 
kubectl apply -f vm.yaml
