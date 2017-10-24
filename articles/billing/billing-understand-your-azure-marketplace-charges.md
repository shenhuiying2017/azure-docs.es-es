---
title: "Descripción de los gastos de servicios externos de Azure | Microsoft Docs"
description: "Obtenga información sobre la facturación de los servicios externos, anteriormente conocidos como Marketplace, en Azure."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Descripción de la facturación de Azure para gastos de servicios externos
Los servicios externos los publican proveedores de software de terceros en Azure Marketplace. Por ejemplo, ClearDB y SendGrid son servicios externos que puede adquirir en Azure, pero que no están publicados por Microsoft.

Cuando aprovisiona un nuevo servicio externo o un recurso, aparece una advertencia:

![Advertencia de compra de Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Los servicios externos los publican empresas distintas a Microsoft pero, a veces, los productos de Microsoft también se pueden clasificar como servicios externos.
> 
> 

## <a name="how-external-services-are-billed"></a>Cómo se facturan los servicios externos
- Los servicios externos se facturan por separado. Los servicios externos se tratan como pedidos individuales dentro de la suscripción de Azure. El período de facturación para cada servicio se establece al adquirir el servicio. No debe confundirse con el período de facturación de la suscripción en la que lo adquirió. Además, recibirá facturas independientes y se le cobrará en su tarjeta de crédito por separado.
- Cada servicio externo tiene un modelo de facturación diferente. Algunos servicios se facturan mediante la modalidad de pago por uso, mientras que otros utilizan un modelo basado en el pago mensual. Necesitará una tarjeta de crédito para los servicios externos de Azure ya que no se pueden comprar servicios externos mediante el pago de factura.
- No puede usar créditos mensuales gratuitos para los servicios externos. Si usa una suscripción de Azure que incluye [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), estos no se podrán aplicar a las facturas de los servicios externos. Utilice una tarjeta de crédito para adquirir servicios externos.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Visualización de los gastos y el historial de servicios externos en Azure Portal
Puede ver una lista de los servicios externos que están en cada suscripción dentro de [Azure Portal](https://portal.azure.com/): 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de cuenta.
2. En el menú de concentrador, seleccione **Suscripción**.
   
    ![En el menú de concentrador, seleccione Suscripción.](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. En la hoja **Suscripciones**, seleccione la suscripción que desea ver y, a continuación, seleccione **Servicios externos**.
   
    ![Seleccione una suscripción en la hoja de facturación](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Debería ver cada uno de los pedidos de servicios externos, el nombre del publicador, el nivel de servicio que haya comprado, el nombre que ha asignado al recurso y el estado actual del pedido. Seleccione un servicio externo para ver las facturas anteriores.
   
    ![Seleccionar un servicio externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Desde aquí, puede consultar los importes de facturas pasadas con el desglose de impuestos incluido.
   
    ![Ver historial de facturación de los servicios externos](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Visualización de los gastos de los servicios externos para los clientes de Contrato Enterprise (EA)
Los clientes de EA pueden ver los gastos de los servicios externos y descargar los informes en el portal de EA. Lea el artículo sobre [Azure Marketplace para clientes de EA](https://ea.azure.com/helpdocs/azureMarketplace) para empezar a trabajar.

## <a name="manage-payment-methods-for-external-service-orders"></a>Administración de los métodos de pago para pedidos de servicios externos
Actualice los métodos de pago para los pedidos de servicios externos en el [Centro de cuentas](https://account.windowsazure.com/).

> [!NOTE]
> Si adquirió la suscripción con una cuenta profesional o educativa, debería [ponerse en contacto con el servicio de asistencia técnica](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para realizar cambios en el método de pago.
> 
> 

1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/) y [vaya a la pestaña **Marketplace**](https://account.windowsazure.com/Store)
   
    ![Seleccione Marketplace en el Centro de cuentas](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Seleccione el servicio externo que desea administrar
   
    ![Seleccione el servicio externo que desea administrar](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. En el lado derecho de la página, haga clic en **Cambiar método de pago**. Este vínculo le permite acceder a un portal diferente para administrar la forma de pago.
   
    ![Resumen del pedido](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Haga clic en **Editar información** y siga las instrucciones para actualizar su información de pago.
   
    ![Seleccione Editar información](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Cancelación de un pedido de servicio externo
Si desea cancelar el pedido de servicio externo, debe eliminar el recurso en [Azure Portal](https://portal.azure.com).

![Eliminar recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si tiene más preguntas, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

