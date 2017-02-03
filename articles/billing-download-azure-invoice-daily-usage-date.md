---
title: "Cómo descargar los datos de uso diario y la factura de facturación de Azure | Microsoft Docs"
description: "Describe cómo descargar los datos de uso diario y la factura de facturación de Azure"
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
ms.date: 12/2/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9b864a55d413ec7775e96fced4770b414f379a23
ms.openlocfilehash: ffc5cae2c4559dd8c7a9042dc676485966b976f4


---
# <a name="how-to-download-your-azure-billing-invoice-and-daily-usage-data"></a>Cómo descargar las datos de uso diario y de factura de facturación de Azure
Puede descargar el resumen de facturación y uso diario desde el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions). Solo el administrador de cuentas tiene permiso para obtener la información del resumen de facturación y uso. Para averiguar quién es el administrador de cuentas de la suscripción, consulte [Transferencia de la propiedad de una suscripción de Azure: Preguntas más frecuentes](billing-subscription-transfer.md#faq).

## <a name="get-invoice-and-usage-from-azure-account-center"></a>Obtener factura e información sobre el uso del Centro de cuentas de Azure
1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/subscriptions) como administrador de cuenta.
2. Seleccione la suscripción para la que quiere la información de factura y uso.
3. Seleccione **HISTORIAL DE FACTURACIÓN**. 

    ![Captura de pantalla que muestra la opción del historial de facturación](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Puede ver sus extractos de los últimos seis períodos de facturación y el período actual sin facturar. 

    ![Captura de pantalla que muestra los períodos de facturación, las opciones para descargar la factura y uso diario, y los cargos totales para cada período de facturación](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Seleccione **Ver extracto actual** para ver una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso. La factura mensual puede ser distinta a esta estimación.

    ![Captura de pantalla que muestra la opción Ver extracto actual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de pantalla que muestra la estimación de los cargos actuales](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Seleccione **Descargar factura** para ver una copia de la última factura en pdf. 

    ![Captura de pantalla que muestra la opción Descargar factura](./media/billing-download-azure-invoice-daily-usage-date/DLInvoice1.png)

7. Seleccione **Descargar uso** para descargar los datos de uso diario como un archivo CSV. Si ve dos versiones disponibles, descargue la versión 2.

    ![Captura de pantalla que muestra la opción Descargar uso](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Para más información sobre la factura y el uso diario, consulte [Comprender la factura de Microsoft Azure](./billing/billing-understand-your-bill.md).

## <a name="get-invoice-from-azure-portal"></a>Obtener factura desde Azure Portal
Puede ver el uso diario desde Azure Portal, pero solo la factura estará disponible para su descarga.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de cuenta. 
2. En el menú de concentrador, seleccione **Suscripción**. 

    ![Captura de pantalla que muestra la opción Suscripción](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. En la hoja **Suscripciones**, seleccione la suscripción que desea ver y, a continuación, seleccione **Facturación y uso**. 

    ![Captura de pantalla que muestra la opción Facturación y uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. En la hoja **Facturación y uso**, haga clic en **Descargar factura** para ver una copia de la factura en pdf. 

    ![Captura de pantalla que muestra los períodos de facturación, la opción de descarga y los cargos totales para cada período de facturación](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. Haga clic en el período de facturación para ver el uso diario. 

Para más información sobre la factura y el uso diario, consulte [Comprender la factura de Microsoft Azure](./billing/billing-understand-your-bill.md).

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> ¿Por qué no veo una factura para el último período de facturación?
Pueden existir varias razones por las que no ve una factura:
- Tiene un importe de crédito mensual con la suscripción que no ha superado o tiene una evaluación gratuita. No se genera una factura a no ser que deba dinero.
- Han transcurrido menos de 30 días desde el día que se suscribió a Azure.
- La factura no se ha generado aún. Espere hasta el final del período de facturación.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.




<!--HONumber=Dec16_HO2-->


