---
title: "Evitar costos inesperados y administrar la facturación: Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo evitar cargos inesperados en la factura de Azure. Use características de administración y seguimiento del costo de las suscripciones a Microsoft Azure."
services: 
documentationcenter: 
author: jlian
manager: mattstee
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: jlian
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c69334279e79352e3f7ad58d356f6891dd060b0a
ms.lasthandoff: 04/03/2017


---
# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>Prevención de costes inesperados con la administración de costes y facturación de Azure

Al suscribirse a Azure, hay varias cosas que puede hacer para obtener una idea más clara de los gastos. Si selecciona la suscripción en [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), puede ver el análisis de los costes y la tasa de evolución actuales. También se puede [descargar facturas pasadas y archivos de uso detallados](billing-download-azure-invoice-daily-usage-date.md). Si desea agrupar los costes para los recursos usados por diferentes proyectos o equipos, consulte el [etiquetado de recursos](../azure-resource-manager/resource-group-using-tags.md). Si prefiere usar el sistema de informes del que dispone su organización, compruebe las [API de facturación](billing-usage-rate-card-overview.md). 

Para más información sobre el uso diario, consulte [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md).

Si realiza la suscripción en el marco de un Contrato Enterprise (EA), un Proveedor de soluciones en la nube (CSP) o Patrocinio de Azure, no podrá aplicar muchas de las características de este artículo. En su lugar, tiene a su disposición un conjunto diferente de herramientas que puede usar para administrar costes. Vea [Recursos adicionales para EA, CSP y Patrocinio](#other-offers).

Si la suscripción es una evaluación gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure bajo licencia Open (AIO) o BizSpark, lea información sobre los [límites de gasto](#spending-limit) para evitar que la suscripción se deshabilite de forma inesperada. 

## <a name="day-0-before-you-add-azure-services"></a>Día 0: Antes de agregar servicios de Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimación del coste en línea con la calculadora de precios

Consulte la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) y la [calculadora de coste total de propiedad](https://aka.ms/azure-tco-calculator) para obtener una estimación del coste mensual del servicio que le interesa. Por ejemplo, se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes en horas de proceso si la deja ejecutándose todo el tiempo:

![Captura de pantalla de la calculadora de precios en la que se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes](./media/billing-getting-started/pricing-calc.PNG)

Para más información, consulte las [preguntas más frecuentes sobre precios](https://azure.microsoft.com/pricing/faq/). O bien, si desea comunicarse con una persona, llame al 1-800-867-1389.

### <a name="check-your-subscription-and-access"></a>Comprobación de la suscripción y el acceso

La visualización de costes requiere [acceso de nivel de suscripciones](../active-directory/role-based-access-control-configure.md), pero solo el administrador de la cuenta puede acceder al [Centro de cuentas](https://account.windowsazure.com/Home/Index), cambiar la información de facturación y administrar las suscripciones. El administrador de la cuenta es la persona que ha completado el proceso de suscripción. Para más información, consulte [Incorporación o cambio de roles de administrador de Azure](billing-add-change-azure-subscription-administrator.md).

Para ver si usted es el administrador de la cuenta, vaya a la [hoja de suscripciones en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) y examine la lista de suscripciones a las que tiene acceso. Busque en **Mi rol**. Si dice *Administrador de cuenta*, entonces, todo correcto. Si dice algo más como *Propietario*, entonces no tiene privilegios completos.

![Captura de pantalla de su rol en la vista de suscripciones de Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Si no es el administrador de la cuenta, posiblemente alguien le dio acceso parcial mediante el [control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md) (RBAC). Para administrar suscripciones y cambiar la información de facturación [averigüe quién es el administrador de la cuenta](billing-subscription-transfer.md#whoisaa) y pídale que realice las tareas o que le [transfiera la suscripción](billing-subscription-transfer.md).

Si el administrador de la cuenta ya no está en su organización y necesita administrar la facturación, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

### <a name="spending-limit"></a> Comprobación para saber si tiene un límite de gasto activado

Si tiene una suscripción que usa créditos, el límite de gasto estará activo de forma predeterminada. De esta manera, cuando gasta todos sus créditos, no se le cobra en su tarjeta de crédito. Consulte la [lista completa de ofertas de Azure y la disponibilidad del límite de gasto](https://azure.microsoft.com/support/legal/offer-details/).

Sin embargo, si se alcanza el límite de gasto, se deshabilitarán sus servicios. Esto significa que se desasignarán sus máquinas virtuales. Para evitar el tiempo de inactividad del servicio debe desactivar el límite de gasto. Cualquier uso por encima del límite se cargará en su tarjeta de crédito guardada. 

Para ver si tiene activo un límite de gasto, vaya a la [vista de suscripciones del Centro de cuentas](https://account.windowsazure.com/Subscriptions). Aparecerá un mensaje emergente si su límite de gasto está activo:

![Captura de pantalla que muestra una advertencia sobre un límite de gasto que está activo en el Centro de cuentas](./media/billing-getting-started/spending-limit-banner.PNG)

Haga clic en el mensaje emergente y siga las indicaciones para quitar el límite de gasto. Si no facilitó la información de la tarjeta de crédito cuando se suscribió, deberá facilitarla ahora para quitar el límite de gasto. Para más información, consulte [Límite de gasto de Azure: cómo funciona y cómo habilitarlo o quitarlo](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="set-up-billing-alerts"></a>Configurar alertas de facturación para las suscripciones de Microsoft Azure

Configure alertas de facturación para recibir mensajes de correo electrónico cuando los costes de uso superen el importe que especifique. Si dispone de créditos mensuales, configure alertas para cuando supere una cantidad específica. Para obtener más información, consulte [Configurar alertas de facturación para las suscripciones de Microsoft Azure](billing-set-up-alerts.md).

![Captura de pantalla de un correo electrónico de alerta de facturación](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Esta característica está todavía en versión preliminar, por lo que debe comprobar periódicamente el uso.

Puede usar la estimación del coste de la calculadora de precios como guía para la primera alerta.

### <a name="understand-limits-and-quotas-for-your-subscription"></a>Descripción de los límites y cuotas de su suscripción

Hay límites predeterminados para cada suscripción, para cosas como el número de núcleos de CPU y direcciones IP. Esté atento a estos límites. Para más información, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md). Puede solicitar un aumento de este límite o cuota [contactando con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="day-1-as-you-add-services"></a>Día 1: A medida que agrega servicios

### <a name="review-the-estimated-cost-in-the-portal"></a>Revisión del coste estimado en el portal

Normalmente, cuando se agrega un servicio en Azure Portal, hay una vista que le muestra un coste estimado similar al mes. Por ejemplo, al elegir el tamaño de la máquina virtual Windows verá el coste mensual estimado de las horas de proceso:

![Ejemplo: se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a> Incorporación de etiquetas a los recursos para agrupar los datos de facturación

Para los servicios compatibles, puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, si ejecuta varias máquinas virtuales para los distintos equipos, puede utilizar etiquetas para clasificar los costes por centro de coste (Recursos humanos, Marketing, Finanzas) o por entorno (producción, preproducción, pruebas). 

![Captura de pantalla que muestra la configuración de etiquetas en el portal](./media/billing-getting-started/tags.PNG)

Las etiquetas se mostrarán a lo largo de las diferentes vistas de informe de costes. Por ejemplo, son visibles directamente en la [vista de análisis de costes](#costs) y en los archivos [.csv de uso detallados](#invoice-and-usage) después del primer período de facturación.

Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Habilitación de medidas de reducción de costes como el apagado automático de las máquinas virtuales

Según el escenario, puede configurar el apagado automático de las máquinas virtuales en Azure Portal. Para más información, consulte [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Apagado automático de máquinas virtuales mediante Azure Resource Manager)///.

![Captura de pantalla de la opción de apagado automático en el portal](./media/billing-getting-started/auto-shutdown.PNG)

El apagado automático no es lo mismo que cuando apaga la máquina virtual desde dentro mediante las opciones de energía. El apagado automático detiene y desasigna las máquinas virtuales para impedir gastos por uso adicionales. Para más información, consulte las preguntas más frecuentes sobre precios de [máquinas virtuales Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) y [máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y sobre los estados de máquinas virtuales.

Para conocer más medidas de reducción de costes de los entornos de desarrollo y pruebas, visite [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

## <a name="cost-reporting"></a> Día 2+: Después de usar los servicios, vea el uso

### <a name="costs"></a> Visite el portal con regularidad para conocer el análisis de costes y la tasa de evolución

Después de ejecutar los servicios, compruebe regularmente el coste de estos. Puede ver el gasto actual y la tasa de evolución en Azure Portal. 

1. Visite la [hoja de suscripciones en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Seleccione la suscripción que desea consultar. Es posible que solo tenga una que seleccionar.

3. El análisis de coste y la tasa de evolución deben aparecer en la hoja emergente. Es posible que su oferta no lo admita (en cuyo caso, se mostrará una advertencia en la parte superior). Espere 24 horas después de agregar un servicio para que se rellenen los datos.
    
    ![Captura de pantalla de la tasa de evolución y el análisis de coste en Azure Portal](./media/billing-getting-started/burn-rate.PNG)

4. Puede anclar la vista al panel.

    ![Captura de pantalla del anclado de una vista al panel](./media/billing-getting-started/pin.PNG)

5. Haga clic en **Análisis de costes** en la lista de la izquierda para ver el análisis de costes por recursos.

    ![Captura de pantalla de la vista de análisis de costes en Azure Portal](./media/billing-getting-started/cost-analysis.PNG)

6. Puede filtrar por diferentes propiedades como [etiquetas](#tags), grupo de recursos e intervalo de tiempo. Haga clic en **Aplicar** para confirmar los filtros y en **Descargar** para exportar la vista a un archivo de valores separados por comas (.csv).

7. Haga clic en un recurso para consultar el historial de gastos y el importe que le cuesta cada día.

    ![Captura de pantalla de la vista de historial de gastos en Azure Portal](./media/billing-getting-started/spend-history.PNG)

Le recomendamos que compare los costes reales con las estimaciones que vio cuando seleccionó los servicios. Si los costes difieren mucho de las estimaciones, debe comprobar el plan de precios que ha seleccionado para los recursos (máquinas virtuales A1 en lugar de A0, por ejemplo). 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>Revisión de los costes de todas las suscripciones en la hoja Facturación

Si administra varias suscripciones como administrador de cuenta, puede ver el importe agregado de la factura y el desglose de todas las suscripciones en la [hoja Facturación](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade). 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Active y compruebe las recomendaciones de Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) es una característica en versión preliminar que ayuda a reducir los costes, ya que identifica los recursos que se usan poco. Actívelo en Azure Portal:

![Captura de pantalla del botón de Azure Advisor en Azure Portal](./media/billing-getting-started/advisor-button.PNG)

Puede obtener recomendaciones prácticas sobre en la pestaña **Cost** (Costo) del panel de Advisor:

![Captura de pantalla de ejemplo de recomendación de coste de Advisor](./media/billing-getting-started/advisor-action.PNG)

Para obtener más información, consulte las [recomendaciones sobre el costo de Advisor](../advisor/advisor-cost-recommendations.md).

### <a name="invoice-and-usage"></a> Obtención de la factura y de los archivos de detalles de uso después del primer período de facturación

Después del primer período de facturación, puede descargar la factura en formato Portable Document Format (.pdf) y los detalles de uso como archivos de valores separados por comas (.csv). También puede decidir recibir la factura por correo electrónico. Estos archivos le ayudarán a entender lo que realmente se le factura después de impuestos, descuentos y créditos. Si no tiene un método de pago asociado a la suscripción, es posible que estos archivos no estén disponibles. Para obtener más información, consulte [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md) y [Comprender la factura de Microsoft Azure](billing-understand-your-bill.md).

![Captura de pantalla de una factura en formato .pdf](./media/billing-getting-started/invoice.png)

Las etiquetas establecidas anteriormente aparecen en los archivos .csv de detalles de uso:

![Captura de pantalla que muestra las etiquetas de los archivos .csv de detalles de uso](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>API de facturación

Utilice la API de facturación para obtener mediante programación los datos de uso. Use la API de RateCard y la API de uso juntas para obtener el uso facturado. Para más información, consulte [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Recursos adicionales para EA, CSP y Patrocinio

Hable con el administrador de la cuenta o el asociado de Azure para conocer más información.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal EA](https://ea.azure.com/), [documentos de ayuda](https://ea.azure.com/helpdocs) e [informe de Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Proveedor de soluciones en la nube (CSP) | Hable con el proveedor |
| Patrocinio de Azure | [Portal de patrocinio](https://www.microsoftazuresponsorships.com/) |

Si es el administrador de una infraestructura de TI para una organización grande, es recomendable que vea las [plantillas scaffold empresariales de Azure](../azure-resource-manager/resource-manager-subscription-governance.md) y las [notas del producto TI empresarial](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (descarga en .pdf, solo en inglés).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
