---
title: Descarga de los datos de uso diario y la factura de Azure | Microsoft Docs
description: "En este artículo se describe cómo descargar o ver los datos de uso diario y la factura de facturación de Azure."
keywords: "factura, facturación, descarga de factura, factura de Azure, uso de Azure"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c920627e11e801bde88b47fc7c7e128e06f59ea8
ms.lasthandoff: 03/22/2017


---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Procedimiento para descargar las datos de uso diario y de factura de Azure
También puede descargar la factura desde [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o recibirla por correo electrónico. Para descargar su uso diario, vaya a la [Centro de cuentas de Azure](https://account.windowsazure.com). Solo el administrador de cuentas tiene permiso para obtener la información de facturación y uso. Para averiguar quién es el administrador de cuentas de la suscripción, consulte [Transferring ownership of an Azure subscription - FAQ](billing-subscription-transfer.md#faq) (Transferencia de la propiedad de una suscripción de Azure - Preguntas más frecuentes).

## <a name="get-your-invoice-in-email-pdf"></a>Obtención de la factura por correo electrónico (.pdf)
Puede optar a destinatarios adicionales y configurarlos para recibir la factura de Azure en un correo electrónico. Esta característica puede no estar disponible para determinadas suscripciones, como ofertas de soporte técnico, contratos Enterprise o Azure bajo licencia Open.

1. Seleccione su suscripción en la [hoja de suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Tendrá que habilitar la opción en cada suscripción que posea. Haga clic en **Facturas** y luego en **Email my invoice** (Enviar mi factura por correo electrónico). 

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Haga clic en **Recibir notificaciones** y acepte los términos:

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Una vez que ha aceptado el contrato, puede configurar destinatarios adicionales:

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Si no recibe un correo electrónico después de seguir los pasos, asegúrese de que su dirección de correo electrónico es correcta en las [preferencias de comunicación de su perfil](https://account.windowsazure.com/profile).


## <a name="download-invoice-from-azure-portal-pdf"></a>Descarga de la factura desde Azure Portal (.pdf)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de la cuenta. 
2. En el menú de concentrador, seleccione **Suscripción**. 

    ![Captura de pantalla que muestra la opción Suscripción](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. En la hoja **Suscripciones**, seleccione la suscripción que desea ver y, a continuación, seleccione **Facturación y uso**. 

    ![Captura de pantalla que muestra la opción Facturación y uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. En la hoja **Facturación y uso**, haga clic en **Descargar factura** para ver una copia de la factura en pdf. 

    ![Captura de pantalla que muestra los períodos de facturación, la opción de descarga y los cargos totales para cada período de facturación](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. Haga clic en el período de facturación para ver el uso diario. 

Para más información sobre la factura, consulte [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md). Para ayudar a administrar los costos, consulte [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Descarga del uso desde el centro de cuentas (.csv)
1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de la cuenta.
2. Seleccione la suscripción para la que quiere la información de factura y uso.
3. Seleccione **HISTORIAL DE FACTURACIÓN**. 

    ![Captura de pantalla que muestra la opción del historial de facturación](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Puede ver sus extractos de los últimos seis períodos de facturación y el período actual sin facturar. 

    ![Captura de pantalla que muestra los períodos de facturación, las opciones para descargar la factura y uso diario, y los cargos totales para cada período de facturación](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Seleccione **Ver extracto actual** para ver una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso. La factura mensual puede ser distinta a esta estimación.

    ![Captura de pantalla que muestra la opción Ver extracto actual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de pantalla que muestra la estimación de los cargos actuales](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Seleccione **Descargar uso** para descargar los datos de uso diario como un archivo CSV. Si ve dos versiones disponibles, descargue la versión 2.

    ![Captura de pantalla que muestra la opción Descargar uso](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Para más información sobre el uso diario, consulte [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md). Para ayudar a administrar los costos, consulte [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing-getting-started.md).

## <a name="noinvoice"></a> ¿Por qué no veo una factura para el último período de facturación?
Pueden existir varias razones por las que no ve una factura:
- Tiene un importe de crédito mensual con la suscripción que no ha superado o tiene una evaluación gratuita. Solo se genera una factura cuando debe dinero.
- Han transcurrido menos de 30 días desde el día que se suscribió a Azure.
- La factura no se ha generado aún. Espere hasta el final del período de facturación.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.


