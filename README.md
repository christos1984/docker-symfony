# Docker Symfony Version

An initial repo of a PoC version of Symfony Framework on docker

## Getting Started

1. If not already done, [install Docker Compose](https://docs.docker.com/compose/install/)
2. Run `docker-compose up -d --build` to build fresh images and start the app (if you omit -d the logs will be displayed in the current shell)
3. Open `https://localhost:8080` in your favorite web browser
4. Run `docker-compose down --remove-orphans` to stop the Docker containers.

## Database support

Since DB is exposed via port-forwarding, you can access it from your local machine at `localhost:4306`, so you can use your favourite DB client program. Credentials can be found inside the `docker-compose` file in the appropriate section.

## Deployment with Minikube

First of all, [install Minikube](https://minikube.sigs.k8s.io/docs/start/) for your OS.
WARNING: use [this Minikube](https://github.com/kubernetes/minikube/issues/13736#issuecomment-1063470210) version if you are using Windows/WSL because of issues of the previous one

1. Build the php image with `docker build -t mts/php -f php/Dockerfile_prod .`
2. We need a registry to upload the images, in this example we will use the local registry that we can use with minikube. Issue 
```
minikube addons enable registry
``` 
and then, in two terminals issue 
```
docker run --rm -it --network=host alpine ash -c "apk add socat && socat TCP-LISTEN:5000,reuseaddr,fork TCP:host.docker.internal:5000"
``` 
and in another 
```
kubectl port-forward --namespace kube-system service/registry 5000:80
``` 
(instructions might be different for your OS, please refer to [official documentation](https://minikube.sigs.k8s.io/docs/handbook/registry/))

Now you have the registry running at `localhost:5000` and thus, you should tag your images like this:
```
docker tag mts/php localhost:5000/mts_php
docker push localhost:5000/mts_php
```

If you have another external registry then start minikube with the `--insecure-registry` param (refer to documentation)

## Deployment into Minikube

With your local `kubectl` binary configured to the minikube cluster, apply `kubectl -f apply k8s/` and all the k8s descriptors will be uploaded to the cluster. Then, try to `minikube service nginx` and the symfony page should appear. If it doesn't please make sure you are using the updated binary of the minikube mentioned above