First, We need to check the requirements are installed or not:
The requirements are minikube, Helm, Kubectl and also docker desktop
Install the Helm bitnami repository
helm repo add bitnami https://charts.bitnami.com/bitnami
and then update the repository -
helm repo update
we need to install redis and postresql ( we are using a bitnami repository)
To install redis
helm install redis bitnami/redis       # it can create 4 pods 
helm install redis bitnami/redis --set architecture=standalone --set auth.enabled=false 
Above command create a standalone pod
To install Postgresql
helm install postgres bitnami/postgresql
 
after installing redis and postgresql check the pods and service
To check
kubectl get pods
kubectl get service
 
after installing download argocd – create a new namespace named argocd to segregate 
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
After installing argocd get the password for argocd -
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
copy the encoded output and paste 
[System.Text.Encoding]::UTF8.GetString(
[System.Convert]::FromBase64String("cFJJaGxXVUVITmEwdXJtUA==")
)
After that expose the argocd-server service to port 8080
kubectl port-forward svc/argocd-server -n argocd 8080:443
after expose copy the link and paste it to your browser
https://localhost:8080
 
This is the output of argocd
 
To generate API key we need to edit the confiq yaml file 
kubectl -n argocd edit configmap argocd-cm
data:
  accounts.admin: apiKey
kubectl rollout restart deployment argocd-server -n argocd
and then expose using expose command
First create a application in ARGOCD and after that create one repo in github
This is my repository URL: https://github.com/misbar1802/argocd-demo


 
After creating Github repo write deployment.yaml and service.yaml from your local machine and push the code to that repo, I used nginx image and 2 replicas and service using nodeport.
 
Once you pushed the code to github repo it will trigerred and deployed on the cluster.
 
 
If any changes happens in the code it will instantly reflect in the cluster like increasing pods or change image like that.
 
