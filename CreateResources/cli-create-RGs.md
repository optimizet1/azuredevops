// https://docs.microsoft.com/en-US/cli/azure/deployment/group#az_deployment_group_create

az deployment group create --resource-group <ResourceGroupName> --template-file azureDeploy.json --parameters azureDeploy.parameters.json


az deployment group create --resource-group RG-A --template-file azureDeploy_RG_A.json --parameters @azureDeploy_RG_A.parameters.json

///////////////////

az group create -l westus -n RG-A
az group create -l westus3 -n RG-A3

//az deployment group create --resource-group RG-A --template-file azureDeploy_RG_A.json --parameters @azureDeploy_RG_A.parameters.json

az storage account create --name mystorage2a -g RG-A -l westus3 --kind StorageV2 --allow-blob-public-access false

az monitor log-analytics workspace create -g RG-A -n A1MyWorkspace
az monitor log-analytics workspace create -g RG-A -n A2MyWorkspace -l westus3

az monitor app-insights component create --app A1demoApp --location eastus --kind web -g RG-A --workspace "/subscriptions/1db226c9-ff74-4d11-9692-39b12e318a5b/resourcegroups/RG-A/providers/microsoft.operationalinsights/workspaces/A1MyWorkspace"

az monitor app-insights component create --app A2demoApp --location westus3 --kind web -g RG-A --workspace "/subscriptions/1db226c9-ff74-4d11-9692-39b12e318a5b/resourcegroups/RG-A/providers/microsoft.operationalinsights/workspaces/A2MyWorkspace"

az functionapp create --consumption-plan-location westus3 --name FuncAMyUniqueAppName --os-type Windows --resource-group RG-A --runtime node --runtime-version 18 --storage-account mystorage2a --functions-version 4

az webpubsub create -n AMyWebPubSub -g RG-A --sku Free_F1 --unit-count 1 --kind WebPubSub -l westus3

///////////////////


az afd 


////////////Test


