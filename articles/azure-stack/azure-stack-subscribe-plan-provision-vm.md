---
title: "Suscripción a una oferta | Microsoft Docs"
description: "Aprenda cómo los usuarios se suscriben a una oferta."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.openlocfilehash: f70815b5e89753a4b0083ffbe10d9920062d1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="subscribe-to-an-offer"></a>Suscripción a una oferta

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Ahora que ha [creado una oferta](azure-stack-create-offer.md), compruebe que los usuarios pueden crear una suscripción.

1. [Inicie sesión](azure-stack-connect-azure-stack.md) el portal de usuarios de Azure Stack (https://portal.local.azurestack.external/) y haga clic en **Obtener una suscripción**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. En el campo **Nombre para mostrar**, escriba un nombre para la suscripción, haga clic en **Oferta**, haga clic en una de las ofertas en la hoja **Elija una oferta** y, después, haga clic en **Crear**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Para ver la suscripción que ha creado, haga clic en **Más servicios**, en **Suscripciones** y, luego, en la nueva suscripción.  

Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

## <a name="subscribe-to-an-add-on-plan"></a>Suscripción a un plan complementario
Si la oferta tiene algún plan complementario, los usuarios pueden agregarlo a su suscripción en cualquier momento.  

1. En el portal de usuarios, seleccione **Más servicios** > **Suscripciones**.

2. Haga clic en la suscripción > botón **Agregar Plan** y seleccione el plan complementario.



## <a name="next-steps"></a>Pasos siguientes
[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
