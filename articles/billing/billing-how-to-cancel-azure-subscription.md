---
title: "Cancelación de su suscripción de Azure | Microsoft Docs"
description: "Describe cómo cancelar la suscripción de Azure, como la suscripción a la evaluación gratuita"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 51fe2ab891e86ae4bd7402622231af47f35aa01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="cancel-your-subscription-for-azure"></a>Cancelación de la suscripción de Azure

Puede cancelar su suscripción de Azure como [administrador de cuenta](billing-subscription-transfer.md#whoisaa). Después de cancelar la suscripción, el acceso a los servicios y recursos de Azure finalizará.

Antes de cancelar la suscripción:

* Realice una copia de seguridad de los datos. Por ejemplo, si está almacenando datos en Azure Storage o SQL, descargue una copia. Si tiene una máquina virtual, guarde una imagen de la misma localmente.
* Cierre los servicios. Vaya a la [página de recursos en el portal de administración](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) y **detenga** todas las máquinas virtuales, aplicaciones u otros servicios en ejecución.
* Considere la posibilidad de migrar los datos. Consulte [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción).

Si cancela un [plan de soporte técnico de Azure](https://azure.microsoft.com/support/plans/) pagado, se le seguirá facturando mensualmente el resto del período de 6 meses.

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelación de la suscripción a través de Azure Portal

1. Seleccione su suscripción en la [página Suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Seleccione la suscripción que desee cancelar y haga clic en **Cancelar suscripción**.

    ![Captura de pantalla que muestra el botón Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Siga las indicaciones y finalice la cancelación.

## <a name="what-happens-after-i-cancel-my-subscription"></a>¿Qué ocurre después de cancelar la suscripción?

Una vez que se cancela, la facturación se detiene inmediatamente. Sin embargo, pueden pasar hasta 10 minutos hasta que la cancelación se muestra en el portal.

Después, los servicios se deshabilitan. Esto significa que las máquinas virtuales se desasignan, las direcciones IP temporales se liberan y el almacenamiento es de solo lectura.

Si cancela en medio de un período de facturación, enviamos la factura final en la fecha de factura típica una vez finalizado el período. 

Esperamos 90 días antes de eliminar permanentemente los datos, por si necesita acceder a ellos o cambia de opinión. No se le cobrará por conservar los datos. Para más información, consulte el [Microsoft Trust Center: How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centro de confianza de Microsoft: Administración de los datos).

## <a name="reactivate-subscription"></a>Reactivación de la suscripción

Si cancela su suscripción de pago por uso accidentalmente, puede [volver a activarla en el Centro de cuentas](billing-subscription-become-disable.md).

Si su suscripción no es de pago por uso, para reactivar la suscripción, póngase en contacto con el soporte técnico en los noventa días posteriores a la cancelación.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si tiene más preguntas, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
