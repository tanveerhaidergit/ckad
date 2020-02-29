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

Q. create a service messaging-service to expose the redis deployment in the marketing namespace within the cluster on port 6379

k expose deployment redis --port=6379 --name=messaging=service --namespace marketing


## Run command to create differrent kubernetes objects

k run nginx --image=nginx --restart=Never (Pod) </br> 
k run nginx --image=nginx (Deployment) </br>
k run nginx --image=nginx --restart=OnFailure (Job) </br>
k run nginx --image=nginx --restart=OnFailure --schedule='<Cron-expression'> (Cron Job) </br>


Readiness Probe:

Test for true container readiness to receive traffic and signal to Kubernetes when a container reaches this condition.

Each container in a pod can be configured with its own readiness probe, and the pod is not in a ready state until all containers are ready. Different types of probes are available for configuration:

hitting an HTTP endpoint (httpGet)
checking for a listening TCP port (tcpSocket)
executing a custom script that exits 0 on success (exec: command: - command - argument)


```

httptGet:  

readinessProbe:  
   httpGet:  
     path: /api/ready  
     port: 8080  


TCP test:

readinessProbe:  
 tcpSocket:   
    port: 3306  


Exec command

readinessProbe:  
 exec:     
   command:   
      - cat  
      -  /app/ls  



# Liveness Probes
On a running container, maintain status of a validly running application by configuring liveness probes (livenessProbe) to regularly
check a container's status. Configuration options are the same as readinessProbe.

Define a liveness command

livenessProbe:   
      exec:  
        command:  
        - cat  
        - /tmp/healthy  
      initialDelaySeconds: 5  
      periodSeconds: 5  


livenessProbe:      
      httpGet:  
        path: /healthz  
        port: 8080  
        httpHeaders:  
        - name: Custom-Header  
          value: Awesome  
      initialDelaySeconds: 3  
      periodSeconds: 3  




#Network Policy

https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/

To limit the access to the nginx service so that only Pods with the label access: true can query it, create a NetworkPolicy 
object as follow

apiVersion: networking.k8s.io/v1 
kind: NetworkPolicy 
metadata: 
  name: access-nginx 
spec:  
  podSelector: 
    matchLabels: 
      app: nginx 
  ingress: 
  - from:  
    - podSelector: 
        matchLabels: 
          access: "true" 
          

          NetworkPolicy includes a podSelector which selects the grouping of Pods to which the policy applies. You can see this policy selects Pods 
with the label app=nginx. The label was automatically added to the Pod in the nginx Deployment. An empty podSelector selects all pods 
in the namespace.








