---
title: "Creación o modificación de laboratorios automáticamente con plantillas de Azure Resource Manager con PowerShell | Microsoft Docs"
description: "Obtenga información sobre cómo usar plantillas de Azure Resource Manager con PowerShell para crear o modificar laboratorios automáticamente en un laboratorio de DevTest"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
ms.openlocfilehash: cea4531175df2cc39790497dc049d27e23ffa0c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Creación o modificación de laboratorios automáticamente con plantillas de Azure Resource Manager y PowerShell

DevTest Labs proporciona muchas plantillas de Azure Resource Manager y los scripts de PowerShell que pueden ayudarle a crear rápida y automáticamente nuevos laboratorios o modificar laboratorios existentes y, a continuación, implementar estos recursos.

Este artículo le guía a través del proceso de uso de estas plantillas y scripts para automatizar la creación, la modificación y la implementación de los laboratorios. En este artículo también se muestra dónde puede encontrar más información sobre cómo usar PowerShell para realizar algunas tareas comunes en DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Paso 1: Recopilación de las plantillas y los scripts
Puede encontrar [plantillas de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) y [scripts de PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) creados previamente en nuestro [repositorio de Github](https://github.com/Azure/azure-devtestlab) público. Úselos como están o personalícelos según sus necesidades y almacénelos en su propio [repositorio Git privado](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Paso 2: Edición de la plantilla de Azure Resource Manager
Si nunca ha creado una plantilla antes, puede seguir los pasos que se indican en [Creación de la primera plantilla de Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-create-first-template).

Además, en los [procedimientos recomendados para crear plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) se ofrecen muchas directrices y sugerencias para ayudarle a crear plantillas de Azure Resource Manager confiables y fáciles de usar. Normalmente, se usará una variación de uno de los enfoques o ejemplos proporcionados y se modificará la plantilla según las necesidades de cada persona.

## <a name="step-3-deploy-resources-with-powershell"></a>Paso 3: Implementación de los recursos con PowerShell
Una vez que haya personalizado las plantillas y los scripts, siga los pasos necesarios para [implementar los recursos con plantillas de Resource Manager y Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). El artículo proporciona información general sobre el uso de Azure PowerShell con plantillas de Azure Resource Manager para implementar los recursos en Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tareas comunes que puede realizar en DevTest Labs con PowerShell
Hay muchas otras tareas habituales que puede automatizar mediante PowerShell. Las siguientes secciones de la documentación describen los pasos necesarios para realizar estas tareas.

* [Creación de una imagen personalizada a partir de un archivo VHD mediante PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carga de archivos VHD en la cuenta de almacenamiento del laboratorio mediante PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Incorporación de un usuario externo a un laboratorio mediante PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Creación de un rol personalizado de laboratorio con PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo crear un [repositorio Git privado](devtest-lab-add-artifact-repo.md) en el que almacenar los scripts o plantillas personalizados.
* Explore las [plantillas de Azure Resource Manager de la galería de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).
