---
title: "Herramientas de la comunidad para la migración de Azure Service Management a Azure Resource Manager"
description: "En este artículo se catalogan las herramientas que la comunidad proporcionó para ayudar en la migración de los recursos de IaaS desde Azure Service Management a la pila de Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 032a7068cce78036caec587bfc53abddf5a36659


---
# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Herramientas de la comunidad para la migración de Azure Service Management a Azure Resource Manager
En este artículo se catalogan las herramientas que la comunidad proporcionó para ayudar en la migración de los recursos de IaaS desde Azure Service Management a la pila de Azure Resource Manager.

> [!NOTE]
> Estas herramientas no se incluyen en el soporte técnico de Microsoft. Por lo tanto, son de código abierto en Github y estamos encantados de aceptar PR para correcciones o escenarios adicionales. Para informar sobre un problema, use la característica de problemas de GitHub.
> 
> La migración con estas herramientas provocará tiempo de inactividad en la máquina virtual clásica. Si lo que busca es información sobre la migración compatible con la plataforma, visite 
> 
> * [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Technical Deep Dive on Platform supported migration from Classic to Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md) (Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager)
> * [Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
Se trata de un módulo de script de PowerShell para migrar su **única** máquina virtual de la pila de Azure Service Management a la pila de Azure Resource Manager. 

[Vínculo a la documentación sobre la herramienta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz es una opción adicional para migrar un conjunto completo de recursos de IaaS de Azure Service Management a recursos de IaaS de Azure Resource Manager. La migración se puede producir dentro de la misma suscripción o entre suscripciones distintas y distintos tipos de suscripción (por ejemplo, suscripciones de CSP).

[Vínculo a la documentación sobre la herramienta](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)




<!--HONumber=Nov16_HO3-->


