# ckad Tips and useful commands

# Common Alias

alias k=kubectl </br>
alias kp='kubectl get po' </br>
alias kd='kubectl delete po --force --grace-period=0 ' </br>
alias c=clear </br>

# Context and current namespace

k cofig set-context --current --namespace=test </br>


#Pod

1. k run nginx --image=ngix --restart=Never  </br>
2. k run nginx --image=ngix --generator=run-pod/v1 </br>
3. use the yaml file

k run nginx --image=ngix --restart=Never --dry-run -o yaml > nginxpod.yml

4. create pod with port

k run nginx --image=ngix --restart=Never --port=80 --dry-run -o yaml > nginxpod.yml

k create -f nginxpod.yml
or

k apply -f nginxpod.yml

5. Delete a pod forcefully

k delete pod --force --grace-priod=0 nginx


Replicas


Services:

create a service with nodeport 30080 exposed for access:

k create svc nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml > 1svc.yml


k create svc clusterip nginx --tcp=80:80 --dry-run -o yaml


Add a node port using deployment

k expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yml

$ vim webapp-service.yaml

ports:
  nodePort: 30082

k apply -f webapp-service.yml


## Run command to create differrent kubernetes objects

k run nginx --image=nginx --restart=Never (Pod) </br> 
k run nginx --image=nginx (Deployment) </br>
k run nginx --image=nginx --restart=OnFailure (Job) </br>
k run nginx --image=nginx --restart=OnFailure --schedule='<Cron-expression'> (Cron Job) </br>





