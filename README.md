# Kubernetes-handsOn
---------------------------------
This is a Kubernetes Project, I created  that gives a solid hands-on experience with writing yaml manifests for Pods, Replicasesets, Deployments, Services etc. and at end,

 Deploying a Voting App in microservices (vote, result, redis, postgres, worker api) via pods-services
 
 Deploying a Voting App in microservices(vote, result, redis, postgres, worker api) via deployments-services
---
Concepts & commands:
# apiVersion: v1 - for   pod, service
 
# apiVersion: apps/v1 - for   replicaset, deployment
 
 
#  kubectl apply/create -f fileName/Replicaset.yaml    - create and deploy Replicaset.
 
#  kubectl scale  --replicas=6 -f fileName/Replicaset.yaml  - scale no. of Replicaset.
 
# kubectl scale --replicask=6  rs  myapp-replicaset- scale no. of replicas via type & name.
 
# In terminal, kubectl edit rs myapp-replicaset
 
 # kubectl create/apply -f filename/ service.yaml  -create svc
 
# kubectl get svc - list svc
# curl http://nodeIP:nodePort  - to access the svc (web server)
 
 # sevice name is important, catioud as it auto sets the DNS entry
 
  # servicePort - generally keep same as targetPort
 
  # target/container/pod/backend Port
 
# kubectl apply -f .    this create and deploy all manifests from current directory
-----------------------------------------------------#
Deploying voting app via Pods-services
   #- first create all the pods definition and services manifest to setup communication among them,
   specification:
   vote api - frontend, exposed externally (allow user to cast vote)
   redis api - database (take user vote input and store   in database)
   worker api - backend (fetch value from redis and update the postgres db in real time env)
   postgres api - database (get the values updated by worker and pass to the result app)
   result api - frontend, exposed externally (show result to the users)
 
    once done, hit below commands:
   # kubectl apply -f .
   # kubectl get po
   # kubectl get svc
 
    ### To access application:
   # kubectl get node -o wide   #pick nodeIP, 80:3001 - podPort:nodePort
   #In google, nodeIP:nodeport access  vote and result app
   # vote app- nodeIP:vote_nodePort and
   # result app - nodeIP:result_nodePort
 
----------------------------------------------------------
Deploying voting app via Deployments-services
Qus. Why with Deployments-services?
Ans- Because there's a limitation with pods-servuces:
       1. can't easily scale up/down
       2. If any instace go down, it can't auto restart  and deploy
       3. can't easily upgrade api, first delete the current version then create new one and deploy - leads to api  go down meantime.
    so,  Replicasets and deployments solves these problems as:
       1. it scale up/down easily (scale --replicas=3)
       2. it auto create and deploy pods if goes down (monitors) no impact.
       3. it can easily upgrade api via inbuilt (rolling update) no downtime, upgrade one at a time.
 
   #- first create all the pods and services manifest,
        #Note - So when you move from a pod to a deployment, it  doesn't matter as long as the labels are okay. so copy/paste all services to deployment-service folder as it is.
        Everything should be good.
 
     once done, hit below commands:
     kubectl apply -f .
     kubectl get svc
     kubectl get deploy
     kubectl get rs
     kubectl get pods
     kubectl get all - to see everything (pods, svc, deployments, replicasets)
 
     ### To access the application:
   # kubectl get node -o wide   #pick nodeIP, 80:3001 - podPort:nodePort
   #In google, nodeIP:nodeport access  vote and result app
   # vote app- nodeIP:vote_nodePort and
   # result app - nodeIP:result_nodePort
 
   To scale:
    let say scale vote api with 5 instance/pod
    kubectl scale deployment myapp-vote --replicas=5
 
---------------------------------------------------------#
Kubernetes on Azure (AKS):
    1. login to Azure account
    2. Come to your Account/subscription (create if not created)
    3. Search AKS > open Kubernetes services > Add Kubernetes cluster - it will open screen to create Kubernetes cluster.
    4. fill/Add subscription, Resource group, cluster name, kubernetes version, node size(ideal keep 1), under Authentication - set service principal to create new + Review/create. it will create and deploy cluster with 1 node.
    5. Once cluster deployed and ready, search and open the resource (cluster name -ex... voting-app) that you created.
    6. To access the cluster, Open cloud shell > prompt to create storage > Refer the official Azure AKS documentation guide for the command - To launch Kubectl in in cloud shell.(Kubectl pre-installed in cloud shell).
    7. az aks get-credentials --resource-group myResourceGroup --name voting app         (it launch kubectl in shell)
    8. verify with Kubectl get node  
    9. git clone --https://url  (clone voting app Project)
    10. cd directory (where all yaml files are present)
    11. kubectl create/apply -f .
    12. kubectl get deployments, svc
       in svc, we can find the loadBalancer for vote and result app
