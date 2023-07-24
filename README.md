# Voting-Result-K8s
App/Pods/Service/Deployment
# Example Voting App

A simple distributed application running across multiple Docker containers.

## Getting started

Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

This solution uses Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:5000](http://localhost:5000), and the `results` will be at [http://localhost:5001](http://localhost:5001).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Run the app in Kubernetes

The folder k8s-specifications contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) which collects new votes
* A [.NET](/worker/) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
* A [Node.js](/result) web app which shows the results of the voting in real time

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.

Microservices Architecture
 

Run:
docker run -d --name=redis redis
docker run -d --name=db postgres:9.4
docker run -d --name=vote -p 5000:80 voting-app
docker run -d --name=result -p 5001:80 result-app
docker run -d --name=worker worker
After running this application you have to connect them so they will work together.    
 

DOCKER RUN --LINKS
docker run -d --name=vote -p 5000:80  --link redis:redis voting-app
docker run -d --name=result -p 5001:80 –link db:db  result-app
docker run -d --name=worker --link db:db  --link redis:redis worker

Next
 
As you know we can not deploy containers directly into Kubernetes.
First we have to create PODS and place the containers that contains applications into PODS.
 

So, now you have to make redis to listen to voting-app. Voting-app can not use the IP address 6379 to speak to redis app. Because IP addresses will be changing every time when the pod is updated or recreated or scaled. 
So, this is when Service comes to the stage. Service is like a door. 

Once you write the in yaml file all the connections, like this
 

You now can run them in the terminal  of vscode.
First check to make sure that no irrellivant pods or services are running – kubectl get pods,svc 
kubectl create -f voting-app-pod.yaml
kubectl create -f voting-app-service.yaml
kubectl get pods,svc
now, to know if it is opening on the port 30004, run 
minikube service voting-service --url
and it will give you the url numbers, which you can open in internet to see if it is opening. 
kubectl create -f redis-pod.yaml
kubectl create- f redis-service.yaml


kubectl create -f postgres-pod.yaml
kubectl create- f postgres-service.yaml

kubectl create -f worker-app-pod.yaml

kubectl create -f result-app-pod.yaml
kubectl create- f result-service.yaml

MULTIPLICATING THE VOTING-APP AND THE RESULT-APPP WITH THE HELP OF REPLICA
In other words replicating the apps.
 

And in or der to have multiple pods we need to have a deployment. 
ls ltr  will show you what .yaml files have been created
then you replicate the voting and result pods in deployment by just running –
	kubectl scale deployment voting-app-deploy –replicas=3 
All the coding that was done in yaml format is in this link of github. You can just copy and paste it to you of Github.  
example-voting-app/k8s-specifications at master · kodekloudhub/example-voting-app (github.com)
