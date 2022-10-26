# Azure Key vault secrets automation & integration in DevOps pipelines

Created: September 27, 2022 8:21 PM
Finished: No
Source: https://rollendxavier.medium.com/automate-secrets-to-azure-key-vault-and-access-it-in-devops-pipelines-69a24ecb9602

How to use Terraform to push secrets into your key vault and later use it across your DevOps application pipelines.

![Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1Nu8Svglxf-kieKkL-vww4Q.jpeg](Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1Nu8Svglxf-kieKkL-vww4Q.jpeg)

Photo by [Jason Dent](https://unsplash.com/@jdent?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/vault?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Requirements

1. A Service Principal (an App registration) with a secret.
2. An Azure Key Vault with an Access Policy for the service principal
3. A service connection in your Azure DevOps Project
4. KeyVault task inside your YAML pipeline scripts

# 1. Create a Service principal with a secret.

The initial step for us is to create a service principal through azure CLI and below are the basic commands to follow.

**Create an Azure Active Directory application**

```
# name for the azure ad application
appName="project_serviceprincipal"# create an Azure AD application
az ad app create \
 --display-name $appName \
 --homepage "http://localhost/$appName" \
 --identifier-uris [http://localhost/$appName](http://localhost/$appName)
```

- `-homepage` and `-identifier-uris` values do not necessarily have to be reachable URIs.

**Create the Service Principal**

Next is to create the service principal with a “contributor” RBAC role

```
sp_password="password123"
subscription_id=$(az account show --query id -o tsv) rg_name="your_resource_group_name" az ad sp create-for-rbac --name $appId --password $sp_password \                       --role contributor \
--scopes /subscriptions/$subscription_id/resourceGroups/$rg_name
```

> Once after running this query, az will generate output including a password key, and make sure you keep it stored for later use, as you cannot retrieve it again.
> 

# 2. Create an Azure Key Vault with Terraform

We can use Terraform to provision the Key Vault and the requirements are

*“Key Vault can be accessed from selected virtual network, trusted azure services and from trusted internet IP’s.”*

So while you create your Key Vault, you can use `azurerm_client_config` to read your current service principal detail used to run your Terraform code (you can use the previously created service principal).

Inside the `network_acls` we have made `default_action`to “**Deny**”, so now the KeyVault is private to your network, and have to add your build agent subnet id to access the vault. But the trouble with that is your DevOps is not a trusted Azure Service yet and has to add your DevOps region IP address also (in this case South United States”) to the `ip_rules` to get Key Vault access in DevOps pipelines. You can get your DevOps region IP details here [Allowed address lists and network connections — Azure DevOps | Microsoft Docs](https://docs.microsoft.com/en-us/azure/devops/organizations/security/allow-list-ip-url?view=azure-devops&tabs=IP-V4)

Finally, `access_policy`which is an important parameter where you will assign service principal access to the key vault, else you cannot add or list any secrets using the service principal

The above sample code is inserting a secret “kv_secret” to the key vault (we have assigned the “Create” role to the terraform service principal) and you can make it more efficient by adding your resource-specific secrets such as storage account key, app insights API key, etc.

# 3. Create a service connection in Azure DevOps

The next step is to create a service connection which we will later use in the Azure DevOps Pipeline to access the Key Vault

1. Lon into your organization (`https://dev.azure.com/{yourorganization}`) and select your project.
2. Select Project settings > Service connections.
3. Select + New service connection, select the type of service connection that you need and then select Next.
4. We will choose “Service Principal (manual)” rather than the recommended one, since we have to provide our service principal details, after click Next

![Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1-bsTCMRXri-fjDT2Orv-Mg.png](Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1-bsTCMRXri-fjDT2Orv-Mg.png)

5. Here you will provide your Subscription details + Service Principal Id & the “Password Key” we created & saved in our step 1

![Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1yfjHX8ocwajOOrNbm_-aKA.png](Azure%20Key%20vault%20secrets%20automation%20&%20integration%20i%20135fb74561204ca19fcbac3177b5c3c2/1yfjHX8ocwajOOrNbm_-aKA.png)

# 4. Use the KeyVault task to read the secrets inside Pipeline

Use the `AzureKeyVault@2` task to download the secrets inside your application pipelines. We have to provide the previously created service connection & Key Vault name to get access to the vault

```
# Azure Key Vault
# Download Azure Key Vault secrets
- task: AzureKeyVault@2
inputs:
connectedServiceName: "my_service_connection"
keyVaultName: "example_keyvault"
secretsFilter: '*' # Downloads all secrets for the key vault
runAsPreJob: true # Runs before the job starts
```

Key Vault task will be run as a pre-job which will run before any jobs and you can assign the secrets to variables, and use them across the pipeline.

```
jobs:
- job: Build
  displayName: 'Build'
  variables:
    MySecret: $(my-secret) # this will replace the value from vault
```

## Conclusion

I have tried to explain a secure way of accessing your Azure Key Vault inside a Private Network and accessing it in your DevOps Pipelines. There are many other ways for the same, and there can be flaws in my approach. I will appreciate doing your research before adopting this. Happy scripting :)