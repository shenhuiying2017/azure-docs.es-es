---
title: "Comprender el límite de gasto de Azure | Microsoft Docs"
description: "Describe cómo funciona el límite de gasto de Azure y cómo quitarlo"
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 39ae134d8927f5123667b64bbd0c659cd5f62ffc
ms.lasthandoff: 04/21/2017


---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Comprender el límite de gasto de Azure y cómo quitarlo

El límite de gasto de Azure es un límite de cuánto puede gastar su suscripción de Azure. Todo los clientes nuevos que se registren para las ofertas de prueba o las que incluyan créditos para varios meses, tienen activado el límite de gasto de forma predeterminada. El límite de gasto es $0. No se puede cambiar. El límite de gasto no está disponible para tipos de suscripción como suscripciones de pago por uso y planes con compromiso. Vea la [lista completa de ofertas de Azure y la disponibilidad del límite de gasto](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>¿Qué ocurre cuando alcanzo el límite de gasto?

Cuando el uso dé lugar a cobros que superen las cantidades mensuales de la oferta, los servicios que desarrolló se deshabilitan durante el resto de ese mes. Por ejemplo, los servicios Cloud Services que desarrolló se quitan de la producción y las máquinas virtuales de Azure se detienen y se desasignan. Para evitar que los servicios se deshabiliten, puede optar por quitar su límite de gasto. Cuando los servicios estén deshabilitados, los datos de las cuentas de almacenamiento y las bases de datos están disponibles en modo de solo lectura para administradores. Al principio del siguiente mes de facturación, si su oferta incluye créditos durante varios meses, se volverá a habilitar su suscripción. Después, puede volver a implementar los servicios Cloud Services y tener acceso total a las cuentas de almacenamiento y bases de datos.

Después de que la suscripción de prueba gratuita alcance el límite de gasto, se puede volver a habilitar la suscripción y hacer que se [actualice a nuestra oferta de pago por uso estándar](billing-upgrade-azure-subscription.md) automáticamente a los 90 días.

Recibirá notificaciones cuando alcance el límite de gasto de su oferta. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com), haga clic en **CUENTA** y luego en **Suscripciones**. Verá notificaciones sobre las suscripciones que han alcanzado el límite de gasto.

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>Cosas por las que se le cobra incluso si tiene un límite de gasto habilitado

Algunos servicios de Azure y las [compras de Marketplace](https://azure.microsoft.com/marketplace/) pueden implicar cobros en el método de pago (CC) incluso si hay un límite de gasto establecido. Como ejemplos se pueden citar las licencias de Visual Studio, Azure Active Directory Premium, los planes de soporte técnico y la mayoría de los servicios de marca de terceros vendidos a través de Marketplace.


## <a name="when-not-to-use-the-spending-limit"></a>Cuándo no usar el límite de gasto

El límite de gasto podría impedirle implementar o usar ciertos servicios de Marketplace y Microsoft. A continuación se indican los escenarios donde debería quitar el límite de gasto en su suscripción.

- Planea implementar primero imágenes de usuario como Oracle y servicios como Visual Studio Team Services. Este escenario hace que se supere el límite de gasto prácticamente de forma inmediata y que la suscripción se deshabilite.

- Tiene servicios que no se pueden interrumpir.

- Tiene servicios y recursos con configuraciones como direcciones IP virtuales que no quiere perder. Estas configuraciones se perderán cuando se desasignen los servicios y recursos.


## <a name="remove-the-spending-limit"></a>Quitar el límite de gasto

Puede quitar el límite de gasto en cualquier momento siempre que haya un método de pago válido asociado a la suscripción. Para las ofertas que tienen crédito para varios meses, también puede volver a habilitar el límite de gasto al principio del siguiente ciclo de facturación.

Para quitar el límite de gasto, siga estos pasos:

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com).

2. Seleccione una suscripción.

3. Si la suscripción está deshabilitada porque se ha alcanzado el límite de gasto, haga clic en esta notificación: "La suscripción alcanzó un límite de gasto y se deshabilitó para evitar cargos.". De lo contrario, haga clic en **Quitar límite de gasto** en el área **ESTADO DE LA SUSCRIPCIÓN**.

4. Seleccione una opción que sea apropiada para usted.

|Opción|Efecto|
|-------|-----|
|Quitar el límite de gasto indefinidamente|Quita el límite de gasto sin activarlo automáticamente al principio del siguiente período de facturación.|
|Quitar el límite de gasto para el período de facturación actual|Quita el límite de gasto de manera que se active automáticamente al principio del siguiente período de facturación.|

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
