---
title: "Implementación de plantillas con la línea de comandos en Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para implementar plantillas en Azure Stack."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implementación de plantillas en Azure Stack mediante la línea de comandos

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Use la línea de comandos para implementar plantillas de Azure Resource Manager en el kit de desarrollo de Azure Stack. Las plantillas de Azure Resource Manager implementan y aprovisionan todos los recursos para su aplicación en una única operación coordinada.

## <a name="before-you-begin"></a>Antes de empezar
 - [Instale Azure Stack y conéctese](azure-stack-connect-cli.md) a esta infraestructura con la Interfaz de la línea de comandos de Azure.
 - Descargue los archivos *azuredeploy.json* y *azuredeploy.parameters.json* desde la [plantilla de ejemplo para crear una cuenta de almacenamiento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Implementar plantilla
Navegue hasta la carpeta donde se descargaron estos archivos y ejecute el siguiente comando para implementar la plantilla:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Este comando implementará la plantilla en el grupo de recursos **cliRG** en la ubicación predeterminada de la POC de Azure Stack.

## <a name="validate-template-deployment"></a>Validar la implementación de la plantilla
Para ver este grupo de recursos y esta cuenta de almacenamiento, use los siguientes comandos:

    azure group list

    azure storage account list




