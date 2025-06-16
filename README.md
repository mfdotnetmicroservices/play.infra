# Play Infra

Play Economy Infrastructure components


## Add the GitHub package source 
### For Windows (Powershell)
```powershell
$owner="mfdotnetmicroservices"
$gh_pat="[PAT HERE]"

dotnet nuget add source --username USERNAME --password $gh_pat --store-password-in-clear-text --name github "https://nuget.pkg.github.com/$owner/index.json"
```

## Add the GitHub package source 
### For Mac
```bash
owner="mfdotnetmicroservices"
gh_pat="[PAT HERE]"
dotnet nuget add source --username USERNAME --password "$gh_pat" --store-password-in-clear-text --name github "https://nuget.pkg.github.com/$owner/index.json"
```

### To confirm that the package was made
### For Mac and Powershell
```bash
dotnet nuget list source
```


## Creating the Azure resource group 
### For Windows (Powershell)
```powershell 
$appname="playeconomy"
az group create --name $appname --location eastus 
```

### For Mac
```bash
appname="playeconomy"
az group create --name "$appname" --location eastus
```


## Creating the Cosmos DB account
### For PC
```powershell
$app_cosmosdb_account="playeconomy-cosmosdb-account"
$appname="playeconomy"
az cosmosdb create --name $app_cosmosdb_account --resource-group $appname --kind MongoDB
```

### For Mac
```bash
app_cosmosdb_account="playeconomy-cosmosdb-account"
appname="playeconomy"
az cosmosdb create --name $app_cosmosdb_account --resource-group $appname --kind MongoDB
```


## Creating the Service Bus namespace
### For windows
```powershell 

az servicebus namespace create --name $appname --resource-group $appname --sku Standard

```

### For mac
```bash

appnamenamespace="playeconomy-servicebus-namespace"
appname="playeconomy"
az servicebus namespace create --name "$appnamenamespace" --resource-group "$appname" --sku Standard

```


## Creating the Container Registry 
### PC
```powershell
$appnameAcr ="playeconomyacr"    
$appname="playeconomy"
az acr create --name $appnameAcr --resource-group $appname --sku Basic

```

### Mac
```bash
appnameAcr="playeconomyacr"    
appname="playeconomy"
az acr create --name $appnameAcr --resource-group $appname --sku Basic
```

## Creating the AKS cluster 
### For Windows

```powershell
$appnameAcr="playeconomyacr"   
$appnameRg="playeconomy"
$appnamecluster = "playeconomy_cluster"
az aks create -n $appnamecluster -g $appnameRg --node-vm-size Standard_B2s --node-count 2 --attach-acr $appnameAcr --enable-oidc-issuer --enable-workload-identity --generate-ssh-keys


az aks get-credentials --name $appnamecluster --resource-group $appnameRg
```

### For mac
```bash
appnameAcr="playeconomyacr"
appnameRg="playeconomy"
appnamecluster="playeconomy_cluster"
az aks create -n "$appnamecluster" -g "$appnameRg" --node-vm-size Standard_B2s --node-count 2 --attach-acr "$appnameAcr" --enable-oidc-issuer --enable-workload-identity --generate-ssh-keys


az aks get-credentials --name "$appnamecluster" --resource-group "$appnameRg"
```

## Creating the Azure Key Vault
### For Windows

```powershell 
$appnamekv="playeconomy-key-vault"
$appnameRg="playeconomy"
az keyvault create -n $appnamekv -g $appnameRg 
```

### For Mac

```bash
appnamekv="playeconomy-key-vault"
appnameRg="playeconomy"
az keyvault create -n "$appnamekv" -g "$appnameRg"

```

## Installing Emissary-ingress
```powershell 
$namespace_dns_name="playeconomy_dns_service"
helm repo add datawire https://app.getambassador.io
helm repo update

kubectl create namespace emissary && \
kubectl apply -f https://app.getambassador.io/yaml/emissary/3.9.1/emissary-crds.yaml

kubectl wait --timeout=90s --for=condition=available deployment emissary-apiext -n emissary-system

$namespace="emissary"
helm install emissary-ingress datawire/emissary-ingress --set service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"= --namespace $namespace_dns_name --create-namespace

kubectl -lapp.kubernetes.io/instance=emissary-ingress deploy --timeout=90s --for condition=available -n $namespace wait
```

```bash


namespace_dns_name="playeconomy-dns-service"
helm repo add datawire https://app.getambassador.io
helm repo update

kubectl create namespace emissary && \
kubectl apply -f https://app.getambassador.io/yaml/emissary/3.9.1/emissary-crds.yaml

kubectl wait --timeout=90s --for=condition=available deployment/emissary-apiext -n emissary-system

namespace="emissary"
helm install emissary-ingress datawire/emissary-ingress --set service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="$namespace_dns_name" --namespace "$namespace" --create-namespace

kubectl -n "$namespace" wait --for condition=available --timeout=90s deploy -lapp.kubernetes.io/instance=emissary-ingress


```


## Configuring Emissary-ingress routing
```powershell 

$namespace="emissary"

kubectl apply -f .\emissary-ingress\listener.yaml -n $namespace 
```

```bash
namespace="emissary"

kubectl apply -f ./emissary-ingress/listener.yaml -n "$namespace"
kubectl apply -f ./emissary-ingress/mappings.yaml -n "$namespace"
```

## Installing cert-manager
```powershell

namespace="emissary"

helm repo add jetstack https://charts.jetstack.io --force-update
helm repo update 

helm install cert-manager jetstack/cert-manager --version v1.18.0 --set crds.enabled=true --namespace "$namespace"
  
```

## Creating the cluster issuer
```bash
namespace="emissary"
kubectl apply -f ./cert-manager/cluster-issuer.yaml -n "$namespace"
kubectl apply -f ./cert-manager/acme-challenge.yaml -n "$namespace"
```


## Creating the tls certificate 
```bash
namespace="emissary"
kubectl apply -f ./emissary-ingress/tls-certificate.yaml -n "$namespace"
```

## Enabling TLS and HTTPS
```bash

namespace="emissary"
kubectl apply -f ./emissary-ingress/host.yaml -n "$namespace"
```