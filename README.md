# docker-registry-k8s

Docker Registry as k8s deployment

## Install

In the folder were you have all .yaml files run the following command:

```
kubectl apply -k .
```

If it does not work for minikube then try:

```
minikube kubectl apply -k .
```

If it does not work for microk8s then try:

```
microk8s kubectl apply -k .
```

You should see in the `default` namespace defined for kubernetes all the new objects.
The corresponding service is called `docker-registry`.

If you want to use it in a different namespace then just add "-n &lt;namespace&gt;" as argument to kubectl call.

You need to use a proxy in front of it (like nginx) to redirect to the exposed port on k8s node.
To find the mapped ports for port 80 and 5000 just check the "Internal Endpoints" of the service "docker-registry"
or run `kubectl get svc` from the shell of the server running the cluster.

## Customization

You must customize some values in `docker-registry-env-configmap.yaml` and `ConfigMap docker-registry-config` in `docker-registry-deployment.yaml`.

## Authentication

Create a file holding your username and password with:

```
htpasswd -Bc /path/to/auth/registry.password myuser
```

## Accessign the docker registry

The ui is at `/` and the registry at `/v2/`.

## How to install docker cli

Execute the following commands to install the command-line version of docker:

```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker-archive-keyring.gpg
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce-cli
docker --version
sudo usermod -aG docker $USER
```

## How to add an image to our registry from a different registry

In case you want to use the public docker registry, replace `source-registry-url` with `hub.docker.com`.

```
docker login <source-registry-url>
docker pull <source-registry-url>/<image-name>:<tag>
docker tag <source-registry-url>/<image-name>:<tag> <target-registry-url>/<image-name>:<tag>
docker login <target-registry-url>
docker push <target-registry-url>/<image-name>:<tag>
```

In case of error try the following solution:

```
echo 'export DOCKER_HOST="tcp://localhost:2375"' >> ~/.bashrc
source ~/.bashrc
docker run hello-world
```
