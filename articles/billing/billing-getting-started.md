---
title: "Evitar costos inesperados y administrar la facturación: Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo evitar cargos inesperados en la factura de Azure. Use características de administración y seguimiento del costo de las suscripciones a Microsoft Azure."
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.openlocfilehash: d74f649a8b5f35ffe16479576959e5ebb2857bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Prevención de cargos inesperados con la administración de costos y facturación de Azure

Al suscribirse a Azure, hay varias cosas que puede hacer para obtener una idea más clara de los gastos. La [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) puede proporcionar una estimación de los costos antes de crear un recurso de Azure. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) proporciona el desglose de costos actual y la previsión correspondientes a la suscripción. Si desea agrupar y comprender los costos para distintos proyectos o equipos, consulte el [etiquetado de recursos](../azure-resource-manager/resource-group-using-tags.md). Si prefiere usar el sistema de informes del que dispone su organización, compruebe las [API de facturación](billing-usage-rate-card-overview.md). 

- Si la suscripción es un Contrato Enterprise (EA), la versión preliminar pública para ver los costos en Azure Portal está disponible. Si realiza la suscripción a través de un Proveedor de soluciones en la nube (CSP) o Patrocinio de Azure, no podrá aplicar algunas de las siguientes características. Vea [Recursos adicionales para EA, CSP y Patrocinio](#other-offers) para obtener más información.

- Si la suscripción es una evaluación gratuita, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), Azure bajo licencia Open (AIO) o BizSpark, la suscripción se deshabilitará automáticamente cuando se usen todos los créditos. Infórmese sobre los [límites de gasto](#spending-limit) para evitar que la suscripción se deshabilite inesperadamente.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtención de costos estimados antes de agregar servicios de Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimación del coste en línea con la calculadora de precios

Consulte la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) para obtener un costo mensual estimado del servicio que le interesa. Puede agregar cualquier recurso de Azure propio para obtener un costo estimado.

![Captura de pantalla del menú de la calculadora de precios](./media/billing-getting-started/pricing-calc.png)

Por ejemplo, se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes en horas de proceso si la deja ejecutándose todo el tiempo:

![Captura de pantalla de la calculadora de precios en la que se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes](./media/billing-getting-started/pricing-calcVM.png)

