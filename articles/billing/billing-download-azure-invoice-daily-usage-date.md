---
title: Descarga de los datos de uso diario y la factura de Azure | Microsoft Docs
description: "En este artículo se describe cómo descargar o ver los datos de uso diario y la factura de facturación de Azure."
keywords: "factura, facturación, descarga de factura, factura de Azure, uso de Azure"
services: 
documentationcenter: 
author: genlin
manager: tonguyen
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01057dd7832cab6121a22e3bb7d0dc4cd7e9b35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Procedimiento para descargar las datos de uso diario y de factura de Azure
También puede descargar la factura desde [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) o recibirla por correo electrónico. Para descargar su uso diario, vaya a la [Centro de cuentas de Azure](https://account.azure.com/Subscriptions). Solo determinados roles tienen permiso para obtener la información de facturación y uso, como el administrador de la cuenta. Para obtener más información sobre cómo obtener acceso a la información de facturación, vea [Manage access to Azure billing using roles](billing-manage-access.md) (Administrar el acceso a la facturación de Azure mediante roles).

>[!NOTE]
>Este artículo no es aplicable para clientes del Contrato Enterprise (EA). Si es un cliente EA, las facturas se envían directamente a los administradores de la inscripción.

## <a name="get-your-invoice-in-email-pdf"></a>Obtención de la factura por correo electrónico (.pdf)
Puede optar a destinatarios adicionales y configurarlos para recibir la factura de Azure en un correo electrónico. Esta característica puede no estar disponible para determinadas suscripciones, como ofertas de soporte técnico, contratos Enterprise o Azure bajo licencia Open.

1. Seleccione su suscripción en la [página Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Tendrá que habilitar la opción en cada suscripción que posea. Haga clic en **Facturas** y luego en **Email my invoice** (Enviar mi factura por correo electrónico). 

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Haga clic en **Recibir notificaciones** y acepte los términos.

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Una vez que ha aceptado el contrato, puede configurar destinatarios adicionales.

    ![Captura de pantalla que muestra el flujo de participación](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Si no recibe un correo electrónico después de seguir los pasos, asegúrese de que su dirección de correo electrónico es correcta en las [preferencias de comunicación de su perfil](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Descarga de la factura desde Azure Portal (.pdf)

1. Seleccione su suscripción en la [página Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) de Azure Portal como [un usuario con acceso a las facturas](billing-manage-access.md).

2. Seleccione **Facturas**. 

    ![Captura de pantalla que muestra la opción Facturación y uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Haga clic en **Descargar factura** para ver una copia de la factura en PDF. Si muestra **No disponible**, vea [¿Por qué no veo una factura para el último período de facturación?](#noinvoice)

    ![Captura de pantalla que muestra los períodos de facturación, la opción de descarga y los cargos totales para cada período de facturación](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. También puede ver el uso diario si hace clic en el período de facturación. 

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

Solo el administrador de la cuenta puede tener acceso al Centro de cuentas de Azure. Otros administradores de facturación, como un propietario, pueden obtener información de uso mediante las [API de facturación](billing-usage-rate-card-overview.md).

Para más información sobre el uso diario, consulte [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md). Para ayudar a administrar los costos, consulte [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing-getting-started.md).

## <a name="noinvoice"></a> ¿Por qué no veo una factura para el último período de facturación?

Pueden existir varias razones por las que no ve una factura:

- Tiene un importe de crédito mensual con la suscripción que no ha superado o tiene una evaluación gratuita. Solo se genera una factura cuando debe dinero.

- Han transcurrido menos de 30 días desde el día que se suscribió a Azure.

- La factura no se ha generado aún. Espere hasta el final del período de facturación.

- Si no es el administrador de la cuenta, es posible que las facturas anteriores no estén disponibles para usted.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

