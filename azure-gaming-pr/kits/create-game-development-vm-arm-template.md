---
title: Quickstart - Create a Game Development Virtual Machine with an ARM template
description: Create an instance of the Game Development Virtual Machine using an Azure Resource Manager (ARM) template.
author: meaghanlewis
ms.topic: quickstart
ms.date: 03/02/2022
ms.author: mosagie
ms.prod: azure-gaming
---

# Quickstart: Create a Game Development Virtual Machine using an ARM template

Create an instance of the Game Development Virtual Machine using an Azure Resource manager (ARM) template. Game Development Virtual Machines are cloud-based virtual machines preloaded with a suite of software and tools for game development. When deployed on GPU-powered compute resources, all tools are configured to use the GPU.

An ARM template is a JavaScript Object Notation (JSON) file that defines the infrastructure and configuration for your project. The template uses declarative syntax. In declarative syntax, you describe your intended deployment without writing the sequence of programming commands to create the deployment.

If your environment meets the prerequisites and you're familiar with using ARM templates, select the Deploy to Azure button. The template will open in the Azure portal.

<!-- <Add Deploy to Azure Button> -->

## Prerequisites

- An Azure account with an active subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free) before you begin.
- To use the CLI commands in this document from your local environment, you need the [Azure CLI](/cli/azure/install-azure-cli).

## Review the template

The template used in this quickstart is from [Azure Quickstart Templates]().

<!-- <Add copy of ARM template JSON (it’s really big)>  -->

The following resources are defined in the template:

- Microsoft.Network/publicIPAddress
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/virtualNetworks
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachiness

## Deploy the template

To use the template from the Azure CLI, login in and choose your subscription. Then run:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the local administrator username:" adminName &&
read -p "Enter the local administrator password:" adminPass &&
templateUri="<URI TO TEMPLATE>" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri--parameters adminName=$adminName --parameters adminPass=$adminPass &&
echo "Press [ENTER] to continue ..." &&
read
```

When you run the above command, enter:

1. The name of the resource group you’d like to create to contain the Game Development VM and associated resources.
2. The Azure region location in which you wish to make the deployment.
3. The username for the Administrative User.
4. The password for the Administrative User.

## Review the deployed resources

To see your Game Development Virtual Machine:

1. Go to the [Azure portal](https://portal.azure.com).
2. Sign in.
3. Choose the resource group you just selected.

You’ll see the Resource Group’s information:

:::image type="content" source="../media/game-dev-vm-resource-group.png" alt-text="Screenshot of an Azure Resource Group containing a Game Development Virtual Machine":::

Click on the Virtual Machine resource to go to its information page. Here you can find information on the VM, including connection details.

## Clean up resources

If you don’t want to use this virtual machine, delete it. Since the Game Development VM is associated with other resources, such as network interfaces, you will probably want to delete the entire resource group. You can delete the resource group in the portal by clicking on the Delete button and confirming. Or you can delete the resource group from the CLI with:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
