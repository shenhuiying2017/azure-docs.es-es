---
title: "Creación de una oferta en Azure Stack | Microsoft Docs"
description: Como administrador de la nube, aprenda a crear una oferta para los usuarios de Azure Stack.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 2666aacbbaf44ae9b3bcf2df480e835457e6f449
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Creación de una oferta en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las [ofertas](azure-stack-key-features.md) son grupos de uno o varios planes que los proveedores presentan a los usuarios para que estos los compren o se suscriban a ellos. Este documento muestra cómo crear una oferta que incluye el [plan que creó](azure-stack-create-plan.md) en el último paso. Esta oferta da a los suscriptores la capacidad de aprovisionar máquinas virtuales.

1. Inicie sesión en el portal de administrador de Azure Stack (https://adminportal.local.azurestack.external) > haga clic en **New** > **Tenant Offers + Plans** >  **Offer** (Nuevo > Ofertas y planes de inquilino).

   ![](media/azure-stack-create-offer/image01.png)
2. En el panel **Nueva oferta**, rellene el **Nombre para mostrar** y el **Nombre de recurso** y, a continuación, seleccione un **Grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo de la oferta. Este nombre descriptivo es la única información acerca de la oferta que verán los usuarios cuando se suscriban. Por lo tanto, asegúrese de usar un nombre intuitivo que ayude al usuario a entender lo que incluye la oferta. Solo el administrador puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con la oferta como un recurso de Administrador de recursos de Azure.

   ![](media/azure-stack-create-offer/image01a.png)
3. Haga clic en **Planes de base** para abrir el panel **Plan**, seleccione los planes que desea incluir en la oferta y, a continuación, haga clic en **Seleccionar**. Haga clic en **Crear** para crear la oferta.

   ![](media/azure-stack-create-offer/image02.png)
4. Haga clic en **Todos los recursos**, busque la nueva oferta, haga clic en la nueva oferta, haga clic en **Cambiar estado** y, a continuación, haga clic en **Publicar**.

   ![](media/azure-stack-create-offer/image03.png)

Las ofertas deben hacerse públicas para que los usuarios obtengan la vista completa cuando se suscriban. Las ofertas pueden ser:

* **Públicas**: visibles para los usuarios.
* **Privadas**: solo son visibles para los administradores de la nube. Resulta útil durante la elaboración del plan o la oferta, o si el administrador de la nube desea [crear cada una de las suscripciones para los usuarios](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
* **Retirados**: cerrados a nuevos suscriptores. El administrador de la nube puede usar las ofertas y planes retirados para evitar futuras suscripciones, pero dejar intactos los suscriptores actuales.

Los cambios en la oferta no son inmediatamente visibles para el usuario. Para visualizar los cambios, es posible que tenga que cerrar sesión e iniciarla para ver la nueva suscripción en el "selector de suscripciones" al crear recursos o grupos de recursos.

> [!NOTE]
>También puede crear ofertas, planes y cuotas predeterminadas mediante PowerShell, como se explica en el [archivo Léame del administrador de servicios de Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).
>


### <a name="next-steps"></a>pasos siguientes
[Creación de suscripciones](azure-stack-subscribe-plan-provision-vm.md)      
[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
