---
title: "Cancelación de su suscripción de Azure | Microsoft Docs"
description: "Describe cómo cancelar la suscripción de Azure, como la suscripción a la evaluación gratuita"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c23c92234b38eeb911bd316fffe5127bbad4de91
ms.openlocfilehash: e64e4a3baf48ecd5b16bf5a141455152b7a486f5


---
# <a name="cancel-your-azure-subscription"></a>Cancelación de su suscripción de Azure
Puede cancelar su suscripción de Azure como administrador de cuenta. Después de cancelar la suscripción, el acceso a los servicios y recursos de Azure finalizará.

Antes de cancelar la suscripción:

* Realice una copia de seguridad de los datos. Por ejemplo, si está almacenando datos en Azure Storage o SQL, descargue una copia. Si tiene una máquina virtual, guarde una imagen de la misma localmente.
* Cierre los servicios. Vaya a la [página de recursos en el portal de administración](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) y **detenga** todas las máquinas virtuales, aplicaciones u otros servicios en ejecución.
* Considere la posibilidad de migrar los datos. Consulte [Move resources to new resource group or subscription](./azure-resource-manager/resource-group-move-resources.md) (Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción).

Si cancela un [plan de soporte técnico de Azure](https://azure.microsoft.com/support/plans/) pagado, se le seguirá facturando mensualmente el resto del período de 6 meses.

## <a name="cancel-subscription-via-the-azure-portal"></a>Cancelación de la suscripción a través de Azure Portal
1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de cuenta.

   > [!NOTE]
   > El administrador de cuenta puede cancelar las suscripciones. Para más información sobre cómo averiguar quién es el administrador de cuenta de la suscripción, consulte las [Preguntas más frecuentes](billing-subscription-transfer.md#faq).
   >
   >

2. En el menú de concentrador, seleccione **Suscripción**.

    ![Botón Suscripción](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. En la hoja de suscripciones, seleccione la suscripción que desee cancelar y haga clic en **Cancelar suscripción** para iniciar la hoja de cancelación.

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. Escriba el nombre de la suscripción, elija un motivo de cancelación y haga clic en el botón **Cancelar suscripción** situado en la parte inferior de la hoja de cancelación.

> [!NOTE]
> La cancelación tardará hasta 10 minutos en reflejarse en el portal, pero la facturación se detendrán inmediatamente.
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>Cancelación de la suscripción a través del Centro de cuentas de Azure
1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
2. En **Haga clic en una suscripción para ver los detalles y el uso**, seleccione la suscripción que quiera cancelar.

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. En el lado derecho de la página, seleccione **Cancelar suscripción**.

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. Seleccione **Sí, cancelar mi suscripción**.

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. Haga clic en  ![checkbutton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) para cerrar la ventana del cuadro de diálogo y volver a la página de su suscripción.

Después de cancelar la suscripción, esperamos 90 días antes de eliminar permanentemente los datos por si necesita acceder a ellos o cambia de opinión. Para más información, consulte el [Centro de confianza de Microsoft: How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) (Administración de los datos).

## <a name="reactivate-subscription"></a>Reactivación de la suscripción
Si cancela su suscripción de pago por uso accidentalmente, puede [volver a activarla en el Centro de cuentas](billing-subscription-become-disable.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.



<!--HONumber=Dec16_HO2-->


