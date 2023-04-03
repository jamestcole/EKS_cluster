EKS cluster

One option for creating an EKS cluster is running the following command, after installing the following.
1.AWS CLI
2.kubectl
3.eksctl

`eksctl create cluster --name my-cluster --region eu-west-1 --nodes 3`

The default settings here are not optimal for most purposes which is why we are going to use a file cluster.yaml instead. This must be adjusted to include your own key and the correct path to use ssh as well as the other appropiate settings.

```
apiVersion: eksctl.io/v1alpha5
Kind: ClusterConfig

metadata:
  name: my-cluster1
  region: eu-west-1

nodeGroups:
  - name: ng-1
    instanceType: t2.micro
    desiredCapacity: 2
    volumeSize: 20
    ssh:
      publicKeyPath: my_aws.pub
```

Then this cluster which takes about 15 minutes to set up can be configured to use kubectl

`aws eks update-kubeconfig --region eu-west-1 --name my-cluster`

## Deploying the cluster

The cluster can be deployed with the hello world yaml file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: gcr.io/google-samples/node-hello:1.0
        ports:
        - containerPort: 8080
```

This can be run with the following command

`kubectl apply -f hello-world.yaml`

Finally the application must be exposed

`kubectl expose deployment hello-world --type=LoadBalancer --port=80 --target-port=8080`

To check the external-ip use the following command

`kubectl get services`

by using the external-ip in your browser, the chosen image should now be accessable, for example "hello kubernetes" is displayed by this image.
