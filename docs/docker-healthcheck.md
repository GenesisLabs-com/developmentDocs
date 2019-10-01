Docker health checks is a cute little feature that allows attaching shell command to container and use it for checking if container’s content is alive enough.
# Add HealthCheck Command

We can add Healthcheck in Dockerfile and with run command too

```
docker run -d  --name server --rm -p 8080:8080 -p 8081:8081 server
HEALTHCHECK --interval=5s --timeout=3s --retries=3 CMD curl --fail https://www.google.com/ || exit 1
```

# Inspect Healthcheck 

```
sleep 2; docker inspect --format='{{json .State.Health}}' explorer
```

# The Pod that will Monitor Healthcheck

```
docker run -d \
    --name autoheal \
    --restart=always \
    -e AUTOHEAL_CONTAINER_LABEL=all \
    -v /var/run/docker.sock:/var/run/docker.sock \
    willfarrell/autoheal
```

a) Apply the label autoheal=true to your container to have it watched.

b) Set ENV AUTOHEAL_CONTAINER_LABEL=all to watch all running containers.

c) Set ENV AUTOHEAL_CONTAINER_LABEL to existing label name that has the value true.
For More Information "https://hub.docker.com/r/willfarrell/autoheal/"

# Healthcheck in Explorer Staging

docker run -d   -e ROOT_URL=http://localhost/   -e MONGO_URL='mongodb://rnssol:rnssol123@18.216.213.191:27017/colorplatform'   -e METEOR_SETTINGS="$(cat settings.json)"   -p 3000:3000  --name explorer --build-arg APT_GET_INSTALL="curl wget" --build-arg NODE_VERSION=4.8.1  --health-cmd="curl --fail 127.0.0.1:3000 || exit 1" --health-interval=2s -health-interval=2s --health-timeout=2s  rnssolutions/explorer:0.1.2