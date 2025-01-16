# AI Lab Infrastructure

## Overview
This project uses Bicep templates to provision Azure resources. The main deployment file is **main.bicep**, which references various module files under the `app` folder.

## Structure
- **main.bicep**: Entry point for resource deployment.
- **app/monitoring.bicep**: Defines monitoring resources (Log Analytics and Application Insights).
- **app/database.bicep**: Creates Cosmos DB and SQL resources.

### main.bicep
- main.bicep declares modules that are conditionally deployed based on parameters (e.g., `switches.CoreDeploy`).
- The `hub` module references `core/ai/hub.bicep` and configures AI-related resources, using outputs like storage and key vault IDs from earlier steps.
- The `project` module references `core/ai/project.bicep`, depends on `hub`, and sets up project environment details (name, location, tags, etc.).
- Each module passes specific parameters (e.g., `AIHubName`, `keyVaultName`) to create and link resources consistently.

#### CoreAI Deploy.bicep
The `CoreAI Deploy.bicep` module is designed to orchestrate the deployment of core AI infrastructure and related resources. It integrates various sub-modules and ensures that all necessary components are deployed in a coordinated manner.

##### Key Components
1. **Parameters**: The module accepts several parameters to customize the deployment. These typically include:
   - `location`: Specifies the region where resources will be deployed.
   - `AIHubName`: The name of the AI hub.
   - `keyVaultName`: The name of the Key Vault.
   - `tags`: Metadata tags for resource management.

2. **Modules**: The `CoreAI Deploy.bicep` module references other Bicep modules to deploy specific resources:
   - **hub.bicep**: Sets up the core AI infrastructure, including Storage Account, Key Vault, and Cognitive Services.
   - **project.bicep**: Configures the project environment, depending on the outputs from `hub.bicep`.

3. **Conditional Deployment**: The deployment of certain modules can be controlled using parameters (e.g., `switches.CoreDeploy`). This allows for flexible and conditional resource provisioning.

4. **Outputs**: The module generates outputs that can be used by other modules or for further configuration. These outputs typically include resource IDs and endpoints.

##### Workflow
1. **Parameter Initialization**: The module initializes parameters to customize the deployment.
2. **Module Invocation**: It invokes the `hub.bicep` and `project.bicep` modules, passing necessary parameters and handling dependencies.
3. **Resource Linking**: Outputs from `hub.bicep` are used as inputs for `project.bicep` to ensure seamless integration and resource linking.
4. **Deployment Execution**: The module executes the deployment, ensuring that all resources are provisioned as specified.

By using the `CoreAI Deploy.bicep` module, you can efficiently deploy a comprehensive AI infrastructure with minimal manual intervention, leveraging the power of Bicep for infrastructure as code.

#### hub.bicep
The `hub.bicep` module is responsible for setting up the core AI infrastructure. It includes the following resources:
- **Storage Account**: Used for storing data and artifacts.
- **Key Vault**: Manages secrets and keys securely.
- **Cognitive Services**: Provides AI capabilities like vision, speech, and language understanding.

The module takes parameters such as `AIHubName`, `location`, and `tags` to configure these resources. Outputs from this module, like storage account and key vault IDs, are used by other modules to ensure seamless integration.

#### project.bicep
The `project.bicep` module is designed to deploy and manage project-specific resources within the AI infrastructure. It builds on the core setup provided by the `hub.bicep` module and includes the following resources:
- **App Service**: Hosts web applications and APIs.
- **SQL Database**: Stores relational data for the project.
- **Application Insights**: Monitors and provides analytics for the application.

The module takes parameters such as `projectName`, `location`, `appServicePlan`, and `databaseConfig` to configure these resources. It ensures that the deployed resources are properly integrated with the core infrastructure by referencing outputs from the `hub.bicep` module, such as storage account and key vault IDs.

Key parameters:
- `projectName`: The name of the project.
- `location`: The Azure region where resources will be deployed.
- `appServicePlan`: Configuration for the App Service plan.
- `databaseConfig`: Configuration settings for the SQL Database.

Outputs from this module, like the App Service URL and SQL Database connection string, are used by other modules or applications to interact with the deployed resources.

## Usage
1. Modify parameters in the .env file to match your environment.

2. Deploy with Azure CLI:
   ```bash
   ./Setup.azcli