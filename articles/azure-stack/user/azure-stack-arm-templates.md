---
title: Uso de plantillas de Azure Resource Manager en Azure Stack | Microsoft Docs
description: "Obtenga información sobre cómo usar plantillas de Azure Resource Manager en Azure Stack para aprovisionar recursos."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 6d4ef16881ef8dc249116aec706f760b163a2972
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Uso de plantillas de Administrador de recursos de Azure en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las plantillas de Azure Resource Manager implementan y aprovisionan todos los recursos para su aplicación en una única operación coordinada. También se pueden reimplementar plantillas para realizar cambios en los recursos del grupo de recursos.

Estas plantillas se pueden implementar con el portal de Microsoft Azure Stack, PowerShell, con la línea de comandos y Visual Studio.

Las plantillas de inicio rápido siguientes están disponibles en [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Implementación de SharePoint (sin alta disponibilidad)
Use la extensión DSC de PowerShell para crear una granja de servidores de SharePoint 2013 que incluya los siguientes recursos:

* Una red virtual
* Tres cuentas de almacenamiento
* Dos equilibradores de carga externos
* Una máquina virtual configurada como controlador de dominio para un nuevo bosque con un único dominio
* Una máquina virtual configurada como un servidor independiente de SQL Server 2014
* Una máquina virtual configurada como una granja de servidores de SharePoint 2013 de la máquina

## <a name="deploy-ad-non-high-availability"></a>Implementación de AD (sin alta disponibilidad)
Use la extensión DSC de PowerShell para crear un servidor de controlador de dominio AD que incluya los siguientes recursos:

* Una red virtual
* Una cuenta de almacenamiento
* Un equilibrador de carga externo
* Una máquina virtual configurada como controlador de dominio para un nuevo bosque con un único dominio

## <a name="deploy-adsql-non-high-availability"></a>Implementación de AD/SQL (sin alta disponibilidad)
Use la extensión DSC de PowerShell para crear un servidor independiente de SQL Server 2014 que incluya los siguientes recursos:

* Una red virtual
* Dos cuentas de almacenamiento
* Un equilibrador de carga externo
* Una máquina virtual configurada como controlador de dominio para un nuevo bosque con un único dominio
* Una máquina virtual configurada como un servidor independiente de SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>Extensión DSC de máquina virtual para un servidor de extracción de Azure Automation
Utilice la extensión de DSC de PowerShell para configurar Administrador de configuración Local (LCM) de una máquina virtual existente y registrarlo en un servidor de extracción de DSC de la cuenta de Azure Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creación de una máquina virtual desde una imagen de usuario
Cree una máquina virtual desde una imagen de usuario personalizada. Esta plantilla también implementa una red virtual (con DNS), una dirección IP pública y una interfaz de red.

## <a name="simple-vm"></a>VM simple
Implemente una máquina virtual de Windows que incluya una red virtual (con DNS), una dirección IP pública y una interfaz de red.

## <a name="cancel-a-running-template-deployment"></a>Cancelación de una implementación de la plantilla en ejecución
Para cancelar la implementación de una plantilla en ejecución, use el cmdlet `Stop-AzureRmResourceGroupDeployment` de PowerShell.

## <a name="next-steps"></a>pasos siguientes
[Implementación de plantillas con el portal](azure-stack-deploy-template-portal.md)

[Información general sobre Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

