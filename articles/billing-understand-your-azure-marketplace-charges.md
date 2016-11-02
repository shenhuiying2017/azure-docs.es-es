<properties
    pageTitle="Descripción de los gastos de servicios externos de Azure | Microsoft Azure"
    description="Obtenga información sobre la facturación de los servicios externos, anteriormente conocidos como Marketplace, en Azure."
    services=""
    documentationCenter=""
    authors="adpick"
    manager="felixwu"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="adpick"/>


# <a name="understand-your-azure-external-service-charges"></a>Descripción de los gastos de servicios externos de Azure

Este artículo explica la facturación de los servicios externos de Azure. Los servicios externos se solían llamar pedidos de Marketplace. Los servicios externos los proporcionan proveedores de servicios independientes, pero están integrados completamente en el ecosistema de Azure. Obtenga información sobre cómo:

- Identificar servicios externos
- Entender cómo la facturación difiere de la de otros recursos de Azure
- Ver y realizar un seguimiento de los costos acumulados por el uso de servicios externos
- Administrar pedidos de servicios externos y la forma de pago para ellos

## <a name="what-are-azure-external-services?"></a>¿Qué son los servicios externos de Azure?

Los servicios externos se solían llamar Azure Marketplace. Por lo general, son servicios publicados por parte de terceros que están disponibles para Azure. Por ejemplo, ClearDB y SendGrid son servicios externos que puede adquirir en Azure, pero que no están publicados por Microsoft.

### <a name="identify-external-services"></a>Identificación de servicios externos

Cuando aprovisiona un nuevo servicio externo o un recurso, aparece una advertencia:

![Advertencia de compra de Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

>[AZURE.NOTE] Los servicios externos los publican empresas distintas a Microsoft pero, a veces, los productos de Microsoft también se pueden clasificar como servicios externos.

### <a name="external-services-are-billed-separately"></a>Los servicios externos se facturan por separado

Los servicios externos se tratan como pedidos individuales dentro de la suscripción de Azure. El período de facturación para cada servicio se establece al adquirir el servicio. No debe confundirse con el período de facturación de la suscripción en la que lo adquirió. Además, recibirá facturas independientes y se le cobrará en su tarjeta de crédito por separado.

### <a name="each-external-service-has-a-different-billing-model"></a>Cada servicio externo tiene un modelo de facturación diferente

Algunos servicios se facturan mediante la modalidad de pago por uso, mientras que otros utilizan un modelo basado en el pago mensual. Necesitará una tarjeta de crédito para los servicios externos de Azure ya que no se pueden comprar servicios externos mediante el pago de factura.

### <a name="you-can't-use-monthly-free-credits-for-external-services"></a>No puede usar créditos mensuales gratuitos para los servicios externos

Si usa una suscripción de Azure que incluye [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), estos no se podrán aplicar a las facturas de los servicios externos. Utilice una tarjeta de crédito para adquirir servicios externos.

## <a name="view-external-service-spending-and-history"></a>Visualización de los gastos y el historial de servicios externos

Puede ver una lista de los servicios externos que están en cada suscripción dentro de [Azure Portal](https://portal.azure.com/): 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y [vaya a la hoja **Facturación**](https://portal.azure.com/?flight=1#blade/Microsoft_Azure_Billing/BillingBlade).

    ![Seleccionar Facturación en el menú del concentrador](./media/billing-understand-your-azure-marketplace-charges/billing-button.png) 
  
2. En la sección **Costos de suscripción**, seleccione la suscripción que desee consultar. 
   
    ![Seleccione una suscripción en la hoja de facturación](./media/billing-understand-your-azure-marketplace-charges/select-sub.png)

3. Haga clic en **Servicios externos**.

    ![Haga clic en servicios externos en la hoja de suscripción](./media/billing-understand-your-azure-marketplace-charges/external-service-blade.png)

4. Debería ver cada uno de los pedidos de servicios externos, el nombre del publicador, el nivel de servicio que haya comprado, el nombre que ha asignado al recurso y el estado actual del pedido. Seleccione un servicio externo para ver las facturas anteriores.

    ![Seleccionar un servicio externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)

5. Desde aquí, puede consultar los importes de facturas pasadas con el desglose de impuestos incluido.

    ![Ver historial de facturación de los servicios externos](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>Administración de los métodos de pago para pedidos de servicios externos

Actualice los métodos de pago para los pedidos de servicios externos en el [Centro de cuentas](https://account.windowsazure.com/).

> [AZURE.NOTE] Si adquirió la suscripción con una cuenta profesional o educativa debería [ponerse en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para realizar cambios en el método de pago.

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

## <a name="need-help?-contact-support."></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.



<!--HONumber=Oct16_HO2-->