Para más información sobre los precios, consulte estas [preguntas más frecuentes](https://azure.microsoft.com/pricing/faq/). O bien, si desea comunicarse con un vendedor de Azure, llame al 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Revisión del costo estimado en Azure Portal

Normalmente, cuando se agrega un servicio en Azure Portal, hay una vista que le muestra un coste estimado similar al mes. Por ejemplo, al elegir el tamaño de la máquina virtual Windows verá el costo mensual estimado de las horas de proceso:

![Ejemplo: se estima que una máquina virtual Windows A1 cuesta 66,96 dólares al mes](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Configurar alertas de facturación para las suscripciones de Microsoft Azure

Configure alertas de facturación para recibir mensajes de correo electrónico cuando los costes de uso superen el importe que especifique. Si dispone de créditos mensuales, configure alertas para cuando supere una cantidad específica. Para obtener más información, consulte [Configurar alertas de facturación para las suscripciones de Microsoft Azure](billing-set-up-alerts.md).

![Captura de pantalla de un correo electrónico de alerta de facturación](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Esta característica está todavía en versión preliminar, por lo que debe comprobar periódicamente el uso.

Puede usar la estimación del coste de la calculadora de precios como guía para la primera alerta.

### <a name="spending-limit"></a> Comprobación para saber si tiene un límite de gasto activado

Si tiene una suscripción que usa créditos, el límite de gasto estará activo de forma predeterminada. De esta manera, cuando gasta todos sus créditos, no se le cobra en su tarjeta de crédito. Consulte la [lista completa de ofertas de Azure y la disponibilidad del límite de gasto](https://azure.microsoft.com/support/legal/offer-details/).

Sin embargo, si se alcanza el límite de gasto, se deshabilitarán sus servicios. Esto significa que se desasignarán sus máquinas virtuales. Para evitar el tiempo de inactividad del servicio debe desactivar el límite de gasto. Cualquier uso por encima del límite se cargará en su tarjeta de crédito guardada. 

Para ver si tiene activo un límite de gasto, vaya a la [vista de suscripciones del Centro de cuentas](https://account.windowsazure.com/Subscriptions). Aparecerá un mensaje emergente si su límite de gasto está activo:

![Captura de pantalla que muestra una advertencia sobre un límite de gasto que está activo en el Centro de cuentas](./media/billing-getting-started/spending-limit-banner.PNG)

Haga clic en el mensaje emergente y siga las indicaciones para quitar el límite de gasto. Si no facilitó la información de la tarjeta de crédito cuando se suscribió, deberá facilitarla ahora para quitar el límite de gasto. Para más información, consulte [Límite de gasto de Azure: cómo funciona y cómo habilitarlo o quitarlo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Formas de supervisar los costos cuando se usan servicios de Azure

### <a name="tags"></a> Incorporación de etiquetas a los recursos para agrupar los datos de facturación

Para los servicios compatibles, puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, si ejecuta varias máquinas virtuales para los distintos equipos, puede utilizar etiquetas para clasificar los costes por centro de coste (Recursos humanos, Marketing, Finanzas) o por entorno (producción, preproducción, pruebas). 

![Captura de pantalla que muestra la configuración de etiquetas en el portal](./media/billing-getting-started/tags.PNG)

Las etiquetas se mostrarán a lo largo de las diferentes vistas de informe de costes. Por ejemplo, son visibles directamente en la [vista de análisis de costes](#costs) y en los archivos [.csv de uso detallados](#invoice-and-usage) después del primer período de facturación.

Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Visite el portal con regularidad para conocer el análisis de costes y la tasa de evolución

Después de ejecutar los servicios, compruebe regularmente el coste de estos. Puede ver el gasto actual y la tasa de evolución en Azure Portal. 

1. Vaya a la [hoja de suscripciones en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) y seleccione una suscripción.

2. El análisis de coste y la tasa de evolución deben aparecer en la hoja emergente. Es posible que su oferta no lo admita (en cuyo caso, se mostrará una advertencia en la parte superior).

    ![Captura de pantalla de la tasa de evolución y el análisis de coste en Azure Portal](./media/billing-getting-started/burn-rate.PNG)

3. Haga clic en **Análisis de costes** en la lista de la izquierda para ver el análisis de costes por recursos. Espere 24 horas después de agregar un servicio para que se rellenen los datos.

    ![Captura de pantalla de la vista de análisis de costes en Azure Portal](./media/billing-getting-started/cost-analysis.PNG)

4. Puede filtrar por diferentes propiedades como [etiquetas](#tags), grupo de recursos e intervalo de tiempo. Haga clic en **Aplicar** para confirmar los filtros y en **Descargar** si desea exportar la vista a un archivo de valores separados por comas (.csv).

5. Además, puede hacer clic en un recurso para ver el historial diario de gastos y cuánto cuesta cada día el recurso.

    ![Captura de pantalla de la vista de historial de gastos en Azure Portal](./media/billing-getting-started/costhistory.PNG)

Le recomendamos que compare los costes reales con las estimaciones que vio cuando seleccionó los servicios. Si los costes difieren mucho de las estimaciones, debe comprobar el plan de precios que ha seleccionado para los recursos (máquinas virtuales A1 en lugar de A0, por ejemplo). 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Habilitación de medidas de reducción de costes como el apagado automático de las máquinas virtuales

Según el escenario, puede configurar el apagado automático de las máquinas virtuales en Azure Portal. Para más información, consulte [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Apagado automático de máquinas virtuales mediante Azure Resource Manager)///.

![Captura de pantalla de la opción de apagado automático en el portal](./media/billing-getting-started/auto-shutdown.PNG)

El apagado automático no es lo mismo que cuando apaga la máquina virtual desde dentro mediante las opciones de energía. El apagado automático detiene y desasigna las máquinas virtuales para impedir gastos por uso adicionales. Para más información, consulte las preguntas más frecuentes sobre precios de [máquinas virtuales Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) y [máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y sobre los estados de máquinas virtuales.

Para conocer más medidas de reducción de costes de los entornos de desarrollo y pruebas, visite [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Active y compruebe las recomendaciones de Azure Advisor

[Azure Advisor](../advisor/advisor-overview.md) es una característica en versión preliminar que ayuda a reducir los costes, ya que identifica los recursos que se usan poco. Actívelo en Azure Portal:

![Captura de pantalla del botón de Azure Advisor en Azure Portal](./media/billing-getting-started/advisor-button.PNG)

Puede obtener recomendaciones prácticas sobre en la pestaña **Cost** (Costo) del panel de Advisor:

![Captura de pantalla de ejemplo de recomendación de coste de Advisor](./media/billing-getting-started/advisor-action.PNG)

Para obtener más información, consulte las [recomendaciones sobre el costo de Advisor](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Revisión de los costos al final del ciclo de facturación

Después del final del ciclo de facturación, la factura estará disponible. También puede [descargar facturas anteriores y archivos de uso de detalle](billing-download-azure-invoice-daily-usage-date.md) para garantizar que se le cobró correctamente. Para más información sobre cómo comparar el uso diario con la factura, consulte [Entienda su factura de Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de facturación

Utilice la API de facturación para obtener mediante programación los datos de uso. Use la API de RateCard y la API de uso juntas para obtener el uso facturado. Para más información, consulte [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Recursos adicionales y casos especiales

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes patrocinadores, CSP y EA
Hable con el administrador de la cuenta o el asociado de Azure para conocer más información.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal EA](https://ea.azure.com/), [documentos de ayuda](https://ea.azure.com/helpdocs) e [informe de Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Proveedor de soluciones en la nube (CSP) | Hable con el proveedor |
| Patrocinio de Azure | [Portal de patrocinio](https://www.microsoftazuresponsorships.com/) |

Si es el administrador de una infraestructura de TI para una organización grande, es recomendable que vea las [plantillas scaffold empresariales de Azure](../azure-resource-manager/resource-manager-subscription-governance.md) y las [notas del producto TI empresarial](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (descarga en .pdf, solo en inglés).

#### <a name="EA"></a>Vistas de los costos del Contrato Enterprise de la versión preliminar dentro de Azure Portal 

Las vistas de los costos de Enterprise están actualmente en la versión preliminar pública. Elementos que tener en cuenta:
- Los costos de suscripción se basan en el uso y no tienen en cuenta los importes de prepago, uso por encima del límite, cantidades incluidas, ajustes e impuestos. Los cargos reales se calculan en el nivel de inscripción. 
- Los importes que se muestran en Azure Portal podrían estar retrasados con respecto a los valores del portal de Enterprise.  
- Si no ve los costos, puede deberse a uno de los siguientes motivos:
    - Es el propietario de la cuenta y su administrador de inscripción ha deshabilitado la opción para que el propietario de la cuenta vea los cargos.  Póngase en contacto con el administrador de inscripción para obtener acceso a los costos. 
    - Es el administrador del departamento y su administrador de inscripción ha deshabilitado la opción para que el administrador del departamento vea los cargos.  Póngase en contacto con el administrador de inscripción para obtener acceso. 
    - Adquirió Azure a través de un asociado de canal y el asociado no ha publicado la información de los precios.  
- Cuando se actualiza la configuración relacionada con el acceso a los costos en Enterprise Portal, hay un retraso de unos minutos antes de que los cambios se reflejen en Azure Portal.
- El límite de gasto, las alertas de facturación y la guía de facturación no se corresponden con las suscripciones EA.

### <a name="check-your-subscription-and-access"></a>Comprobación de la suscripción y el acceso

La visualización de costes requiere [acceso de nivel de suscripciones a la información de facturación](billing-manage-access.md), pero solo el administrador de la cuenta puede acceder al [Centro de cuentas](https://account.azure.com/Subscriptions), cambiar la información de facturación y administrar las suscripciones. El administrador de la cuenta es la persona que ha completado el proceso de suscripción. Para más información, consulte [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios](billing-add-change-azure-subscription-administrator.md).

Para ver si usted es el administrador de la cuenta, vaya a la [hoja de suscripciones en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) y examine la lista de suscripciones a las que tiene acceso. Busque en **Mi rol**. Si dice *Administrador de cuenta*, entonces, todo correcto. Si dice algo más como *Propietario*, entonces no tiene privilegios completos.

![Captura de pantalla de su rol en la vista de suscripciones de Azure Portal](./media/billing-getting-started/sub-blade-view.PNG)

Si no es el administrador de la cuenta, posiblemente alguien le dio acceso parcial mediante el [control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md) (RBAC). Para administrar suscripciones y cambiar la información de facturación [averigüe quién es el administrador de la cuenta](billing-subscription-transfer.md#whoisaa) y pídale que realice las tareas o que le [transfiera la suscripción](billing-subscription-transfer.md).

Si el administrador de la cuenta ya no está en su organización y necesita administrar la facturación, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
