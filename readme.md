
# login using az cli
az login

# create a resouirce group

az group create -n scresgp -l westeurope

# create Azure container Registry (ACR)
az acr create -n sgacr1001 -g sgresgp --admin-enabled false --sku Basic

-n is name of ACR, this will be a DNS name for the reositories so must be unique across Azure
, -g is resource-group --admin-enabled false , means the image in acr will be used for private deployment --sku is the plan (pricing)

The login server name will be sgacr1001.azurecr.io

# since the AKS Needs the ACR ID / name to fetch images, we must store this ID into some varible

ACR_NAME=$(az acr show -n sgacr1001 -g sgresgp --query id -o tsv)

# push the image to ACR
# Tag the image  to ACR Login Server name
docker tag firstappforaks:0.0.1 sgacr1001.azurecr.io/firstappforaks:0.0.1
# login on the ACR
az acr login -n sgacr1001
# push image on ACR
docker push sgacr1001.azurecr.io/firstappforaks:0.0.1


# create aka
az aks create -n aks-sc1001 -g sgresgp --enable-managed-identity --attach-acr $ACR_NAME --generate-ssh-key --node-count 2

# install aks cli for running "aks" command line tools and "kubectl" command
az aks install-cli

# download the AKS cluster configuration non local to get credentials (principal)
# so that images will be successfully deployed and will be available for public access

az aks get-credentials -n aks-sc1001 -g sgresgp

# get kubernetes context (This wil make sure that "kubectl" is working)
# list all aks configured with your local environment
kubectl config get-contexts

# run the following command to make sure that all the kubctl oeprations are working 
# for the specified aks cluster from your machine

kubectl config use-context aks-sc1001

# deploy the artifacts on AKS
kubectl apply -f pod.yaml
kubectl apply -f service.yaml

# get pods
kubectl get pods
# get service endpoint
kubectl get endpoints

# wait for the servic to provide public external IP
kubectl get svc -w
