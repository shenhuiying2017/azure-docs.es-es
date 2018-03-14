---
title: "Proporcionar conjuntos de escalado de máquinas virtuales en Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo un operador de la nube puede agregar el escalado de máquinas virtuales a la plataforma Marketplace de Azure Stack."
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: cb8ac5435b7a5c6deb9d4571696c79b2ed15c93a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Proporcionar conjuntos de escalado de máquinas virtuales en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure Stack. Puede utilizarlos para implementar y administrar un conjunto de máquinas virtuales idénticas. Si todas las máquinas virtuales tienen la misma configuración, los conjuntos de escalado no requieren un aprovisionamiento previo de las máquinas virtuales. Esto facilita la creación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedores, de macroproceso y macrodatos.

Este artículo le guía por el proceso de poner a disposición de los usuarios los conjuntos de escalado en la plataforma Marketplace de Azure Stack. Después de completar este procedimiento, los usuarios pueden agregar conjuntos de escalado de máquinas virtuales a sus suscripciones.

Los conjuntos de escalado de máquinas virtuales en Azure Stack son similares a los conjuntos de escalado de máquinas virtuales en Azure. Para obtener más información, consulte los siguientes vídeos:
* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich habla sobre los conjuntos de escalado de Azure)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

En Azure Stack, los conjuntos de escalado de máquinas virtuales no admiten escalado automático. Puede agregar más instancias a un conjunto de escalado a través del portal de Azure Stack, las plantillas de Resource Manager o PowerShell.

## <a name="prerequisites"></a>requisitos previos
* **PowerShell y herramientas**

   Instalación y configuración de PowerShell para Azure Stack y las herramientas de Azure Stack. Consulte [Get up and running with PowerShell in Azure Stack](azure-stack-powershell-configure-quickstart.md) (Ponerse en marcha con PowerShell en Azure Stack).

   Después de instalar las herramientas de Azure Stack, asegúrese de importar el siguiente módulo de PowerShell (ruta de acceso relativa a la carpeta .\ComputeAdmin en la carpeta AzureStack-Tools-master):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Imagen del sistema operativo**

   Si no ha agregado ninguna imagen de sistema operativo a la plataforma Marketplace de Azure Stack, consulte [Add the Windows Server 2016 VM image to the Azure Stack marketplace](azure-stack-add-default-image.md) (Agregar la imagen de VM de Windows Server 2016 a la plataforma Marketplace de Azure Stack).

   Para obtener compatibilidad con Linux, descargue Ubuntu Server 16.04 y agréguelo con ```Add-AzsVMImage``` con los siguientes parámetros: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Adición del conjunto de escalado de máquinas virtuales

Edite el siguiente script de PowerShell para su entorno y, después, ejecútelo para agregar un conjunto de escalado de máquinas virtuales a la plataforma Marketplace de Azure Stack. 

``$User`` es la cuenta que usa para conectar el portal de administración. Por ejemplo, serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Eliminación de un conjunto de escalado de máquinas virtuales

Para eliminar un elemento de la galería del conjunto de escalado de máquinas virtuales, ejecute el siguiente comando de PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Es posible que el elemento de la galería no se quite de forma inmediata. Es posible que deba actualizar el portal varias veces para que el elemento se muestre como eliminado de Marketplace.


## <a name="next-steps"></a>Pasos siguientes
[Preguntas frecuentes acerca de Azure Stack](azure-stack-faq.md)

