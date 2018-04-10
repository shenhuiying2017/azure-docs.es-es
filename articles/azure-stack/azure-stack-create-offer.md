---
title: Creación de una oferta en Azure Stack | Microsoft Docs
description: Como administrador de la nube, aprenda a crear una oferta para los usuarios de Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Creación de una oferta en Azure Stack

Las [ofertas](azure-stack-key-features.md) son grupos de uno o varios planes que los proveedores presentan a los usuarios para que estos los compren o se suscriban a ellos. Este documento muestra cómo crear una oferta que incluye el [plan que creó](azure-stack-create-plan.md) en el último paso. Esta oferta da a los suscriptores la capacidad de aprovisionar máquinas virtuales.

1. Inicie sesión en el portal de administrador de Azure Stack (https://adminportal.local.azurestack.external) > haga clic en **Nuevo** > **Ofertas + Planes de inquilinos** > **Oferta**.

   ![](media/azure-stack-create-offer/image01.png)
2. En el panel **Nueva oferta**, rellene el **Nombre para mostrar** y el **Nombre de recurso** y, a continuación, seleccione un **Grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo de la oferta. Este nombre descriptivo es la única información acerca de la oferta que verán los usuarios cuando se suscriban. Por lo tanto, asegúrese de usar un nombre intuitivo que ayude al usuario a entender lo que incluye la oferta. Solo el administrador puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con la oferta como un recurso de Administrador de recursos de Azure.

   ![](media/azure-stack-create-offer/image01a.png)
3. Haga clic en **Planes de base** para abrir el panel **Plan**, seleccione los planes que desea incluir en la oferta y, a continuación, haga clic en **Seleccionar**. Haga clic en **Crear** para crear la oferta.

   ![](media/azure-stack-create-offer/image02.png)
4. Después de crear la oferta puede cambiar su estado. Las ofertas deben hacerse *públicas* para que los usuarios tengan la vista completa de ellas cuando se suscriban. Las ofertas pueden ser:
   - **Públicas**: visibles para los usuarios.
   - **Privadas**: solo las ven los administradores de la nube. Resulta útil durante la elaboración del plan o la oferta, o si el administrador de la nube desea [crear cada una de las suscripciones para los usuarios](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Retirados**: cerrados a nuevos suscriptores. El administrador de la nube puede usar las ofertas y planes retirados para evitar futuras suscripciones, pero dejar intactos los suscriptores actuales.

   > [!TIP]  
   > Los cambios en la oferta no son inmediatamente visibles para el usuario. Para ver los cambios, es posible que los usuarios tengan que cerrar sesión y volver a iniciarla en el portal de usuarios para ver la nueva oferta. 

   Para cambiar el estado de la oferta: 

   - **Versión 1803 y posteriores**:  
     En la hoja de información general de la oferta, haga clic en **Estado de accesibilidad**, seleccione el estado que desea usar, como *Pública* y, luego, en **Guardar**. 
 
     ![Seleccionar Estado de accesibilidad](media/azure-stack-create-offer/change-state.png) 

     Como alternativa, después de acceder a una oferta puede ir a **Configuración de la oferta**y, después, seleccionar **Estado de accesibilidad** para cambiar el estado. 

   - **Antes de la versión 1803**:  
     Haga clic en **Todos los recursos**, busque la nueva oferta, haga clic en la nueva oferta, haga clic en **Cambiar estado** y, a continuación, haga clic en **Publicar**.

  
   > [!NOTE] 
   > También puede usar PowerShell para crear ofertas, planes y cuotas predeterminados. Para más información, consulte [Azure Stack Service Administration](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin) (Administración del servicio Azure Stack).
   >


### <a name="next-steps"></a>Pasos siguientes
[Creación de suscripciones](azure-stack-subscribe-plan-provision-vm.md)      
[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
