Azure Resource Manager (ARM) templates are usually saved as JSON files and stored in a version control system or any other secure location. You can create ARM templates using a simple text editor or more advanced tools like Visual Studio Code with Azure Resource Manager extension or the Azure Portal's built-in template editor.

Here are steps to create and deploy ARM templates:

### Save ARM Templates:

1. **Text Editor or IDE:**
   Save your ARM template as a JSON file, for example, `azureDeploy.json`. Include all the necessary parameters, resources, and configurations in the file.

2. **Version Control System (Optional):**
   Store your ARM templates in a version control system (e.g., Git) to track changes, collaborate with others, and maintain version history.

### Deploy ARM Templates:

1. **Azure Portal:**
   - Navigate to the Azure Portal.
   - Go to the desired resource group or create a new one.
   - In the left sidebar, select "Deployments" under the resource group.
   - Click the "Add" button to start the deployment.
   - Provide the path to your ARM template file and configure any required parameters.
   - Review and confirm the deployment.

2. **Azure CLI:**
   - Use the `az deployment group create` command to deploy an ARM template.
   - Example command:
     ```bash
     az deployment group create --resource-group <ResourceGroupName> --template-file azureDeploy.json --parameters azureDeploy.parameters.json
     ```

3. **Azure PowerShell:**
   - Use the `New-AzResourceGroupDeployment` cmdlet to deploy an ARM template.
   - Example command:
     ```powershell
     New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroupName> -TemplateFile .\azureDeploy.json -TemplateParameterFile .\azureDeploy.parameters.json
     ```

4. **Azure DevOps:**
   - Set up a build or release pipeline in Azure DevOps.
   - Add a task to deploy the ARM template using the "Azure Resource Group Deployment" task.
   - Configure the task with the path to your ARM template file and any required parameters.

5. **Azure REST API:**
   - Use the Azure Resource Manager REST API to programmatically deploy ARM templates.
   - Make a `PUT` request to the deployment endpoint with the JSON payload containing your ARM template and parameters.

Ensure that you have the necessary permissions to deploy resources in the target subscription and resource group. Also, validate your ARM templates before deployment using tools like the [Azure Resource Manager Template Validator](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-syntax#template-validator).