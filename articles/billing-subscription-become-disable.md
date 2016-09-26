<properties
	pageTitle="¿Qué hago si se deshabilita mi suscripción de Azure? | Microsoft Azure"
	description="Describe las razones por las que la suscripción de Azure se ha deshabilitado y la resolución."
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="genli"/>

# ¿Qué hago si se deshabilita mi suscripción de Azure?

> [AZURE.NOTE] Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rápidamente el problema.

Si la suscripción de Azure se deshabilita, puede seguir los pasos de este artículo para volver a habilitarla. La suscripción de Azure puede deshabilitarse por las razones siguientes.

## Se ha alcanzado el límite de gasto

Para evitar que se incurra accidentalmente en gastos de uso que superen el importe incluido en la oferta, hemos introducido la característica de [límite de gasto](https://azure.microsoft.com/pricing/spending-limits/). Cuando el uso supere el importe mensual incluido en la oferta, deshabilitaremos el servicio durante el resto del mes de facturación. Puede optar por quitar el límite de gasto para evitar este problema y [configurar alertas de facturación para las suscripciones de Microsoft Azure](billing-set-up-alerts.md) para ayudarle a supervisar y administrar la actividad de facturación de las cuentas de Azure.

A continuación se indica cómo puede quitar el límite de gasto:

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Home/Index).

2. Haga clic en **Suscripciones**.

3. Seleccione una suscripción.

4. Si ve el mensaje *La suscripción alcanzó un límite de gastos y se deshabilitó para evitar cargos*, haga clic en él.

	Si lo prefiere, haga clic en **Quitar límite del gasto** en el área ** Estado de la suscripción**.

5. Seleccione una de las siguientes opciones que sea adecuada para usted:

|Opción|Efecto|
|------|------|
|Quitar el límite de gasto indefinidamente|Quita el límite de gasto sin activarlo automáticamente al principio del siguiente período de facturación.|
|Quitar el límite de gasto para el período de facturación actual|Quita el límite de gasto de manera que se active automáticamente al principio del siguiente período de facturación.|

>[AZURE.NOTE] Si está usando una suscripción de evaluación gratuita, quitar el límite de gasto convertirá la suscripción a pago por uso al final de la evaluación gratuita.

## Su factura está vencida

Pagar el saldo pendiente en su totalidad. Para ello, consulte el artículo [¿Por qué ha recibido una notificación de que su suscripción de Azure tiene un saldo pendiente?](billing-azure-subscription-past-due-balance.md#what-can-you-do-to-resolve-the-issue)

## La factura supera el límite de la tarjeta de crédito

Para solucionar este problema, use uno de los métodos siguientes:

- [Cambie a otra tarjeta de crédito](billing-how-to-change-credit-card.md).
- Si tiene una empresa, puede [cambiar al método de pago de factura](https://azure.microsoft.com/pricing/invoicing/).

## El administrador de cuentas cancela la suscripción

>[AZURE.NOTE] Este método actualmente solo se aplica a las suscripciones de pago por uso. Para otros tipos de suscripción, deberá [ponerse en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Si es el administrador de cuentas, puede volver a activar su suscripción de pago por uso siguiendo estos pasos:

- Seleccione la suscripción cancelada.
- Haga clic en cualquiera de los vínculos **Reactivar** de la página de detalles de la suscripción.

![reactivate-sub](./media/billing-how-to-cancel-azure-subscription/reactivate-sub.png)

## Cómo volver a habilitar suscripciones que no son de pago por uso:

Para reactivar una suscripción deshabilitada, debe crear una incidencia de soporte técnico: inicie sesión en [Azure Portal](https://portal.azure.com/), seleccione **Ayuda y soporte técnico**>**Nueva solicitud de soporte técnico**.

![helpandsupportbutton](./media/billing-how-to-create-billing-support-ticket/helpandsupport.png)

Si no puede acceder al portal, envíe la solicitud de soporte técnico desde el sitio web de soporte técnico de Azure o desde el Centro de cuentas de Azure:

 * Para enviar una incidencia de soporte técnico desde el [sitio web de soporte técnico de Azure](https://azure.microsoft.com/support/options/), haga clic en **Obtener soporte**.
 * Para enviar una incidencia de soporte técnico desde el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions), seleccione una suscripción y haga clic en **Consultar al soporte técnico de Microsoft**.

Para más información, consulte [Creación de una incidencia de soporte técnico para problemas de suscripción y facturación de Azure](billing-how-to-create-billing-support-ticket.md).

> [AZURE.NOTE] Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

<!---HONumber=AcomDC_0914_2016-->