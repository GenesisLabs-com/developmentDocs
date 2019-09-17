# Deployment of explorer on k8s


## create secrets

To pass environnement variables to explorer container we have to pass secrets to k8s.

run this command
```
kubectl create secret generic explorer-settings --from-file=./settings.json --insecure-skip-tls-verify
```

Secrets of mongodb url and root rrl are added in yaml file. For this run following command

```
kubectl apply -f ./secrets.yaml --insecure-skip-tls-verify
```

to start deployment run following commands

```
kubectl delete -f recipes/explorer.yaml --insecure-skip-tls-verify
```


## Docker Deployment of explorer Production

```
docker run -d   -e ROOT_URL=http://localhost/   -e MONGO_URL='mongodb://admin123:admin123@ds359077.mlab.com:59077/colorplatform'   -e METEOR_SETTINGS="$(cat settings.json)"   -p 80:3000   rnssolutions/explorer:release1

```

## To build explore in production 

following site is reference

```
https://github.com/jshimko/meteor-launchpad
```
environment variable 
```
   export MONGO_URL='mongodb://admin123:admin123@ds359077.mlab.com:59077/colorplatform'
   export ROOT_URL='localhost'
```

## To build docker file

```
docker run -d \
  -e ROOT_URL=http://localhost/ \
  -e MONGO_URL='mongodb://admin123:admin123@ds359077.mlab.com:59077/colorplatform' \
  -e METEOR_SETTINGS="$(cat settings.json)" \
  -p 80:3000 \
  rnssolution/explorer
  
```