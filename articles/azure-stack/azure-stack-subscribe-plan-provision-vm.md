---
title: "Suscripción a una oferta | Microsoft Docs"
description: "Aprenda cómo se suscriben los inquilinos a las ofertas."
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 3cd87ebe9827249d32f15b5de0ad8521d0282c47
ms.contentlocale: es-es
ms.lasthandoff: 09/15/2017

---
# <a name="subscribe-to-an-offer"></a>Suscripción a una oferta
Ahora que ha [creado una oferta](azure-stack-create-offer.md), compruebe que los inquilinos pueden crear una suscripción.

1. [Inicie sesión](azure-stack-connect-azure-stack.md) el portal de inquilinos de Azure Stack (https://portal.local.azurestack.external/) y haga clic en **Obtener una suscripción**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. En el campo **Nombre para mostrar**, escriba un nombre para la suscripción, haga clic en **Oferta**, haga clic en una de las ofertas en la hoja **Elija una oferta** y, después, haga clic en **Crear**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. Para ver la suscripción que ha creado, haga clic en **Más servicios**, en **Suscripciones** y, luego, en la nueva suscripción.  

Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

## <a name="subscribe-to-an-add-on-plan"></a>Suscripción a un plan complementario
Si la oferta tiene algún plan complementario, los inquilinos pueden agregarlo a su suscripción en cualquier momento.  

1. En el portal de inquilinos, seleccione **más servicios** > **Suscripciones**.

2. Haga clic en la suscripción > botón **Agregar Plan** y seleccione el plan complementario.



## <a name="next-steps"></a>Pasos siguientes
[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)

