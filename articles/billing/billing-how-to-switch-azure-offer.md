---
title: "Cambio de la oferta de suscripción de Azure | Microsoft Docs"
description: "Aprenda a cambiar su suscripción de Azure y a pasar a una oferta diferente mediante el Centro de cuentas de Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 381994079b7bcaeff08802b06573b977bf631e9d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Cambie la suscripción de pago por uso de Azure a otra oferta

Como cliente de [pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/), puede cambiar la suscripción a Azure por cualquier otra oferta del [Centro de cuentas](https://account.windowsazure.com/Subscriptions). Por ejemplo, esta característica se puede utilizar para aprovechar los [créditos mensuales de que disfrutan los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**¿Solo desea actualizar desde la evaluación gratuita?** Vea la [actualización de pago por uso](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Lo que se admite:

| De | Para |
| --- | --- |
| Pay-As-You-Go |[Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Pay-As-You-Go |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Pay-As-You-Go |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Pay-As-You-Go |[Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Pay-As-You-Go |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Pay-As-You-Go |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Para cambiar a otra oferta, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Cambio de oferta de suscripción

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Inicie sesión en [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions).
1. Seleccione una suscripción de pago por uso.
1. Haga clic en **Cambiar a otra oferta**. Este botón solo está disponible en las suscripciones de pago por uso tras el primer período de facturación.

   ![Observar el botón Cambiar oferta situado en el lado derecho de la página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Seleccione la oferta que desee** en la lista de suscripciones a las que puede cambiar. Dicha lista varía en función de los grupos a los que la cuenta está asociada. Si no hay ninguna disponible, compruebe la [lista de ofertas disponibles a las que puede cambiar](#whats-supported) y asegúrese de que pertenece a los grupos apropiados. 

   ![Seleccionar una oferta a la que desee cambiar](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Según la oferta a la que cambie, es posible que vea una nota sobre el impacto del cambio. Recorra esta lista detenidamente y siga las instrucciones antes de continuar.

   ![Revisar las notas](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Puede cambiar el nombre de la suscripción. De forma predeterminada, este se establece en el nombre de la nueva oferta. Haga clic en **Cambiar oferta** para completar el proceso.

   ![Hacer clic en el botón verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. ¡Éxito! Ya se ha cambiado la suscripción a la nueva oferta.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-is-an-azure-offer"></a>¿Qué es una oferta de Azure?

Una oferta de Azure es el *tipo* de la suscripción a Azure que tiene. Por ejemplo, [pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure bajo licencia Open](https://azure.microsoft.com/offers/ms-azr-0111p/) y [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) son todas las ofertas de Azure. Cada oferta tiene distintos [términos](https://azure.microsoft.com/support/legal/offer-details/) y algunos tienen ventajas especiales. La oferta de la suscripción se puede encontrar en la página de suscripción del Centro de cuentas. Haga clic en el nombre de la oferta para obtener más detalles.

   ![Haga clic en el vínculo de la oferta en el Centro de cuentas para obtener más detalles](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>¿Por qué no se ve el botón?

Puede que no vea el botón **Cambiar a otra oferta** en los siguientes casos:

* No tiene una suscripción de [pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Actualmente las únicas suscripciones que se pueden convertir a otra oferta son las de pago por uso.
  * Si usa una [evaluación gratuita](https://azure.microsoft.com/free/), aprenda a [actualizarla a la modalidad de pago por uso](billing-upgrade-azure-subscription.md).
  * Para cambiar de oferta desde otra suscripción [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Aún está en el primer período de facturación; para poder cambiar de oferta debe esperar a que finalice.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>¿Por qué no se ve el mensaje "No hay ninguna oferta disponible en su región o país en este momento"?

* Podría no reunir los requisitos para cambiar de oferta. Compruebe la [lista de ofertas disponibles a las que puede cambiar](#whats-supported) y asegúrese de que ha activado las ventajas adecuadas con Visual Studio o Bizspark.
* Es posible que algunas ofertas no estén disponibles en todos los países.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>¿Cómo afecta el cambio de ofertas de Azure ofrece a mi servicio y facturación?

Estos son los detalles de lo que ocurre cuando se cambian las ofertas de Azure en el Centro de cuentas.

#### <a name="no-service-downtime"></a>No hay ningún tiempo de inactividad del servicio

No hay tiempo de inactividad del servicio para los usuarios asociados a la suscripción. Pero es posible que la oferta a la que cambie tenga restricciones. Por ejemplo, algunas ofertas no permiten el uso de producción, por lo que tendría que mover los recursos de producción a otra suscripción.

#### <a name="quota-increases-are-reset"></a>Los aumentos de cuota se restablecen

Cuando cambia las ofertas, se restablece cualquier [aumento de límite o cuota por sobre el límite predeterminado](../azure-supportability/resource-manager-core-quotas-request.md). No hay ningún tiempo de inactividad del servicio, incluso si tiene más recursos más allá del límite predeterminado. Por ejemplo, si usa 200 núcleos en la suscripción y luego cambia las ofertas, la cuota de núcleos se restablece al valor predeterminado de 20 núcleos. Las máquinas virtuales que usan los 200 núcleos no se ven afectadas y podrían seguir en ejecución. Sin embargo, si no hace otra solicitud de aumento de cuota, no podrá aprovisionar más núcleos.

#### <a name="billing"></a>Facturación

El mismo día que se realiza el cambio se genera una factura por todos los cargos pendientes. Posteriormente, la suscripción se factura según los términos de precios de la nueva oferta. La fecha de facturación de la suscripción pasa a ser la fecha del cambio de oferta. Los datos de uso y facturación anteriores al cambio de oferta no se conservan, por lo que se recomienda descargar una copia de los mismos antes de realizar el cambio.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>¿Puedo migrar de la suscripción de pago por uso a la de [Proveedor de soluciones en la nube](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o a la de [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Para migrar a CSP, vea [Azure Pas-As-You-Go Subscription Migration to CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp) (Migración de la suscripción de pago por uso de Azure a CSP).
* Para migrar a EA, indique a su administrador de inscripciones que agregue su cuenta a dicho EA. Siga las instrucciones que aparecen en el correo electrónico de invitación para mover las suscripciones bajo la inscripción EA. Para más información, consulte [Associate an Existing Account](https://ea.azure.com/helpdocs/associateExistingAccount) (Asociar una cuenta existente) en el portal EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>¿Puedo migrar datos y servicios a nueva una suscripción?

* Puede migrar los recursos directamente a la nueva suscripción; consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md).
* Para transferir la propiedad de una suscripción a Azure y todo lo que implica a otra persona, consulte [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Transferencia de la propiedad de una suscripción a Azure).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
