---
title: 'Azure App Service en Azure Stack: actualización de dominio de error | Microsoft Docs'
description: Redistribución de Azure App Service en Azure Stack entre dominios de error
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150981"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Redistribución de Azure App Service en Azure Stack entre dominios de error

*Se aplica a: sistemas integrados de Azure Stack*

Con la actualización 1802, Azure Stack ahora admite la distribución de cargas de trabajo entre dominios de error, una característica que es crítica para la alta disponibilidad.

> [!IMPORTANT]
> Debe haber actualizado el sistema integrado en Azure Stack a la versión 1802 para poder beneficiarse de la compatibilidad con dominios de error.  Este documento solo se aplica a implementaciones de proveedor de recursos de App Service realizadas antes de la actualización 1802.  Si ha implementado App Service en Azure Stack después de aplicar la actualización 1802 a Azure Stack, el proveedor de recursos ya está distribuido entre dominios de error.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Reequilibrio de un proveedor de recursos de App Service entre dominios de error

Para redistribuir los conjuntos de escalado implementados para el proveedor de recursos de App Service, debe realizar los pasos siguientes para cada conjunto de escalado.  De forma predeterminada, los nombres de los conjuntos de escalado son:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Si no tiene ninguna instancia implementada en algunos de los conjuntos de escalado a nivel de trabajo, no necesita reequilibrarlos.  Los conjuntos de escalado se reequilibrarán correctamente al escalarlos horizontalmente en el futuro.
>
>

1. Vaya a Virtual Machine Scale Sets en el portal de administrador de Azure Stack.  Los conjuntos de escalado implementados como parte de la implementación de App Service se enumerarán con la información del recuento de instancias.

    ![Azure App Service Scale Sets enumerados en la experiencia de usuario con Virtual Machine Scale Sets][1]

2. A continuación, escale cada conjunto horizontalmente.  Por ejemplo, si tiene tres instancias existentes en el conjunto de escalado, debe escalarlas horizontalmente a seis, para que las tres instancias nuevas se aprovisionen en los dominios de error.
    a. [Configuración del entorno de administración de Azure Stack con PowerShell](azure-stack-powershell-configure-admin.md) b. Use este ejemplo para escalar horizontalmente el conjunto de escalado:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Este paso puede tardar algunas horas en completarse en función del tipo de rol y del número de instancias.
>
>

3. Supervise el estado de las nuevas instancias de rol en la hoja Roles de administración de App Service.  Compruebe el estado de una instancia de rol individual; para ello, haga clic en el tipo de rol en la lista.

    ![Azure App Service en roles de Azure Stack][2]

4. Una vez que las instancias nuevas están en un estado **Listo**, vuelva a la hoja del conjunto de escalado de máquinas virtuales y **elimine** las instancias anteriores.

5. Repita estos pasos para **cada** conjunto de escalado de máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md).

* [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
