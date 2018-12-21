# kafka-helm-kubernetes


This repository will eventually contain scripting to automatically  deploy a kafka cluster to kubernetes.  
At the moment it it just a collection of the manual instructions.

## Pre requisites

Familar with google cloud

## Steps

- Create your kubernetes cluster on Google cloud
- login to your cluster using the cloud console
- Install Helm (install.sh)
``` 
#!/usr/bin/env bash

echo "install helm"
# installs helm with bash commands for easier command line integration
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
# add a service account within a namespace to segregate tiller
kubectl --namespace kube-system create sa tiller
# create a cluster role binding for tiller
kubectl create clusterrolebinding tiller \
    --clusterrole cluster-admin \
    --serviceaccount=kube-system:tiller

echo "initialize helm"
# initialized helm within the tiller service account
helm init --service-account tiller
# updates the repos for Helm repo integration
helm repo update

echo "verify helm"
# verify that helm is installed in the cluster
kubectl get deploy,svc tiller-deploy -n kube-system
```
- `helm repo add confluent https://confluentinc.github.io/cp-helm-charts/`
- `helm repo update`
- `helm install --set cp-schema-registry.enabled=false,cp-kafka-rest.enabled=false,cp-kafka-connect.enabled=false confluent/cp-helm-charts`

Thats It!

## Verify Installation

- `kubectl exec -c cp-kafka-broker -it operatic-rottweiler-cp-kafka-0 -- /bin/bash /usr/bin/kafka-console-producer --broker-list localhost:9092 --topic test`
- Type some text to send some messages
- `kubectl exec -c cp-kafka-broker -it operatic-rottweiler-cp-kafka-0 -- /bin/bash  /usr/bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning`




## Source Material

The steps detailed above are taken from the sources below.  

- https://medium.com/google-cloud/installing-helm-in-google-kubernetes-engine-7f07f43c536e
- https://www.confluent.io/blog/getting-started-apache-kafka-kubernetes/
- https://github.com/confluentinc/cp-helm-charts/tree/master/charts/cp-kafka
- https://docs.confluent.io/current/installation/installing_cp/cp-helm-charts/docs/index.html
