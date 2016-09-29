<properties
	pageTitle="Cambio de la suscripción de Azure a otra oferta | Microsoft Azure"
	description="Aprenda a cambiar su suscripción de Azure y a pasar a una oferta diferente mediante el portal de administración de suscripciones."
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="genli"/>

# Cambio de la suscripción de Azure a otra oferta

## Lo que necesita saber

Es posible que [reúna los requisitos](#where-is-the-button) para cambiar su suscripción por una oferta más favorable, sin tiempo de inactividad en los servicios. A continuación se muestran las rutas de acceso que se admiten actualmente en el [Centro de cuentas](https://account.windowsazure.com/Subscriptions). Haga clic en los vínculos para información sobre los requisitos.

| De | Para |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pago por adelantado de 12 meses](https://azure.microsoft.com/offers/ms-azr-0026p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |
> [AZURE.NOTE] Si está en la evaluación gratuita, conozca cómo [actualizar a pago por uso](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

> Si hace poco que se ha registrado para obtener una suscripción, debe esperar a que finalice su primer período de facturación antes de poder cambiar de oferta.

> Para otros cambios de oferta, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
	
## Guía para el cambio de la suscripción a otra oferta

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	Inicie sesión en [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions).
2.	Seleccione la suscripción que va a cambiar.
3.	Haga clic en **Cambiar a otra oferta**. Descubra por qué [si no ve el botón](#where-is-the-button).

	![siwtchbutton](./media/billing-how-to-switch-azure-offer/switchbutton.png)
	
4.	Se le presentarán todas las ofertas a las que puede cambiar la suscripción. Esta lista varía según las pertenencias a las que su cuenta está asociada, así como por la oferta actual. Puede hacer clic en el vínculo **Más información** en cada oferta para ver información detallada. Haga clic en la oferta que quiera para continuar con el paso siguiente.

	![selectoffer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5.	Según la oferta a la que cambie, es posible que vea una nota sobre el impacto del cambio. Recorra esta lista detenidamente y siga las instrucciones antes de continuar.

	![thingstonote](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6.	Puede cambiar el nombre de la suscripción. De forma predeterminada, este se establece en el nombre de la nueva oferta. Haga clic en **Cambiar oferta** para completar el proceso.

	![confirmpage](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7.	¡Éxito! Ya se ha cambiado la suscripción a la nueva oferta.

## Preguntas más frecuentes

### ¿Qué cambios de oferta admite?

Vea la lista de [ofertas disponibles a las que puede cambiar](#what-you-need-to-know).

### ¿Dónde está el botón?

Puede que no vea el botón **Cambiar a otra oferta** en los siguientes casos:

- Todavía está en el primer período de facturación; debe esperar a que finalice antes de poder cambiar de oferta.

- No reúne los requisitos para ningún cambio de oferta. Compruebe la lista de [ofertas disponibles a las que puede cambiar](#what-you-need-to-know) para asegurarse de que se admite el cambio de oferta deseado.

### ¿Tendrán las ofertas de cambio consecuencias en el servicio?

No hay ningún tiempo de inactividad del servicio. Pero es posible que la oferta a la que cambie tenga restricciones. Por ejemplo, algunas ofertas prohíben el uso en producción. Por tanto, tendrá que mover los recursos de producción a otra suscripción.

### ¿Tendrán las ofertas de cambio consecuencias en los administradores del servicio? 

No repercuten en los usuarios asociados a la suscripción.

### ¿Qué cambios relacionados con la facturación puedo esperar al cambiar de oferta?

El día que realiza el cambio, se generará una factura por todos los cargos pendientes. Posteriormente, la suscripción se facturará según los términos de precios de la nueva oferta. La fecha de facturación de la suscripción pasa a ser la fecha del cambio de oferta. Los datos de uso y facturación anteriores al cambio de oferta no se conservarán, por lo que se recomienda descargarlos antes de realizar el cambio.

> [AZURE.NOTE] Debido a restricciones relacionadas con la facturación, no se pueden realizar cambios de oferta dentro del primer ciclo de facturación después de crear una suscripción.

### ¿Puedo usar este mecanismo para migrar desde una oferta directa de la web a [Proveedor de soluciones en la nube](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) o a una oferta de [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

Este mecanismo se puede usar para cambiar solo desde ofertas directas de la web, y viceversa. Para mover la suscripción existente a EA, indique a su administrador de inscripciones que agregue su cuenta a este contrato. A continuación, recibirá un correo electrónico de invitación. Al seguir las instrucciones de aceptación de la invitación, las suscripciones de un Contrato Enterprise se moverán automáticamente. En este momento no hay existe la posibilidad de mover una suscripción directa de la web existente a CSP hoy mismo.

## Pasos siguientes

- Aprenda a [administrar roles de administrador](billing-add-change-azure-subscription-administrator.md) para su suscripción.

- Realice un seguimiento del uso mediante la [descarga de los datos y la factura de uso](billing-download-azure-invoice-daily-usage-date.md).

> [AZURE.NOTE] Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

<!---HONumber=AcomDC_0914_2016-->