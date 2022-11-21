## Installation

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

3. Add image to appropriate YAMLs; change value in path spec.template.spec.containers[.name] to your image location in the next files: tb-broker.yml and database-setup.yml
4. Change directory to parent folder besides this one and execute the following command:
```
helm install --create-namespace --namespace=thingsboard-mqtt-broker broker mqttBroker/
```
