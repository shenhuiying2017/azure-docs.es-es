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
ms.date: 06/27/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c415fada30aa0b0bd9b9d1e416bc37ef30653f68
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="cancel-your-subscription-for-azure"></a>Cancelación de la suscripción de Azure

Puede cancelar su suscripción de Azure como [administrador de cuenta](billing-subscription-transfer.md#whoisaa). Después de cancelar la suscripción, el acceso a los servicios y recursos de Azure finalizará.

Antes de cancelar la suscripción:

* Realice una copia de seguridad de los datos. Por ejemplo, si está almacenando datos en Azure Storage o SQL, descargue una copia. Si tiene una máquina virtual, guarde una imagen de la misma localmente.
* Cierre los servicios. Vaya a la [página de recursos en el portal de administración](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) y **detenga** todas las máquinas virtuales, aplicaciones u otros servicios en ejecución.
* Considere la posibilidad de migrar los datos. Consulte [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md) (Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción).

Si cancela un [plan de soporte técnico de Azure](https://azure.microsoft.com/support/plans/) pagado, se le seguirá facturando mensualmente el resto del período de 6 meses.

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelación de la suscripción a través de Azure Portal

1. Seleccione su suscripción en la [página Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Seleccione la suscripción que desee cancelar y haga clic en **Cancelar suscripción**.

    ![Captura de pantalla que muestra el botón Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)

1. Siga las indicaciones y finalice la cancelación.

## <a name="cancel-subscription-using-the-azure-account-center"></a>Cancelación de la suscripción a través del Centro de cuentas de Azure

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de la cuenta.

1. En **Haga clic en una suscripción para ver los detalles y el uso**, seleccione la suscripción que quiera cancelar.

    ![Captura de pantalla que muestra una suscripción de ejemplo seleccionada](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)

1. En el lado derecho de la página, seleccione **Cancelar suscripción**.

    ![Captura de pantalla que muestra el botón Cancelar suscripción](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)

1. Seleccione **Sí, cancelar mi suscripción**.

    ![Captura de pantalla que muestra el cuadro de diálogo Cancelar](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)

1. Haga clic en  ![Botón de símbolo de verificación](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) para cerrar la ventana del cuadro de diálogo y volver a la página de su suscripción.

## <a name="what-happens-after-i-cancel-my-subscription"></a>¿Qué ocurre después de cancelar la suscripción?

Una vez que se cancela, la facturación se detiene inmediatamente. Sin embargo, pueden pasar hasta 10 minutos hasta que la cancelación se muestra en el portal.

Después, los servicios se deshabilitan. Esto significa que las máquinas virtuales se desasignan, las direcciones IP temporales se liberan y el almacenamiento es de solo lectura.

A menos que esté disfrutando de una evaluación gratuita o tenga crédito disponible, se le facturarán los cargos de uso pendientes generados entre el último ciclo de facturación y la fecha de cancelación. Recibirá la última factura al final del ciclo de facturación.

Después de cancelar la suscripción, esperamos 90 días antes de eliminar permanentemente los datos por si necesita acceder a ellos o cambia de opinión. No se le cobrará por conservar los datos. Para más información, consulte el [Microsoft Trust Center: How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Centro de confianza de Microsoft: Administración de los datos).

## <a name="reactivate-subscription"></a>Reactivación de la suscripción

Si cancela su suscripción de pago por uso accidentalmente, puede [volver a activarla en el Centro de cuentas](billing-subscription-become-disable.md).

Si su suscripción no es de pago por uso, para reactivar la suscripción, póngase en contacto con el soporte técnico en los noventa días posteriores a la cancelación.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si tiene más preguntas, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

