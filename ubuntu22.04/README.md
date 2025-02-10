# Download Cloud Image
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img

# Rename File (Optional)
mv jammy-server-cloudimg-amd64.img 22.04-jammy-server-cloudimg-amd64.img

# Build image
docker build -t kubevirt-images:ubuntu22.04 .

# Push image to docker.io
docker login
docker tag kubevirt-images:ubuntu22.04 username_docker/kubevirt-images:ubuntu22.04
docker push username_docker/kubevirt-images:ubuntu22.04

# Test image - change image in vm.yaml 
kubectl apply -f vm.yaml
