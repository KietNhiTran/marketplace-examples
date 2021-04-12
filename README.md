# Installation guide - Microsft Commercial Marketplace Community Sample Code and SDK for SaaS Applications

## Introduction

This page simplified the install instruction for the sameple code located here ![Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) as well as fix error during deployment.

## Prerequisite

* Powershell is installed
* Azure CLI is installed
* You must have an active Azure subscription for development and testing purposes. Create an Azure subscription [here.](https://azure.microsoft.com/free/)
* You must have a Partner Center account enabled for use with the commercial marketplace. Create an account [here.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)
* The SDK has been implemented using [.NET Core 3.1.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* For data persistence we are using [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) and [Entity Framework](https://docs.microsoft.com/ef/). However, feel free to use any data repository you are comfortable with.  

## Instruction

### Step 1: source code

* Repo1: Clone the original repository ![Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK)
* Repo2: Clone this repossitory
* Copy 2 files in the templates folder of **repo2** to the template folder of **repo1**

### Step2: deploy infrastructure

* cd deployment\templates
* run below deployment command. Remember to replace parameter value with your own.
```powershell
.\infra_deployemnt.ps1 
            -WebAppNamePrefix "contoso" 
            -TenantID "tenandId" 
            -ADApplicationID "clientId" 
            -ADApplicationSecret "secret" 
            -SQLServerName "contososqlsrv" 
            -SQLAdminLogin "adminlogin" 
            -SQLAdminLoginPassword "password" 
            -PublisherAdminUsers "user@contoso.com"              
            -AzureSubscriptionID "subscriptionId" 
            -ResourceGroupForDeployment "resourcegroup" 
            -Location "East US" 
            -PathToARMTemplate ".\updated_deploy.json"
```

| Parameter | Description |
|-----------| -------------|
| WebAppNamePrefix | Prefix used for creating web applications. Example: contoso |
| TenantID | The value should match the value provided for Active Directory TenantID in the Technical Configuration of the Transactable Offer in Partner Center |
| ADApplicationID | The value should match the value provided for Active Directory Application ID in the Technical Configuration of the Transactable Offer in Partner Center |
| ADApplicationSecret | Secret key of the AD Application |
| SQLServerName | Name of the database server (without database.windows.net) |
| SQLAdminLogin | SQL Admin login |
| SQLAdminLoginPassword | SQL Admin password |
| PublisherAdminUsers | Provide a list of email addresses (as comma-separated-values) that should be granted access to the Publisher Portal |
| ResourceGroupForDeployment | Name of the resource group to deploy the resources |
| Location | Location of the resource group |
| AzureSubscriptionID | Subscription where the resources be deployed |
| PathToARMTemplate | Local Path to the ARM Template |


### Step 3: deploy samples applications

```javascript
// build and publish .NET sample appications
dotnet publish ..\..\src\SaaS.SDK.CustomerProvisioning\SaaS.SDK.CustomerProvisioning.csproj -c debug -o ..\..\Publish\CustomerPortal
dotnet publish ..\..\src\SaaS.SDK.PublisherSolution\SaaS.SDK.PublisherSolution.csproj -c debug -o ..\..\Publish\PublisherPortal

// zip the builds
zip -r CustomerPortal.zip ..\..\Publish\CustomerPortal\*
zip -r PublisherSolution.zip ..\..\Publish\PublisherPortal\*

// deploy the applications
az webapp deployment source config-zip -g <deployed resource group> -n <Customer portal webapp name> --src CustomerPortal.zip
az webapp deployment source config-zip -g <deployed resource group> -n <Publisher portal webapp name> --src PublisherPortal.zip

```

### Ste 4: register application in your tenant

Follow the instruction here ![Register a SaaS application] (https://docs.microsoft.com/en-us/azure/marketplace/partner-center-portal/pc-saas-registration)

* For "Supported account types" you should select : "Accounts in any organizational directory (Any Azure AD directory - Multitenant)"
* After register, create a secret
* After register, go to "Authentication" section, adding below URL to "Redirect URIs"
  * https://<Customer portal>.azurewebsites.net
  * https://<Customer portal>.azurewebsites.net/
  * https://<Customer portal>.azurewebsites.net/Home/Index
  * https://<Customer portal>.azurewebsites.net/Home/Index/
  * https://<Publisher portal>.azurewebsites.net
  * https://<Publisher portal>.azurewebsites.net/
  * https://<Publisher portal>.azurewebsites.net/Home/Index
  * https://<Publisher portal>.azurewebsites.net/Home/Index/

### Step 5: Update the Technical configuration for your SaaS transactable offer in Partner Center

![Techinical configuration](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK/blob/main/docs/images/offer-technical-configuration.png)

Publish your offer.


### Step 6: enjoy the demo

* Very important: you should wait as lest 30 min after your offer published in order to see the solution available in marketplace preview link.
* Follow the market preview link to access the offer.
* click on "Subscribe" or "Get it" to start the flow. 
* Ref the customer expereince here: https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK/blob/main/docs/Customer-Experience.md 
* Ref the publisher experience here: https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK/blob/main/docs/Publisher-Experience.md


## Reference

* Databse design of the sample app can be found here: ![ERD diagram](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK/blob/main/docs/images/amp-saas-db.png)