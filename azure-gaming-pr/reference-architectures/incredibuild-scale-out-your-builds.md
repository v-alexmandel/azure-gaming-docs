---
title: Scale out your cloud builds with Incredibuild
description: Learn how to set up Incredibuild in your build pipeline
author: Tze Lin Ong
keywords: 
ms.topic: reference-architecture
ms.date: 3/14/2022
ms.author: tzong
ms.prod: azure-gaming
---

# Set up Incredibuild on Azure DevOps build agents

## Overview
This guide will show how to set up Incredibuild on an Azure DevOps Windows build agent as part of an Azure-powered build pipeline from the related GDC 2022 talk . This document is not intended as a substitute for Incredibuild’s documentation, rather, it aims to give the context of Incredibuild setup within an Azure DevOps build pipeline. We will refer to the Incredibuild documentation where appropriate.
This document also assumes the reader has some fundamental knowledge of:
- working in the Azure Portal, with the ability to create VMs and simple networking
- working in Azure DevOps, with the ability to set up build agents (not to be confused with Incredibuild agents)

## What is Incredibuild?
Incredibuild is a suite of compilation acceleration software that takes advantage of idle compute resources within an organization to spread compute load. You can find out more about the company, its products and licensing at **[https://www.incredibuild.com/](https://www.incredibuild.com/)**
Incredibuild has been used for more than two decades and is a popular choice in game development to speed up build workflows in game studios. 

## Quick overview of Incredibuild architecture
This section is a summary of the **[full Incredibuild documentation](https://docs.incredibuild.com/win/latest/windows/components_and_architecture.html)**, and points out the bare basics needed to get started. Incredibuild basically has two components: Agents and Coordinators.
### Agents
Agents are the machine instances that run compile workloads. The initiator agent is the first thing to get activated to assess the workload. Then helper agents assist the initiators. Together, they are the computing resources that receive packages of work to execute, then send back the results.
Agents can be on-premises machines, VMs in the cloud, or a mix of both. Once an Incredibuild software agent is installed on a computer and configured to point to a Coordinator, it then gets registered as an Agent.
### Coordinator
Coordinators, as the name implies, coordinate the sending and receiving of work processes between all agents. They do this after receiving a request from the initiator Agent that tells it how many cores it needs. Among other tasks, Coordinators also track availability, state, quality of service and package update status of all agents.  
The coordinator software is simply installed on the machine designated for that task.
There is usually one Coordinator to handle multiple agents. Of course, you can also set up redundant Coordinators for fault tolerance.  
Coordinators require a static IP address or a configured DNS name for all agents to locate it. 


## Prerequisites and considerations
There are a few considerations for how to set up your Incredibuild system. What machine will you use for the Coordinator and Initiator Agent?  What machines will you use for your helper Agents?  If you are using Incredibuild Cloud agents, what kind of VMs will you need, and how many? Do you already have an Azure Subscription you can use?

Much will depend on your current setup and your budgets. Here are some suggestions for you to consider.

### Complete Azure setup
In this case, we assume:
- Coordinators and Agents will all be VMs in Azure
- The Azure DevOps pipeline will use a custom build agent that will act as the Coordinator and Initiator Agent

Also consider where your Helper Agents will come from. During the Incredibuild Cloud setup, you will be asked to specify what type(s) of VMs you would like to use, and the maximum number of VMs to scale. 
You also have the option of using Spot VM instances as agents. Spot instances allow you to take advantage of Azure’s unused capacity at a significant cost savings. The trade-off is that when Azure needs the capacity back, the Azure infrastructure will evict these Spot Virtual Machines. However, because of the way Incredibuild divides the compute tasks, Azure Spot VM remain a viable option for a build pipeline. For more information on Azure Spot VMs, please see the **[documentation](https://docs.microsoft.com/en-us/azure/virtual-machines/spot-vms)**.
You should also check your Azure core quota. You cannot allocate more VMs of a certain type than your Subscriptions’ quotas allow, so make sure that you confirm you can allocate as many as you need. You can **[request more quota](https://docs.microsoft.com/en-us/azure/azure-portal/supportability/per-vm-quota-requests)** of a certain VM SKU if needed.

### Hybrid setup
You may already have an on-premises compute farm that you would like to use as part of the machine pool. Incredibuild deals with both on-premises machines and cloud machines, so you would just have to configure them accordingly.
One consideration is to use your on-premises resources as the mainstay of your build pipeline, and then use Azure VMs to burst when needed.
Don’t forget to check your Azure core quota as well. If you don’t have enough quota of a certain SKU of interest, in certain Azure regions, you should apply ahead of time for a quota increase as it could take some time. 

### Licensing
You will need a working Incredibuild license. You can obtain a **[free trial license](https://www.incredibuild.com/free-trial)** from Incredibuild. This will allow you to run a limited number of agent cores to get started. For a full license, please contact Incredibuild.

### Azure Subscription
Assuming you will use Azure VMs to scale computation, you will need an Azure Subscription with a suitable RBAC role for yourself, such that you can create an **[App Registration](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)**. This will be needed in the Incredibuild Cloud setup phase.

### Full Incredibuild requirements
Incredibuild specifies requirements for open ports, networking, storage, operating system and more, in their **[documentation here](https://docs.incredibuild.com/win/latest/windows/system_requirements.html)**.


## Setting up Incredibuild



## Testing and troubleshooting


## Conclusion




