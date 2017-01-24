---
title: "Preguntas más frecuentes sobre suscripciones y facturación de Azure | Microsoft Docs"
description: "Se proporcionan respuestas a las preguntas comunes sobre suscripciones y facturación de Azure"
services: 
documentationcenter: 
author: genlin
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 614f90ee-b625-46cf-a91e-3bcd83190e62
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 7583a1c81802bdedabdb70c125aac536b879204d
ms.openlocfilehash: 38531c1b3cb7c7de9b832b4fbedd8991f40e3424


---
# <a name="azure-billing-and-subscription-faq"></a>Preguntas más frecuentes sobre suscripciones y facturación de Azure
En este artículo se ofrecen respuestas a algunas de las preguntas más frecuentes sobre suscripciones y facturación de Azure.

> [!NOTE]
> Si necesita más ayuda sobre cualquier aspecto de este artículo, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver rápidamente el problema.
>
>

## <a name="account-administration"></a>Administración de cuentas
* [¿Cómo administro las cuentas de administrador en el nuevo Portal de Azure?](#how-do-i-manage-administrator-accounts-in-the-new-azure-portal)
* [¿Se pueden enviar notificaciones a una dirección de correo electrónico diferente de la dirección de correo electrónico del propietario de la cuenta asociada a mi cuenta?](#can-notifications-be-sent-to-a-different-email-address-other-than-the-account-owner-email-address-associated-with-my-account)
* [¿Por qué no puedo editar mi suscripción o agregar más datos?](#why-cant-i-edit-or-add-details-to-my-subscription)
* [No puedo iniciar sesión en el Portal de Azure](#i-am-unable-to-log-in-to-the-azure-portal)

## <a name="billing"></a>Facturación
* [¿Cómo puedo obtener una copia de la factura?](#how-do-i-get-a-copy-of-my-invoice)
* [No veo una factura del último período de facturación. ¿Por qué?](#i-do-not-see-an-invoice-for-the-last-billing-period-why)
* [¿Cómo se puede realizar un pago?](#how-do-i-make-a-payment)

## <a name="free-trial"></a>Versión de prueba gratuita
* [¿Puedo probar Azure gratis, sin riesgo de que se realicen cargos?](#can-i-try-azure-for-free-without-any-risk-of-being-charged)
* [¿Qué es una suscripción de evaluación gratuita y cuánto dura?](#what-is-a-free-trial-subscription-and-how-long-does-it-last)
* [¿Qué recursos están disponibles cuando se usa una evaluación gratuita?](#what-resources-are-available-when-using-a-free-trial)
* [¿Qué sucede si quito el límite de gasto de mi evaluación gratuita?](#what-happens-if-i-remove-the-spending-limit-on-my-free-trial)
* [¿Puedo obtener un aumento de cuota en una suscripción de evaluación gratuita?](#can-i-get-a-quota-increase-on-a-free-trial-subscription)
* [¿Dónde puedo obtener más detalles sobre la versión de evaluación gratuita?](#where-can-i-get-more-details-on-free-trial)

## <a name="how-to-buy-azure"></a>Instrucciones para contratar Azure
* [¿En qué países y regiones está comercialmente disponible Microsoft Azure y qué divisas pueden usarse para comprar Azure?](#in-which-countries-and-regions-is-microsoft-azure-commercially-available-and-what-currencies-can-be-used-to-purchase-azure)
* [¿Existen restricciones en cuanto a la reventa de servicios basados en Azure a países con embargo?](#do-we-restrict-resale-of-azure-based-service-into-countries-under-embargo)
* [¿Están disponibles Azure y Base de datos SQL a través del contrato de licencia de proveedor de servicios de Microsoft?](#are-azure-and-sql-database-available-through-microsoft-services-provider-license-agreement)
* [¿Puedo usar mis licencias de Windows existentes que compré fuera de Azure?](#can-i-use-my-existing-windows-licenses-that-i-bought-outside-of-azure)
* [¿Quién puede adquirir servicios de Azure?](#who-can-purchase-azure-services)

## <a name="making-payments"></a>Realizar pagos
* [¿Qué opciones de pago tengo para adquirir Azure?](#what-payment-options-do-i-have-in-purchasing-azure)
* [¿Cómo puedo solicitar el método de pago de facturación?](#how-can-i-request-the-invoice-method-of-payment)
* [¿Cómo puedo comprobar el estado de un pago realizado con tarjeta de crédito?](#how-do-i-check-the-status-of-a-payment-made-by-credit-card)
* [¿Cómo puedo quitar una tarjeta de crédito que ya no utilizo como método de pago de Azure?](#how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method)
* [¿Cómo puedo actualizar o cambiar la información de la tarjeta de crédito?](#how-can-i-update-or-change-my-credit-card-information)
* [¿Cómo puedo editar la información de pago de las suscripciones de Azure?](#how-can-i-edit-my-payment-information-for-my-azure-subscriptions)
* [¿Por qué veo un mensaje donde se indica que tengo que utilizar una tarjeta de crédito internacional?](#why-am-i-seeing-a-message-that-i-need-to-use-an-international-credit-card)

## <a name="azure-pricing"></a>Precios de Azure
* [¿Dónde puedo encontrar información sobre beneficios y precios de Servicios de Azure?](#where-can-i-find-the-benefits-and-pricing-information-for-azure-services)
* [¿Cómo puedo cambiar el plan de precios?](#how-do-i-change-my-pricing-plan)

## <a name="service-level-agreements-sla"></a>Contratos de nivel de servicio (SLA)
* [¿Cómo se pueden conocer por adelantado los períodos de inactividad del servicio por mantenimiento planeado?](#how-do-we-know-in-advance-about-service-downtime-for-planned-maintenance)
* [¿Qué es el contrato de nivel de servicio de Azure de tiempo de actividad y conectividad?](#what-is-the-azure-sla-agreement-for-uptime-and-connectivity)
* [¿Qué son los créditos del contrato de nivel de servicio de Azure?](#what-are-the-azure-sla-credits)
* [¿Cómo funcionarán los contratos de nivel de servicio de Azure con los contratos de licencia locales de Microsoft vigentes en la actualidad?](#how-will-azure-service-level-agreements-work-with-current-on-premises-microsoft-licensing-agreements)

## <a name="spending-limits"></a>Límites de gasto
* [¿Si desactivo el límite de gasto, puedo activarlo después?](#can-i-turn-the-spending-limit-back-on-if-i-turn-off-it)
* [¿Se puede ajustar el importe del límite de gasto?](#can-i-adjust-the-amount-of-the-spending-limit)

## <a name="support-plans"></a>Planes de soporte técnico
* [¿Cómo compro soporte técnico de Azure?](#how-do-i-purchase-azure-support)
* [¿La cuota mensual cubre un único plan de Azure o toda la cuenta?](#does-the-monthly-rate-cover-a-single-azure-plan-or-the-entire-account)
* [¿Cómo cambio a un plan de soporte técnico de Azure de nivel superior?](#how-do-i-upgrade-to-a-higher-tier-azure-support-plan)
* [¿Cuándo se me cobrará el soporte técnico de Azure?](#when-will-i-be-billed-for-azure-support)
* [¿Qué ocurre al final del período de la suscripción?](#what-happens-at-the-end-of-the-term)
* [¿Cómo cancelo mi plan de soporte técnico de Azure?](#how-do-i-cancel-my-azure-support-plan)

## <a name="migrating-subscriptions-services-and-data"></a>Migración de suscripciones, servicios y datos
* [¿Cómo se migran los datos y servicios de una suscripción de Azure a otra?](#how-do-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription)
* [¿Cómo puedo transferir la propiedad de mis suscripciones?](#how-do-i-transfer-ownership-of-my-subscriptions)

## <a name="subscription-service-limits-quotas"></a>Límites de servicio de suscripción (cuotas)
* [¿Puedo obtener un aumento de cuota en una suscripción de evaluación gratuita?](#can-i-get-a-quota-increase-on-a-free-trial-subscription)

### <a name="what-payment-options-do-i-have-in-purchasing-azure"></a>¿Qué opciones de pago tengo para adquirir Azure?
Puede comprar Azure con una tarjeta de crédito o débito o elegir la opción de facturación.

> [!NOTE]
> * Si opta por la opción de facturación, no podrá cambiar a la opción de tarjeta de crédito. Para registrarse para la facturación, consulte [Facturación de Azure](https://azure.microsoft.com/pricing/invoicing/).
> * Tenga en cuenta que no aceptamos tarjetas de crédito virtuales ni de pago por adelantado.
> * Únicamente asumirá los intereses de la tarjeta de crédito o de otros gastos de la tarjeta que puedan generarse.
>
>

### <a name="how-can-i-request-the-invoice-method-of-payment"></a>¿Cómo puedo solicitar el método de pago de facturación?
Siga los pasos que se indican en [Facturación de Azure](https://azure.microsoft.com//pricing/invoicing/) para enviar una solicitud de pago mediante factura. Una vez aprobada su solicitud, se le proporcionarán instrucciones para configurar su suscripción para el método de pago de facturas.

### <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>¿Cómo puedo comprobar el estado de un pago realizado con tarjeta de crédito?
Debe enviar una incidencia de soporte técnico para solicitar asistencia. Para crear un vale de soporte para comprobar el estado de un pago realizado mediante tarjeta de crédito, consulte [Creación de una incidencia de soporte técnico para problemas de suscripción y facturación de Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-do-i-get-a-copy-of-my-invoice"></a>¿Cómo puedo obtener una copia de la factura?
Como Administrador de la cuenta de Azure, puede ver la factura actual en el centro de cuentas de Azure y descargar también extractos de los seis períodos de facturación anteriores. Para más información, consulte [Cómo descargar las datos de uso diario y de factura de facturación de Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method"></a>¿Cómo puedo quitar una tarjeta de crédito que ya no utilizo como método de pago de Azure?
Debe enviar una incidencia de soporte técnico para solicitar asistencia. Para crear un vale de soporte para quitar una tarjeta de crédito, consulte el artículo [Creación de una incidencia de soporte técnico para problemas de suscripción y facturación de Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-can-i-update-or-change-my-credit-card-information"></a>¿Cómo puedo actualizar o cambiar la información de la tarjeta de crédito?
Si se renueva la tarjeta de crédito y el número sigue siendo el mismo, solo tiene que actualizar la información de la tarjeta de crédito existente, como la fecha de caducidad. Si el número de su tarjeta de crédito cambia o si desea usar otra tarjeta distinta, tendrá que agregar la nueva tarjeta. Puede consultar instrucciones para actualizar o agregar una tarjeta de crédito en [Cambio de la tarjeta de crédito usada para pagar una suscripción de Azure](billing-how-to-change-credit-card.md).

### <a name="how-do-we-know-in-advance-about-service-downtime-for-planned-maintenance"></a>¿Cómo se pueden conocer por adelantado los períodos de inactividad del servicio por mantenimiento planeado?
Nuestro contrato de nivel de servicio de disponibilidad se aplica a las aplicaciones cliente que utilizan un mínimo de dos instancias de rol.  No hay ningún contrato de nivel de servicio para instancias únicas. Consulte la página [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) para ver todos los detalles.

#### <a name="planned-maintenance-classic-portal"></a>Mantenimiento planeado (portal clásico)
En configuraciones de una o varias instancias, Azure envía comunicaciones por correo electrónico por adelantado para avisarle del próximo mantenimiento planeado (con una semana de antelación en el caso de una sola instancia y con 48 horas de antelación en el caso de varias instancias). Este correo electrónico se enviará a las cuentas de correo electrónico del administrador de cuenta, del administrador de servicios y del coadministrador facilitadas en la suscripción.

> [!NOTE]
> Para más información específica de las máquinas virtuales, consulte el artículo [Mantenimiento planeado de máquinas virtuales Linux en Azure](virtual-machines/virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
>
>

#### <a name="unplanned-maintenance"></a>Mantenimiento no planeado
En caso de que se produzca una interrupción del servicio no planeada en la plataforma Azure, se realizarán actualizaciones de estado en nuestro [panel de servicios](http://status.azure.com/) y los clientes recibirán también un aviso en el [Portal de Azure clásico](https://manage.windowsazure.com/).

### <a name="what-is-the-azure-sla-agreement-for-uptime-and-connectivity"></a>¿Qué es el contrato de nivel de servicio de Azure de tiempo de actividad y conectividad?
Azure tiene contratos de nivel de servicio independientes para todos los servicios que se encuentran disponibles con carácter general y no en vista previa. El contrato de nivel de servicio explica los compromisos de Microsoft en cuanto a tiempo de actividad y conectividad. Para consultar la información más reciente al respecto, diríjase a la página [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) .

### <a name="what-are-the-azure-sla-credits"></a>¿Qué son los créditos del contrato de nivel de servicio de Azure?
Los créditos del contrato de nivel de servicio de Azure se calculan por cada suscripción como un porcentaje de la factura de dicho servicio en el mes de facturación en que se incumplió el contrato de nivel de servicio. Los créditos del servicio se aplican en la factura del mes posterior. Por lo general, proporcionamos un 10 % de crédito si caemos por debajo del primer umbral (99,95 % o 99,9 %, dependiendo del servicio) y el 25 % si caemos por debajo del umbral siguiente (99 %). Consulte la página [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) para ver todos los detalles.

### <a name="how-will-azure-service-level-agreements-work-with-current-on-premises-microsoft-licensing-agreements"></a>¿Cómo funcionarán los contratos de nivel de servicio de Azure con los contratos de licencia locales de Microsoft vigentes en la actualidad?
Los contratos de nivel de servicio de Azure son independientes de nuestros contratos de licencia locales de Microsoft. Nuestros contratos de nivel de servicio para Azure le ofrecen un tiempo de actividad mensual garantizado para los servicios que se consuman en la nube, con créditos de contrato de nivel de servicio que se aplicarán sobre lo facturado en caso de que no se cumpla esa garantía.

### <a name="i-do-not-see-an-invoice-for-the-last-billing-period-why"></a>No veo una factura por el último período de facturación. ¿Por qué?
Podría haber varias razones para esto:

* Han transcurrido menos de 30 días desde el día que se suscribió a Azure.
* El tipo de suscripción es de evaluación gratuita o una de las ofertas de miembro (crédito mensual para suscriptores de Visual Studio, Open, BizSpark e Imagine) que no tiene asociada una tarjeta de crédito ni otro instrumento de pago.
* La fecha de generación de facturas aún no ha llegado.

### <a name="i-am-unable-to-log-in-to-the-azure-portal"></a>No puedo iniciar sesión en el Portal de Azure
Consulte [No puedo iniciar sesión para administrar mi suscripción de Azure](billing-cannot-login-subscription.md).

> [!NOTE]
> Asegúrese de haber iniciado sesión con el administrador correcto: un administrador de cuenta solo puede acceder al [Centro de cuentas](https://account.windowsazure.com/), mientras que los administradores de servicios (SA) y los coadministradores (CA) solo pueden acceder a [Azure Portal](https://portal.azure.com/) o al [Portal de Azure clásico](https://manage.windowsazure.com/).
>
>

### <a name="how-do-i-purchase-azure-support"></a>¿Cómo compro soporte técnico de Azure?
Los planes de soporte técnico de Azure se pueden comprar en línea o a través de un contrato Enterprise. Los planes de soporte técnico Developer, Estándar o Professional Direct están disponibles en línea a través de la página [Soporte técnico de Azure para clientes](https://azure.microsoft.com/support/plans/) . Para comprar un plan de soporte técnico, debe ser el propietario de la cuenta de Microsoft Azure.

Si compra Microsoft Azure a través de un contrato Enterprise (EA), puede agregar un plan de soporte técnico Standard o Professional Direct al contrato Enterprise poniéndose en contacto con su revendedor de grandes cuentas (LAR). El soporte técnico Developer no está disponible a través de un contrato Enterprise.

### <a name="does-the-monthly-rate-cover-a-single-azure-plan-or-the-entire-account"></a>¿La cuota mensual cubre un único plan de Azure o toda la cuenta?
El costo fijo mensual del soporte técnico de Azure cubre una cuenta, independientemente del número de suscripciones o de usuarios que tenga en ella.

Todas las suscripciones de una cuenta compartirán el mismo plan de soporte técnico y todos los usuarios con acceso de administrador o de propietario a cualquiera de las suscripciones de la cuenta con un plan de soporte técnico también tendrán derecho a recibir soporte técnico para las suscripciones de la cuenta concreta a las que tienen acceso.

Para acceder a una cuenta de Microsoft Azure, puede usar el [Centro de cuentas de Azure](https://account.windowsazure.com/Home/Index). Desde ahí puede ver la lista de las suscripciones que pertenecen a la cuenta y que tendrán derecho a recibir soporte técnico.

### <a name="how-do-i-upgrade-to-a-higher-tier-azure-support-plan"></a>¿Cómo cambio a un plan de soporte técnico de Azure de nivel superior?
Para actualizar a un plan de nivel superior, [envíe una solicitud de soporte técnico](billing-how-to-create-billing-support-ticket.md) y elija **Administración de la suscripción**. Microsoft renunciará a los pagos restantes de su plan de soporte técnico original de Azure y se le cobrarán los meses restantes de su compromiso inicial con arreglo al nivel superior.

### <a name="when-will-i-be-billed-for-azure-support"></a>¿Cuándo se me cobrará el soporte técnico de Azure?
Cuando compre un plan de soporte técnico de Azure en línea, se le cobrará inmediatamente la cuota del primer mes. Se le cobrará el importe mensual el primer día de cada ciclo de facturación. Las compras de contratos Enterprise (EA) seguirán el ciclo de facturación de los contratos.

### <a name="what-happens-at-the-end-of-the-term"></a>¿Qué ocurre al final del período de la suscripción?
Al final del período de seis meses, los planes se renovarán automáticamente durante otros seis meses en el mismo plan de soporte técnico de Azure y con el mismo método de pago. Como propietario de la cuenta, puede optar por desactivar la función de renovación automática desde el [Centro de cuentas de Azure](https://account.windowsazure.com/Home/Index).

Si no desea que la suscripción se renueve automáticamente, inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Home/Index), seleccione el plan de soporte técnico en cuestión, haga clic en "Cambiar detalles de renovación" y desactive la opción predeterminada de renovación automática.

### <a name="how-do-i-cancel-my-azure-support-plan"></a>¿Cómo cancelo mi plan de soporte técnico de Azure?
El propietario de una cuenta puede cancelar un plan de soporte técnico de Azure; para ello, debe iniciar sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Home/Index) y seleccionar **Suscripciones**. Si tiene una suscripción, se mostrará una lista de suscripciones. Seleccione la suscripción que desea cancelar. A continuación, se mostrarán los detalles de la suscripción con una lista de opciones a la derecha de la pantalla. Seleccione **Cancelar** y continúe. Si no tiene una suscripción, aparecerá el mensaje “No tiene ninguna suscripción”.

> [!NOTE]
> Un plan de soporte técnico de Azure tiene una duración de seis meses y Microsoft requiere un compromiso monetario continuado durante todo este período. La cancelación antes del vencimiento del período no eximirá del compromiso restante ni supondrá el reembolso de ningún importe prepagado.
> Para más información, consulte [Cancelación de su suscripción de Azure](billing-how-to-cancel-azure-subscription.md).
>
>

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription"></a>¿Cómo se migran los datos y servicios de una suscripción de Azure a otra?
Para más información, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/) . Para crear una incidencia de soporte técnico con el fin de migrar datos y servicios de una suscripción de Azure a otra nueva, consulte el artículo [Creación de una incidencia de soporte técnico para problemas de suscripción y facturación de Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="how-do-i-manage-administrator-accounts-in-the-new-azure-portal"></a>¿Cómo administro las cuentas de administrador en el nuevo Portal de Azure?
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Gracias a RBAC puede dividir las tareas entre el equipo de DevOps, y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. Para más información, vea [Control de acceso basado en roles de Azure](active-directory/role-based-access-control-configure.md).

### <a name="how-do-i-transfer-ownership-of-my-subscriptions"></a>¿Cómo puedo transferir la propiedad de mis suscripciones?
Ahora puede hacerlo fácilmente en el Centro de cuentas de Microsoft Azure (para clientes de ofertas de modelo de pago por uso, crédito mensual para suscriptores de Visual Studio, Microsoft Partner Network o suscripciones de BizSpark). Hemos agregado la capacidad de transferir suscripciones a otros usuarios. En otras palabras, ahora puede cambiar el administrador de cuenta de cualquier oferta de pago por uso, crédito mensual para suscriptores de Visual Studio, Microsoft Partner Network o suscripción de BizSpark que tenga. Tenga en cuenta que la cuenta de usuario del destinatario debe estar en el mismo país y que no puede transferir suscripciones asociadas a una compra en Marketplace.
Para más información, consulte el artículo [Transferencia de suscripciones de Azure](billing-subscription-transfer.md).

### <a name="can-i-use-my-existing-windows-licenses-that-i-bought-outside-of-azure"></a>¿Puedo usar mis licencias de Windows existentes que compré fuera de Azure?
Sí, mediante la movilidad de licencias. Si tiene contrato de servicio puede "traerse su propia licencia" para todos los productos de servidor que admitan máquinas virtuales, excepto Windows Server. Para más información, consulte [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

### <a name="where-can-i-find-the-benefits-and-pricing-information-for-azure-services"></a>¿Dónde puedo encontrar información sobre beneficios y precios de Servicios de Azure?
Para obtener información sobre las ventajas de los servicios de Azure, consulte [Detalles de las ofertas de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Para obtener información de precios, consulte la [página de precios de Azure](https://azure.microsoft.com/pricing/).

### <a name="how-do-i-change-my-pricing-plan"></a>¿Cómo puedo cambiar el plan de precios?
Puede cambiar su suscripción de pago por uso por la [oferta de pago por adelantado de doce meses](https://azure.microsoft.com/offers/ms-azr-0026p/). Con esta oferta, pague por adelantado los servicios de Azure durante un período de 12 meses y reciba un 5 % de descuento. En el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions), vaya a su suscripción y haga clic en Cambiar oferta. Esta es actualmente la única oferta que se puede cambiar mediante el Centro de cuentas. También puede ponerse en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/) para cambiar a otra oferta.

### <a name="can-notifications-be-sent-to-a-different-email-address-other-than-the-account-owner-email-address-associated-with-my-account"></a>¿Se pueden enviar notificaciones a una dirección de correo electrónico diferente de la dirección de correo electrónico del propietario de la cuenta asociada a mi cuenta?
Sí. Si desea especificar una dirección de correo electrónico diferente para recibir notificaciones, siga estas instrucciones:

1. Vaya a la pestaña [Perfil](https://account.windowsazure.com/Profile) del Portal de cuentas de Azure.
2. Haga clic en **Editar detalles** para actualizar su dirección de correo electrónico a fin de recibir notificaciones.

### <a name="how-can-i-edit-my-payment-information-for-my-azure-subscriptions"></a>¿Cómo puedo editar la información de pago de las suscripciones de Azure?
Para ver y editar la información de la cuenta de Azure, debe iniciar sesión en el Centro de cuentas de Azure como administrador de cuenta. A continuación se ofrecen instrucciones para administrar el método de pago de las suscripciones de Azure.**

1. Vaya al [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions).
2. En la página de suscripciones, haga clic en la suscripción cuyo método de pago desea actualizar.
3. En la página de resumen de la **suscripción** , haga clic en Cambiar método de pago. Aparece la herramienta **Cambiar método de pago** en una ventana independiente.

   **Nota**: también se puede acceder al Centro de cuentas desde el Portal de Microsoft Azure clásico. Para ello, haga clic en el nombre de su cuenta y elija Ver mi factura.
4. En la página **Elegir método de pago**, haga clic en la lista desplegable, seleccione el método de pago que desee actualizar y haga clic en **Editar**.
5. En la página de detalles, compruebe que ha seleccionado el tipo y el número de tarjeta de crédito correctos.
6. Realice los cambios necesarios en los datos de la tarjeta y haga clic en **Siguiente**.

Para más información, consulte el artículo [Cambio de la tarjeta de crédito usada para pagar una suscripción de Azure](billing-how-to-change-credit-card.md).

> [!NOTE]
> Actualmente, la herramienta Cambiar el método de pago no permite quitar una tarjeta de crédito existente como método de pago. Para obtener información acerca de cómo quitar una tarjeta de crédito, consulte [¿Cómo puedo quitar una tarjeta de crédito que ya no utilizo como método de pago de Azure?](#how-do-i-remove-a-credit-card-that-i-no-longer-use-as-an-azure-payment-method) en este mismo artículo.
>
>

### <a name="why-am-i-seeing-a-message-that-i-need-to-use-an-international-credit-card"></a>¿Por qué veo un mensaje donde se indica que tengo que utilizar una tarjeta de crédito internacional?
En algunos casos, aunque esté pagando por servicios de Azure en su divisa local o preferida, adquiere servicios a través de una entidad de Microsoft no local y su tarjeta de crédito la procesa un banco no local. En estos casos, se puede producir un error con las tarjetas emitidas por bancos locales que puede afectar negativamente a los servicios de Azure. Si no está seguro de si la tarjeta de crédito tiene habilitada las transacciones internacionales, póngase en contacto con su banco o emisor de tarjeta para confirmarlo. Consulte la lista de [países y monedas admitidos](billing-countries-and-currencies.md).

### <a name="why-cant-i-edit-or-add-details-to-my-subscription"></a>¿Por qué no puedo editar mi suscripción o agregar más datos?
Para ver y editar la información de la cuenta de Azure, debe iniciar sesión en el Centro de cuentas de Azure como administrador de cuenta.  Si es administrador de Azure y no puede editar la suscripción, envíe una incidencia de soporte técnico para solicitar asistencia. Para crear un vale de soporte, consulte el artículo [Creación de una incidencia de soporte técnico para problemas de suscripción y facturación de Azure](billing-how-to-create-billing-support-ticket.md).

### <a name="who-can-purchase-azure-services"></a>¿Quién puede adquirir servicios de Azure?
Azure está concebido para su uso por empresas que deseen compilar servicios y aplicaciones de escala en la nube. Sin embargo, cualquier usuario puede adquirir servicios de Azure.

### <a name="can-i-try-azure-for-free-without-any-risk-of-being-charged"></a>¿Puedo probar Azure gratis, sin riesgo de que se realicen cargos?
Sí. Con la [característica de límite de gasto](https://azure.microsoft.com/pricing/spending-limits/), los clientes que se suscriban a una oferta de evaluación gratuita, crédito mensual para suscriptores de Visual Studio, Microsoft Partner Network o BizSpark pueden usar Azure sin que se realicen cargos, mientras dicha característica esté activada. Todos los clientes nuevos que se registran para estas ofertas tendrán el límite de gasto activado con un valor de 0 USD.

### <a name="can-i-turn-the-spending-limit-back-on-if-i-turn-off-it"></a>¿Si desactivo el límite de gasto, puedo activarlo después?
Para nuestras ofertas de ventajas para miembros (por ejemplo, el crédito mensual para suscriptores de Visual Studio), puede volver a habilitar la característica de límite de gasto al comienzo del siguiente ciclo de facturación. La característica de límite de gasto no se puede volver a habilitar para el período de facturación actual una vez que se deshabilita.
Para más información, consulte [Cambiar el límite de gasto de Azure](https://msdn.microsoft.com/library/azure/dn465781.aspx).

### <a name="can-i-adjust-the-amount-of-the-spending-limit"></a>¿Se puede ajustar el importe del límite de gasto?
En la versión inicial de esta característica, el límite de gasto se encuentra establecido en $0 y no se puede modificar. Se ha diseñado para permitir que los clientes de nuestras ofertas de evaluación gratuita, crédito mensual para suscriptores de Visual Studio, Microsoft Partner Network o BizSpark utilicen Azure con total garantía de que no se les facturará ningún importe.
Para más información, consulte [Límite de gasto de Azure](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="in-which-countries-and-regions-is-microsoft-azure-commercially-available-and-what-currencies-can-be-used-to-purchase-azure"></a>¿En qué países y regiones está comercialmente disponible Microsoft Azure y qué monedas pueden usarse para comprar Azure?
Azure se puede comprar en más de 140 países de todo el mundo, y admitimos la facturación en muchas divisas. Haga clic [aquí](billing-countries-and-currencies.md) para ver una lista de países y divisas.

### <a name="do-we-restrict-resale-of-azure-based-service-into-countries-under-embargo"></a>¿Existen restricciones en cuanto a la reventa de servicios basados en Azure a países con embargo?
Sí.

### <a name="are-azure-and-sql-database-available-through-microsoft-services-provider-license-agreement"></a>¿Están disponibles Azure y Base de datos SQL a través del contrato de licencia de proveedor de servicios de Microsoft?
Actualmente, no está previsto ofrecer Azure o Base de datos SQL a través de SPLA.

### <a name="what-is-a-free-trial-subscription-and-how-long-does-it-last"></a>¿Qué es una suscripción de evaluación gratuita y cuánto dura?
Una suscripción de evaluación gratuita de Azure es una evaluación gratuita durante un mes para cualquier cliente nuevo y proporciona 200 USD de créditos de Azure sin costo alguno. Para ver qué servicios cubre y no cubre el crédito de evaluación gratuita, consulte la [página de la oferta de evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/).

### <a name="what-resources-are-available-when-using-a-free-trial"></a>¿Qué recursos están disponibles cuando se usa una cuenta de evaluación gratuita?
Recibirá 200 USD de créditos de Azure con la evaluación gratuita para usar durante 30 días. Usted decide cómo desea usar esta cantidad. Para más información, consulte la [página de la oferta de evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) .

Puede registrarse para obtener una suscripción de evaluación [aquí](http://azure.microsoft.com/pricing/free-trial/).

Consulte también [Preguntas más frecuentes sobre la evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

### <a name="what-happens-if-i-remove-the-spending-limit-on-my-free-trial"></a>¿Qué sucede si quito el límite de gasto de mi evaluación gratuita?
Al quitar el límite de gasto, se le empieza a cobrar por el consumo de recursos una vez superado el crédito de la evaluación gratuita. No podrá restablecer el límite de gasto una vez que se haya quitado.

### <a name="can-i-get-a-quota-increase-on-a-free-trial-subscription"></a>¿Puedo obtener un aumento de cuota en una suscripción de evaluación gratuita?
Las suscripciones de evaluación gratuita no son aptas para aumentar la cuota. Si tiene una versión de evaluación gratuita, puede actualizar a una suscripción de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/) . Para más información sobre cómo realizar la actualización, consulte [Upgrade your free Azure subscription to Pay-As-You-Go](billing-upgrade-azure-subscription.md) (Actualización de la evaluación gratuita de Azure a pago por uso).

> [!NOTE]
> Las suscripciones de Microsoft Imagine también pueden optar a aumentos de cuota.
>
>

### <a name="where-can-i-get-more-details-on-free-trial"></a>¿Dónde puedo obtener más detalles sobre la versión de evaluación gratuita?
Visite la [página de la oferta de evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) y lea las [preguntas más frecuentes sobre la cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

> [!NOTE]
> Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
>
>

### <a name="how-do-i-make-a-payment"></a>¿Cómo se puede realizar un pago?
Si ha configurado una tarjeta de crédito o una tarjeta de débito como forma de pago, el pago se realizará automáticamente.

Si se encuentra en un [método de factura de pago](https://azure.microsoft.com/pricing/invoicing/), envíe el pago a la ubicación indicada en la parte inferior de la factura. Para obtener ayuda, [póngase en contacto con el equipo de soporte técnico](billing-how-to-create-billing-support-ticket.md).



<!--HONumber=Dec16_HO2-->


