# Define parameters
rg_a="RG-A"
rg_b="RG-B"
rg_c="RG-C"
location="westus3"
storage_account_sku="Standard_LRS"
function_runtime="node"

# Naming conventions
app_insights_name_prefix="MyAppInsights"
webpubsub_name_prefix="MyWebPubSub"
frontdoor_name_prefix="MyFrontDoor"
functionapp_name_prefix="MyFunctionApp"
storage_account_name_prefix="mystorageaccount"
key_vault_name_prefix="MyKeyVault"

# Create RG-A with resources
az group create --name $rg_a --location $location

# Blob Storage in RG-A
az storage account create --name ${storage_account_name_prefix}a --resource-group $rg_a --location $location --sku $storage_account_sku

# Azure Function (Node.js) in RG-A
functionapp_name_a="${functionapp_name_prefix}A"
az functionapp create --resource-group $rg_a --consumption-plan-location $location --runtime $function_runtime --functions-version 3 --name $functionapp_name_a --storage-account ${storage_account_name_prefix}a

# Azure WebPub Sub in RG-A
az webpubsub create --name ${webpubsub_name_prefix}A --resource-group $rg_a --sku Standard_S1 --unit-count 1 --location $location

# Azure Front Door in RG-A
az extension add --name front-door
az front-door create --resource-group $rg_a --name ${frontdoor_name_prefix}A --backend-address https://${storage_account_name_prefix}a.blob.core.windows.net --frontend-host-name ${frontdoor_name_prefix}A.azurefd.net

# Application Insights in RG-A
az monitor app-insights component create --app ${app_insights_name_prefix}A --location $location --resource-group $rg_a --kind web --application-type web

# Create RG-B with resources
az group create --name $rg_b --location $location

# Blob Storage in RG-B
az storage account create --name ${storage_account_name_prefix}b --resource-group $rg_b --location $location --sku $storage_account_sku

# Key Vault in RG-B
az keyvault create --name ${key_vault_name_prefix}B --resource-group $rg_b --location $location

# Azure Function (Node.js) in RG-B
functionapp_name_b="${functionapp_name_prefix}B"
az functionapp create --resource-group $rg_b --consumption-plan-location $location --runtime $function_runtime --functions-version 3 --name $functionapp_name_b --storage-account ${storage_account_name_prefix}b

# Create RG-C with resources
az group create --name $rg_c --location $location

# Blob Storage in RG-C
az storage account create --name ${storage_account_name_prefix}c --resource-group $rg_c --location $location --sku $storage_account_sku

# Key Vault in RG-C
az keyvault create --name ${key_vault_name_prefix}C --resource-group $rg_c --location $location

# Azure Function (Node.js) in RG-C
functionapp_name_c="${functionapp_name_prefix}C"
az functionapp create --resource-group $rg_c --consumption-plan-location $location --runtime $function_runtime --functions-version 3 --name $functionapp_name_c --storage-account ${storage_account_name_prefix}c
