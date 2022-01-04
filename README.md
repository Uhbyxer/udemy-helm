# docker

docker run -p 5000:5000 in28min/todo-rest-api-h2:1.0.0.RELEASE

Detach
docker run -p 5000:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE

Restart policy Always
docker run -p 5000:5000 -d --restart=always in28min/todo-rest-api-h2:1.0.0.RELEASE

docker stop 4eb0eccf50f5
docker container pause 4eb0eccf50f5

Stop (gracefuly)
docker container stop 4eb0eccf50f5

Kill (immediately stop)
docker container kill 4eb0eccf50f5

Remove stopped containers
docker container prune
All
docker container prune -a

21bea6f940269a510e8437bdccdd3f4362ed6316d98840a99860d81c461f688d

Logs
docker logs 21bea6f

Follow logs
docker logs -f 21bea6f

Add Tag
docker tag in28min/todo-rest-api-h2:1.0.0.RELEASE in28min/todo-rest-api-h2:latest

History / inspect
docker image history f8049a029560
docker i3mage inspect f8049a029560

Events
docker events

Top / Stats
docker top 21bea6f94026
docker stats

Info about all details
docker system df

=================================================
Azure EKS

Create Azure Resource Group
az group create --name kubernetes-resource-group --location westeurope

Create Azure Service Principal
az ad sp create-for-rbac --skip-assignment --name kubernetes-cluster-service-principal
RS:
{
"appId": "2ac4f3bc-0333-46f3-9e81-4f0ca71ab72b",
"displayName": "kubernetes-cluster-service-principal",
"name": "2ac4f3bc-0333-46f3-9e81-4f0ca71ab72b",
"password": "z1jPvAeo26QKbTw5EKq.BSD338ay4XHD9w",
"tenant": "b5e3ff4e-3b00-42ee-b5e5-ca64d68620f6"
}

Create Cluster
az aks create --name in28minutes-cluster --node-count 4 --enable-addons monitoring --resource-group kubernetes-resource-group --vm-set-type VirtualMachineScaleSets --load-balancer-sku standard --enable-cluster-autoscaler  --min-count 1 --max-count 7 --generate-ssh-keys --service-principal 2ac4f3bc-0333-46f3-9e81-4f0ca71ab72b --client-secret  z1jPvAeo26QKbTw5EKq.BSD338ay4XHD9w
RS:
SSH key files 'C:\Users\UX502712\.ssh\id_rsa' and 'C:\Users\UX502712\.ssh\id_rsa.pub' have been generated under ~/.ssh to allow SSH access to the VM. If using machines without permanent storage like Azure Cloud Shell without an attached file share, back up your keys to a safe location
Resource provider 'Microsoft.OperationalInsights' used by this operation is not registered. We are registering for you.

Less resources
az aks create --name in28minutes-cluster --node-count 2 --enable-addons monitoring --resource-group kubernetes-resource-group --vm-set-type VirtualMachineScaleSets --load-balancer-sku standard --enable-cluster-autoscaler  --min-count 1 --max-count 4 --generate-ssh-keys --service-principal 2ac4f3bc-0333-46f3-9e81-4f0ca71ab72b --client-secret  z1jPvAeo26QKbTw5EKq.BSD338ay4XHD9w


Get creds for kubectl
az aks get-credentials --resource-group kubernetes-resource-group --name in28minutes-cluster
Check
kubectl version

Get all contexts
kubectl config get-contexts

Get namespaces
kubectl get ns

Get nodes
kubectl get nodes

Get services
kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.0.0.1     <none>        443/TCP   10m

Create deployment
kubectl create deployment hello-world-rest-api --image=in28min/hello-world-rest-api:0.0.1.RELEASE
kubectl create deployment todowebapp-h2 --image=in28min/todo-web-application-h2:0.0.1-SNAPSHOT

Expose on external port
kubectl expose deployment hello-world-rest-api --type=LoadBalancer --port=8080
kubectl expose deployment todowebapp-h2 --type=LoadBalancer --port=8080

Port forwarding (if not load balancer)
kubectl port-forward myapp01-6464ccd48-8xd5z 8077:80

Terminal into pod
kubectl exec -it myapp08-redis-master-0 redis-cli

Watch
kubectl get svc --watch


Apply configs from yaml
kubectl apply -f mysql-database-data-volume-persistentvolumeclaim.yaml,mysql-deployment.yaml,mysql-service.yaml
kubectl apply -f config-map.yaml,secret.yaml,todo-web-application-deployment.yaml,todo-web-application-service.yaml

Wide
kubectl get deployment -o wide

Delete all by labels:
kubectl delete all -l app=hello-world-rest-api
kubectl delete all -l app=todowebapp-h2
kubectl delete all -l io.kompose.service=todo-web-application
kubectl delete all -l io.kompose.service=mysql

------------------
Install Ingress

Configure Helm Repo
helm repo add stable https://charts.helm.sh/stable

Install ingress controller
helm install stable/nginx-ingress --namespace default --set controller.replicaCount=1 --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux --generate-name

Apply ingress
kubectl apply -f 05-currency-conversion-microservice-basic/ingress_azure.yaml

Scale deployment
kubectl scale deployment currency-exchange --replicas=3

Delete stuff
kubectl delete svc currency-conversion
kubectl delete svc currency-exchange
kubectl delete svc nginx-ingress-1583140351-controller
az aks delete --name in28minutes-cluster --resource-group kubernetes-resource-group

=========== Helm
Add repo
helm repo add bitnami https://charts.bitnami.com/bitnami
List of added repos
helm repo list

Search in repos:
helm search repo nginx
for versions
helm search repo --versions nginx

Change namespace in the current k8s context
kubectl config set-context --current --namespace=web

Install nginx
helm install nginx01 bitnami/nginx

Override params (from yaml & cmd line)
helm install nginx03 --values values.yaml --set service.port=8080 bitnami/nginx

Upgrade deployment
helm upgrade nginx01 --values values.yaml --set service.port=8080 bitnami/nginx

Get the list of installs
helm list

Uninstall
helm uninstall nginx01

Keep history
helm uninstall nginx01 --keep-history

Dry run
helm install nginx03 --values values.yaml --set service.port=8080 --dry-run bitnami/nginx

Get release notes
helm get notes nginx01

Manifest (applied changes ) dry run -> changes to be applied
helm get manifest nginx01

Get release history
helm history nginx01

Rollback to revision
helm rollback nginx01 1

Autogenerate release names
helm install bitnami/nginx --generate-name

+ create new namespace
  helm install bitnami/nginx --generate-name -n apps --create-namespace

Upgrade (upsert)
helm upgrade --install nginx05 --values values.yaml --set service.port=8080 bitnami/nginx


--------
Create charts

helm create myapp

Release from the local directory
helm install myapp01 myapp

Package
helm package .

Release from package
helm install myapp02 C:\repo-sandbox\helm\mycharts\myapp\myapp-0.1.0.tgz

Dependencies
helm dependency update .
helm dependency build .

Plugins
helm plugin list

====== Minikube
Get service url
minikube service -n default --url nginx01
======
