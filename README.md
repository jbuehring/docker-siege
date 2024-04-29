The below represents updated shortcuts for reusably building + using this image at Invoca.  The original README.md content from the upstream repo follows for posterity.

# Build Image
```shell
cd ~/Documents/Git/docker-siege;

docker build . -t jbuehring/docker-siege:latest -f Dockerfile --no-cache --platform linux/amd64;

docker push jbuehring/docker-siege:latest;
```

# Run Locally
```shell
cd ~/Documents/Git/docker-siege;

# The `-v $(pwd):/app` will bind mount your current directory to `/app` within the container, letting you use local files.
docker run -it --rm --entrypoint 'bash' -v $(pwd):/app jbuehring/docker-siege:latest;

# token intentionally obscured
/usr/local/bin/siege --concurrent='5' --time='1m' --file='/app/urls_01.txt' --rc='/app/siege.conf' --header='Authorization: Bearer sLj8G...-fZ5E';
```

# Deploy Kubernetes
```shell
cd ~/Documents/Git/docker-siege;

kubectl --context='orange.staging.us-east-1' --namespace='smsplatform01' apply -f ./docker-siege-deployment.yaml;
```

# Run in Kubernetes
```shell
kubectl --context='orange.staging.us-east-1' --namespace='smsplatform01' exec -it docker-siege-5b8cd766bb-4pc56 -c siege -- bash;

# token intentionally obscured
/usr/local/bin/siege --concurrent='5' --time='1m' --file='/app/urls_01.txt' --rc='/app/siege.conf' --header='Authorization: Bearer sLj8G...-fZ5E';

```

---

[![](https://images.microbadger.com/badges/image/ecliptik/docker-siege.svg)](https://microbadger.com/images/ecliptik/docker-siege "Get your own image badge on microbadger.com")

#Usage
## Running Container as a Command

This is a public version of the Siege container used for load testing at [Demandbase](https://www.demandbase.com).

To run this container using the [ecliptik/docker-siege](https://hub.docker.com/r/ecliptik/docker-siege/) image stored in Docker Hub that is automatically built whenever this github repository is updated. The [Dockerfile](Dockerfile) has a default `ENTRYPOINT` of `siege` and all arguments passed after the container image will pass to `siege`. The default argument is `--help`.

## Running Container With Local Configuration

To skip the bundled configuration files in the image, and use local files in the current directoy, bind mount it to `/app` using the `-v` flag.

For example using this repository and you want to create a URL list to pass to siege called `urls.txt` without having to re-build the container image,

```
docker pull ecliptik/docker-siege
docker run -it --rm -v $(pwd):/app ecliptik/docker-siege -c 50 -f /app/urls.txt -R /app/siege.conf
```

The `-v $(pwd):/app` will bind mount your current directory to `/app` within the container, letting you use local files.

## Running in a Jenkins Job

Runnig this container using the image in Docker Hub as a Jenkins job requires not including the `-t` option, otherwise Jenkins will give an error.

## Updating Docker Image

To update the version fo Siege, find the latest version from the [Siege Downloads](http://download.joedog.org/siege/) page and in the [Dockerfile](Dockerfile) update the `SIEGE_VERSION` variable and re-build,

```
docker build -t ecliptik/docker-siege .
```
