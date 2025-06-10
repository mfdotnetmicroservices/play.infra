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