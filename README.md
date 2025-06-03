# Play Infra

Play Economy Infrastructure components


## Add the GitHub package source 
### For Windows (Powershell)
```powershell
$owner="mfdotnetmicroservices"
$gh_pat="[PAT HERE]"

dotnet nuget add source --username USERNAME --password $gh_pat --store-password-in-clear-text --name github "https://nuget.pkg.github.com/$owner/index.json"
```

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