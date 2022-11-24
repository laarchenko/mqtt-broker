## Preparation

1. Add mqtt-broker image to your repo. To create image use the following command
```
mvn clean install -DskipTests -Ddockerfile.skip=false
```
then tag image with your repo or custom image name
```
docker tag thingsboard/tb-mqtt-broker YOUR_REPO/IMAGE_NAME 
```
and push it to your private remote repository
```
docker push YOUR_REPO/IMAGE_NAME
```

2. Create k8s secret with your docker creds
   Execute docker login and if login succesfull, check your docker creds: they must be on path ~/.docker/config.json; if path is valid, create secret with the follwoing command:
```
kubectl create secret generic regcred \
    --from-file=.dockerconfigjson=~/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson
```

## Run
If you would like to run thingsboard from zero, use the following command:
```
helm install --create-namespace --namespace=tb --generate-name tboard-complex/ --set broker.imageName="YOUR_DOCKER_IMAGE_NAME" --set databaseSetup.imageName="YOUR_DOCKER_IMAGE_NAME"
```
In case you need just broker, execute the following steps:
1. Create file **myValues.yaml** and fill in it by the structure:
```
broker:
  imageName: "YOUR DOCKER REPO/IMAGE"
databaseSetup:
  imageName: "YOUR DOCKER REPO/IMAGE"
kafka:
  serversUrl: "KAFKA`S SERVICE URL"
postgresql:
  springDataSourceUrl: "DB CONNECTION URL FOR SPRING"
  springDataSourceUsername: "POSTGRES USERNAME"
  springDataSourcePassword: "POSTGRES PASSWORD"
```
2. Install broker with the following command:
```
helm install --namespace="NAMESPACE WHERE POSTGRES AND KAFKA DEPLOYED" --generate-name -f myValues.yaml tboard-standalone/ 
```