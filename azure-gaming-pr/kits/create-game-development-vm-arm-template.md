---
title: Quickstart - Create a Game Development Virtual Machine with an ARM template
description: Create an instance of the Game Development Virtual Machine using an Azure Resource Manager (ARM) template.
author: dciborow
keywords: 
ms.topic: quickstart
ms.date: 02/01/2022
ms.author: dciborow
ms.prod: azure-gaming
---

# Quickstart: Create a Game Development Virtual Machine using an ARM template

Create an instance of the Game Development Virtual Machine using an Azure Resource manager (ARM) template. Game Development Virtual Machines are cloud-based virtual machines preloaded with a suite of software and tools for game development. When deployed on GPU-powered compute resources, all tools are configured to use the GPU.

An ARM template is a JavaScript Object Notation (JSON) file that defines the infrastructure and configuration for your project. The template uses declarative syntax. In declarative syntax, you describe your intended deployment without writing the sequence of programming commands to create the deployment.

If your environment meets the prerequisites and you're familiar with using ARM templates, select the Deploy to Azure button. The template will open in the Azure portal.

<!-- <Add Deploy to Azure Button> -->

## Prerequisites

- An Azure account with an active subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free) before you begin.
- To use the CLI commands in this document from your local environment, you need the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## Review the template

The template used in this quickstart is from [Azure Quickstart Templates](). 

<!-- <Add copy of ARM template JSON (it’s really big)>  -->

The following resources are defined in the template:

- Microsoft.Network/publicIPAddress
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/virtualNetworks
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachiness
