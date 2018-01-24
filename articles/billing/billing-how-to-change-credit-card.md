---
title: "Cambio de la tarjeta de crédito para Azure | Microsoft Docs"
description: "Describe cómo cambiar la tarjeta de crédito que se usa para pagar una suscripción de Azure"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: 9cab81b6072c6f096f6f1a419cebcca9630ebde3
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Agregar, actualizar o quitar una tarjeta de crédito o débito para Azure

En el Centro de cuentas, puede agregar una nueva tarjeta de crédito, actualizar una existente o eliminar una que no se use. Debe ser [administrador de cuenta](billing-subscription-transfer.md#whoisaa) para realizar estos cambios.

**¿Desea cambiar al pago mediante factura?** Consulte [Pago de suscripciones de Azure con factura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Incorporación de una nueva tarjeta de crédito o débito

1. Inicie sesión en el [Centro de cuentas](https://account.windowsazure.com/Subscriptions) como administrador de la cuenta.
1. Seleccione una suscripción.
1. En el lado derecho de la página, seleccione **Manage payment methods**(Administrar métodos de pago).

    ![Captura de pantalla que muestra la opción Administrar métodos de pago seleccionada.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Seleccione "+" para agregar una tarjeta.

    ![Captura de pantalla que muestra la opción de edición junto al método de pago.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Escriba los detalles de la tarjeta de crédito o débito.
1. Seleccione **Guardar**. 

Si se produce un error después de agregar la tarjeta de crédito, consulte [Tarjeta de crédito rechazada al suscribirse a Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Actualización de la tarjeta de crédito o débito existente

Si se renueva la tarjeta de crédito y el número sigue siendo el mismo, actualice la información de la tarjeta de crédito existente, como la fecha de caducidad. Si su número de tarjeta de crédito cambia porque la tarjeta se pierda, se la roben o caduque, siga los pasos de la sección [Adición de una tarjeta de crédito como método de pago](#addcard). No es necesario actualizar el CVV.

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions) como administrador de la cuenta.
1. Seleccione la suscripción que está vinculada a la tarjeta.
1. Seleccione **Administrar métodos de pago**.
1. Seleccione **Editar** junto a la tarjeta que desee actualizar.
1. Actualice los detalles de la tarjeta de crédito o débito.
1. Seleccione **Guardar**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Uso de una tarjeta de crédito diferente para la suscripción a Azure

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions) como administrador de la cuenta.
1. Seleccione la suscripción que está vinculada a la tarjeta.
1. En el lado derecho de la página, seleccione **Manage payment methods**(Administrar métodos de pago).
1. Haga clic en **En su lugar, use**, junto a la tarjeta de crédito que desea usar. Esto también actualiza las otras suscripciones asociadas actualmente a esta tarjeta. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Eliminación de una tarjeta de crédito o débito de la cuenta

1. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions) como administrador de la cuenta.
1. Seleccione la suscripción que está vinculada a la tarjeta.
3. En el lado derecho de la página, seleccione **Manage payment methods**(Administrar métodos de pago).
4. Haga clic en **Eliminar** para la tarjeta de crédito que desea eliminar.

Si la tarjeta de crédito está asociada a otras suscripciones activas de Microsoft, no podrá eliminarla de su cuenta de Azure. Elimine la tarjeta de crédito de todas las suscripciones activas que tenga con Microsoft y vuelva a intentarlo.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Mi suscripción está deshabilitada. ¿Por qué no puedo eliminar mi tarjeta de crédito ahora?

Una vez que la suscripción está deshabilitada o se cancela, esperamos 90 días antes de eliminarla permanentemente. Mantenemos su método de pago en el archivo durante el período de retención por si desea reactivar la suscripción. Después de eso, la suscripción se elimina por completo.

Si tiene que quitar la tarjeta de crédito o débito antes de que finalice el período de retención de 90 días, [vuelva a activar la suscripción](billing-subscription-become-disable.md). Si no puede reactivarla, [póngase en contacto con el soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>¿Por qué sigo recibiendo un mensaje similar a "Su sesión de inicio de sesión ha expirado. Haga clic aquí para volver a iniciar sesión"?

Si sigue apareciendo este mensaje de error incluso si ya cerró la sesión y volvió a iniciarla, inténtelo de nuevo con una sesión de exploración privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>¿Cómo se usa una tarjeta diferente para cada suscripción que tengo?

Desgraciadamente, si sus suscripciones ya están usando la misma tarjeta, no es posible separarlas para utilizar varias tarjetas. Sin embargo, cuando se registra con una nueva suscripción, puede elegir usar un nuevo método de pago para esa suscripción.

### <a name="how-do-i-make-payments"></a>¿Cómo puedo realizar los pagos?

Si ha configurado una tarjeta de crédito o de débito como método de pago, se le cobra automáticamente en su tarjeta después de finalizar cada período de facturación. No es necesario hacer nada más.

Si desea [pagar con factura](billing-how-to-pay-by-invoice.md), envíe el pago a la ubicación indicada en la parte inferior de la factura.

### <a name="how-do-i-make-a-one-time-payment"></a>¿Cómo se puede realizar un pago único?

Por desgracia, Azure actualmente no admite los pagos únicos para las tarjetas de crédito o débito. 

### <a name="how-do-i-change-the-tax-id"></a>¿Cómo se cambia el número de identificación tributaria?

Para agregar o actualizar el número de identificación tributaria, visite [**Perfil** en el Centro de cuentas de Azure](https://account.azure.com/Profile) y seleccione **Registro fiscal**. Este número de identificación tributaria se utiliza para los cálculos de exención fiscal y aparece en la factura.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
