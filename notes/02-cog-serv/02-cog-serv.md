# [Secure Cognitive Services](https://docs.microsoft.com/en-us/learn/modules/secure-cognitive-services/)

## Consider authentication

- Notes
    >
    > - Regenerate keys
    >
    > - Protect keys with Azure Key Vault
    >
    > - Token-based authentication (REST interfaces)
    >
    > - Azure Active Directory authentication
    >

## Implement network security

- Notes
    >
    > - Network access restriction options under "Cognitive services/Networking"
    >

## Lab: Manage Cognitive Services Security

- Notes

    >
    > - Clone the repository for this course: Clone the repository for this course
    >
    > - On MS Azure, Select the ＋Create a resource button, search for cognitive services, and create a Cognitive Services
    >
    > - [caldeira-ms-ai-102-cog-services](./template/deployment.json)
    >
    > - Manage authentication keys on Azure CLI
    >   - az cognitiveservices account keys list --name caldeira-ms-ai-102-cog-services --resource-group ms-ai-102
    >
    >   - curl -X POST "https://caldeira-ms-ai-102-cog-services.cognitiveservices.azure.com/text/analytics/v3.0/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: 5c7a274b81dd4d7f83dae597e2bb27f6" --data-ascii "{'documents':[{'id':1,'text':'hello'}]}"
    >
    > - Regenerate keys:
    >   - az cognitiveservices account keys regenerate --name caldeira-ms-ai-102-cog-services --resource-group ms-ai-102 --key-name key1
    >   - New key1: 011a2c3f409345b19e8d361cdab998f0
    >
    > - Create a service principal (the subscription ID is the id attribute in the output)
    >   - az account show
    >   - {
    "environmentName": "AzureCloud",
    "homeTenantId": "67aa405f-06b1-4d86-a2d4-cba52ba2479c",
    "id": "da9c5b25-8a9f-4e89-a973-72e6768ff89a",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Azure for Students",
    "state": "Enabled",
    "tenantId": "67aa405f-06b1-4d86-a2d4-cba52ba2479c",
    "user": {
        "name": "alexandre.caldeira@ufv.br",
        "type": "user"
    }
    }
    >
    >   - az ad sp create-for-rbac -n "api://ai-app-" --role owner --scopes subscriptions/da9c5b25-8a9f-4e89-a973-72e6768ff89a/resourceGroups/ms-ai-102
    >   - Creating 'owner' role assignment under scope 'subscriptions/da9c5b25-8a9f-4e89-a973-72e6768ff89a/resourceGroups/ms-ai-102'
        {
        "appId": "8d92be60-f115-4ba4-93ca-a4190593c73c",
        "displayName": "ai-app-",
        "name": "api://ai-app-",
        "password": "GQrCmO__eV7.-kYyC8EJGNnLU9yBW1kZ-K",
        "tenant": "67aa405f-06b1-4d86-a2d4-cba52ba2479c"
        }
    >
    > - To get the object ID of your service principal
    >   - az ad sp show --id 8d92be60-f115-4ba4-93ca-a4190593c73c --query objectId --out tsv
    >
    > - To assign permission for your new service principal to access secrets in your Key Vault, run the following Azure CLI command
    >   - az keyvault set-policy -n caldeira-ms-ai-102-kv --object-id 0e7757b4-073d-4b84-a8d9-b731b34f533b --secret-permissions get list
    >
    > Note: In this exercise, we'll store the service principal credentials in the application configuration and use them to authenticate a ClientSecretCredential identity in your application code. This is fine for development and testing, but in a real production application, an administrator would assign a managed identity to the application so that it uses the service principal identity to access resources, without caching or storing the password.
    >

## Monitor Cognitive Services

- Notes
    >
    > - The scope of the alert rule - in other words, the resource you want to monitor.
    > - A condition on which the alert is triggered. The specific trigger for the alert is based on a signal type, which can be Activity Log or Metric.
    > - Optional actions, such as sending an email to an administrator notifying them of the alert, or running an Azure Logic App to address the issue automatically.
    > - Alert rule details, such as a name for the alert rule and the resource group in which it should be defined.
    >
    >
    >
    > - Create resources for diagnostic log storage: ou can use Azure Event Hub as a destination in order to then forward the data on to a custom telemetry solution, and you can connect directly to some third-party solutions, or Azure Log Analytics, or Azure Storage.
    >
    >

## Using container instances

- Notes
    >
    > - docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=https://caldeira-ms-ai-102-cog-services.cognitiveservices.azure.com/ ApiKey=011a2c3f409345b19e8d361cdab998f0
    >
    > - curl -X POST "http://localhost:5000/text/analytics/v3.0/languages?" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'on premises bullshit'},{'id':2,'text':'hallo hoe gaat het?'}]}"
    >