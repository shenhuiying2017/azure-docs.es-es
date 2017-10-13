---
title: "Configuración de alertas de crédito o facturación para las suscripciones de Azure | Microsoft Docs"
description: "Describe cómo puede configurar alertas en su factura de Azure para que pueda evitar sorpresas de facturación."
keywords: "alerta de crédito, alerta de facturación"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc0cb2b036e835450ee0fc404cce12439fabc77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Configuración de alertas de crédito o facturación para las suscripciones de Microsoft Azure
Si es el administrador de cuenta de una suscripción a Azure, puede utilizar el servicio de alertas de facturación de Azure para crear alertas de facturación personalizadas que le ayudarán a supervisar y administrar la actividad de facturación de las cuentas de Azure.

Este servicio está en versión preliminar, por lo que primero debe habilitarlo en la página Características de vista previa.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Establecimiento de los destinatarios de correo electrónico y del umbral de alerta
1. Visite [la página Características de vista previa](https://account.windowsazure.com/PreviewFeatures) y habilite **Billing Alert Service** (Servicio de alertas de facturación).

1. Después de recibir la confirmación de correo electrónico de que el servicio de facturación está activado para su suscripción, visite [la página de suscripciones](https://account.windowsazure.com/Subscriptions) en el portal de cuentas. Haga clic en la suscripción que desea supervisar y después haga clic en **Alertas**.

    ![Captura de pantalla de la vista de suscripciones del Centro de cuentas de Azure, con las alertas resaltadas][Image1]

2. Luego, haga clic en **Agregar alerta** para crear la primera alerta. Puede configurar un total de cinco alertas de facturación por suscripción, con un umbral diferente y hasta dos destinatarios de correo electrónico para cada alerta.

    ![Captura de pantalla de la vista Alertas, donde puede agregar alertas][Image2]

3. Cuando agregue una alerta, asígnele un nombre único, elija un umbral de gasto y elija las direcciones de correo electrónico a las que se enviarán las alertas. Al configurar el umbral, podrá elegir un **Total de facturación** o un **Crédito monetario** desde la lista **Alerta** lista. Para el total de facturación, se enviará una alerta cuando el gasto de la suscripción supere el umbral. Para un crédito monetario, se enviará una alerta cuando los créditos monetarios caigan por debajo del límite. Normalmente, los créditos monetarios se aplican a las suscripciones Evaluación gratuita y Visual Studio.

    ![Captura de pantalla de la vista de adición de alerta, donde puede configurar destinatarios][Image3]

Azure admite cualquier dirección de correo electrónico pero no comprueba que la dirección de correo electrónico funcione. Por lo tanto, revise si hay errores ortográficos.

## <a name="check-on-your-alerts"></a>Comprobación de las alertas
Después de configurar las alertas, el centro de cuentas enumera y muestra cuántas más se pueden configurar. Para cada alerta, se mostrará la fecha y la hora de envío, si es una alerta de total de facturación o de crédito monetario, así como el límite configurado. El formato de fecha y hora es de 24 horas según el horario universal coordinado (UTC) y la fecha tiene el formato aaaa-mm-dd. Haga clic en el signo más de una alerta de la lista para modificarla o haga clic en la papelera para eliminarla.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Alertas de facturación para los clientes del Contrato Enterprise (EA)
Los clientes EA pueden obtener alertas para cada departamento con una inscripción estableciendo cuotas de gastos. Consulte [Department Spending Quotas](https://ea.azure.com/helpdocs/departmentSpendingQuotas) (Cuotas de gastos de departamento) en el portal EA para empezar a trabajar.

## <a name="learn-more-about-azure-cost-management"></a>Más información sobre la administración de costos de Azure
- Calcule los costos mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/), la [calculadora de costo total de propiedad](https://aka.ms/azure-tco-calculator) y cuando agregue un servicio.
- [Revise el uso y los costos con regularidad en Azure Portal](billing-getting-started.md#costs).
- Active las [recomendaciones sobre el costo de Azure Advisor](../advisor/advisor-cost-recommendations.md).

Para obtener información, consulte [Introducción a la administración de costes y facturación de Azure](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
