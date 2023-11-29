# Variables
resourceGroupName="YourResourceGroup"
location="West US 3"
webPubSubName="YourWebPubSubNamespace"
functionAppName="YourFunctionApp"
appServicePlanName="YourAppServicePlan"
storageAccountName="YourStorageAccount"

# Create Resource Group
az group create --name $resourceGroupName --location $location

# Create Azure Web Pub Sub Namespace
az webpubsub create --name $webPubSubName --resource-group $resourceGroupName --sku Standard_S1 --capacity 1

# Create Azure Function App
az functionapp create --name $functionAppName --resource-group $resourceGroupName --consumption-plan-location $location --runtime node

# Configure Azure Function App with Web Pub Sub
webPubSubConnectionString=$(az webpubsub show --name $webPubSubName --resource-group $resourceGroupName --query "primaryConnectionString" --output tsv)

az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings WEBPUBSUB_APPSETTING=$webPubSubConnectionString

# Optionally, configure additional settings based on your requirements
# az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings AnotherSetting=AnotherValue

# Display connection strings for reference
echo "Web Pub Sub Connection String: $webPubSubConnectionString"
