---
title: Azure Cloud Build Pipelines
description: This guide shows how to create a basic demo pipeline as seen in the GDC 2022 talk, "Harnessing the power of Azure cloud builds from development to deployment". This is part 1 of an 8 part series.
author: Tze Lin Ong
keywords: 
ms.topic: reference-architectures
ms.date: 3/18/2022
ms.author: tzong
ms.prod: azure-gaming
---
# Azure Cloud Build Pipelines
# A guide to creating a demo build pipeline 

## Overview
This document will guide you through all the steps required to create a basic Azure build pipeline like the one seen in the Game Developer Conference 2022 talk entitled “Harness the power of Azure cloud builds from development to deployment”. 
The focus of this documentation is the portion from version control through build distribution, which is the blue highlighted portion of the overall architecture below. We will use a popular Unreal Engine 4 sample, ShooterGame, as the game build and pipeline.

[![Azure Cloud Build Overview](media/cloud-build-pipeline/acb-0-intro/overview.png)](media/cloud-build-pipeline/acb-0-intro/overview.png)