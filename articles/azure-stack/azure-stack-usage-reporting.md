---
title: Notificar los datos de uso de Azure Stack a Azure | Microsoft Docs
description: "Aprenda cómo configurar datos de uso de informes en Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2A397C83-1A6C-4290-B0EF-FDC34EE290CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mabrigg;AlfredoPizzirani
ms.openlocfilehash: 451e8c604e48d9cfc42284f2413fa5ed4fea03bc
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Notificar los datos de uso de Azure Stack a Azure 

Los datos de uso, también denominados datos de consumo, representan la cantidad de recursos usados. 

Los sistemas de varios nodos de Azure Stack que usan el modelo de facturación basado en el consumo deben notificar datos de uso a Azure para la facturación.  Los operadores de Azure Stack deben configurar su instancia de Azure Stack para informar de los datos de uso a Azure.

> [!NOTE]
> Los informes de datos de uso son obligatorios para los usuarios de varios nodos de Azure Stack con una licencia de modelo de pago por uso. Es opcional para los clientes con una licencia de modelo de capacidad (vea la [página Cómo comprar Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack)). Para los usuarios de Azure Stack Development Kit, los operadores de Azure Stack pueden notificar los datos de uso y probar la característica. Sin embargo, no se cobrará a los usuarios por el uso. 


![flujo de facturación](media/azure-stack-usage-reporting/billing-flow.png)

Los datos de uso se envían desde Azure Stack a Azure a través de Azure Bridge. En Azure, el sistema commerce procesa los datos de uso y genera la factura. Cuando se genera la factura, el propietario de la suscripción de Azure puede verla y descargarla desde el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions). Para aprender cómo se autoriza el uso de Azure Stack, consulte el [documento de precios y empaquetado de Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Configuración de informes de datos de uso

Para configurar los informes de datos de uso, debe [registrar la instancia de Azure Stack con Azure](azure-stack-register.md). Como parte del proceso de registro, se configura el componente Azure Bridge de Azure Stack, que conecta Azure Stack a Azure y envía los datos de uso. Los siguientes datos de uso se envían desde Azure Stack a Azure:

- **Id. de medidor**: Identificador único del recurso que se consumió.
- **Cantidad**: cantidad de uso de los recursos.
- **Ubicación**: Ubicación donde se implementa el recurso actual de Azure Stack.
- **URI de recurso**: URI completo del recurso para el que se notifica el uso.
- **Id. de suscripción**: Identificador de suscripción del usuario de Azure Stack. Se trata de la suscripción (Azure Stack) local.
- **Tiempo**: Hora de inicio y finalización de los datos de uso. Hay un retraso determinado entre el momento en que se usan estos recursos en Azure Stack y el momento en que se notifican los datos de uso a commerce. Azure Stack agrega los datos de uso para cada 24 horas y los datos de uso de informes para la canalización del comercio en Azure llevan algunas horas más. Por lo tanto, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente.

## <a name="generate-usage-data-reporting"></a>Generación de informes de datos de uso

1. Para probar los informes de datos de uso, cree unos pocos recursos en Azure Stack. Por ejemplo, puede crear una [cuenta de almacenamiento](azure-stack-provision-storage-account.md), una [máquina virtual Windows Server](azure-stack-provision-vm.md) y una máquina virtual Linux con SKU estándar y básico para ver cómo se informa del uso del núcleo. Los datos de uso para diferentes tipos de recursos se notifican en medidores diferentes.

2. Deje que los recursos se ejecuten durante horas. Información de uso se recopila aproximadamente una vez cada hora. Después de recopilarse, estos datos se transmiten a Azure y se procesan en el sistema Azure commerce. Este proceso puede tardar varias horas.

## <a name="view-usage---csp-subscriptions"></a>Visualización del uso: suscripciones de CSP

Si registró Azure Stack usando una suscripción de CSP, puede ver los cargos y el uso de la misma manera en que se ve el consumo de Azure. El uso de Azure Stack se va a incluir en la factura y en el archivo de reconciliación, disponibles a través del [Centro de asociados de Microsoft](https://partnercenter.microsoft.com/partner/home). El archivo de reconciliación se actualiza mensualmente. Si necesita acceso a información de uso reciente de Azure Stack, puede usar las Partner Center API.

   ![centro de asociados](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Visualización del uso: suscripciones al Contrato Enterprise

Si registró Azure Stack usando una suscripción del Contrato Enterprise, puede ver los cargos y el uso en el [portal de EA](https://ea.azure.com/). El uso de Azure Stack se incluirá en las descargas avanzadas junto con el uso de Azure en la sección de informes en el portal de EA. 

## <a name="view-usage--other-subscriptions"></a>Visualización del uso: otras suscripciones

Si ha registrado Azure Stack con cualquier otro tipo de suscripción, por ejemplo, una de pago por uso, puede ver el uso y los cargos en el centro de cuentas de Azure. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions) como administrador de cuenta de Azure y seleccione la suscripción de Azure que usa para registrar Azure Stack. Puede ver los datos de uso de Azure Stack, la cantidad que se cobra por cada uno de los recursos usados, como se muestra en la siguiente imagen:

   ![flujo de facturación](media/azure-stack-usage-reporting/pricing-details.png)

Para Azure Stack Development Kit, los recursos de Azure Stack tampoco se cobran; el precio se muestra como 0,00 USD. Cuando varios nodos de Azure Stack comienzan a estar disponibles con carácter general, puede ver el costo real para cada uno de estos recursos.

## <a name="which-azure-stack-deployments-are-charged"></a>¿Qué implementaciones de Azure Stack se cobran?

El uso de recursos es gratuito para Azure Stack Development Kit. Para los sistemas de varios nodos de Azure Stack, se cobran las máquinas virtuales de carga de trabajo, los servicios de almacenamiento y App Services.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>¿Se les cobrará a los usuarios la infraestructura de las máquinas virtuales?

No. Los datos de uso de algunas máquinas virtuales del proveedor de recursos de Azure Stack se notifican a Azure, pero no se cobran las máquinas virtuales que se crearan durante la implementación para habilitar la infraestructura de Azure Stack.  

Solo se cobra a los usuarios las máquinas virtuales que se ejecuten en suscripciones de inquilinos. Todas las cargas de trabajo se deben implementar con suscripciones de inquilino a fin de cumplir los términos de licencia de Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Tengo una licencia de Windows Server que deseo utilizar en Azure Stack, ¿cómo lo hago?

El uso de las licencias existentes impide generar medidores de uso. Las licencias existentes de Windows Server pueden utilizarse en Azure Stack, tal como se describe en la sección "Uso de software existente con Azure Stack" de la [Guía de licencias de Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Los clientes deben implementar sus máquinas virtuales Windows Server como se describe en el tema [Ventaja de uso híbrido de Azure para Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) a fin de usar sus licencias existentes.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>¿Qué suscripción se cobra por los recursos consumidos?
La suscripción que se proporciona cuando [se registra Azure Stack con Azure](azure-stack-register.md) se cobra.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>¿Qué tipos de suscripciones se admiten para informar de los datos de uso?

En Azure Stack de varios nodos, se admiten suscripciones de Contrato Enterprise (EA) y CSP. En Azure Stack Development Kit, las suscripciones Enterprise Agreement (EA), pago por uso, CSP y MSDN admiten la notificación de datos de uso.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>¿El informe de datos de uso funciona en las nubes soberanas?

En Azure Stack Development Kit, los informes de datos de uso requieren suscripciones creadas en el sistema global de Azure. No se pueden registrar las suscripciones creadas en una de las nubes soberanas (nubes del Azure Government, Azure Germany y Azure China) con Azure, por lo que no admiten informes de datos de uso.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>¿Cómo pueden identificar los usuarios los datos de uso de Azure Stack en el portal de facturación de Azure?

Los usuarios pueden ver los datos de uso del Azure Stack en el archivo de detalles de uso. Para saber cómo obtener el archivo de detalles de uso, consulte el artículo [Descargar archivo de uso desde el Centro de cuentas de Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). El archivo de detalles de uso contiene los medidores de Azure Stack que identifican las máquinas virtuales y el almacenamiento en Azure Stack. Todos los recursos usados en Azure Stack se notifican en la región denominada "Azure Stack".

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>¿Por qué el uso notificado en Azure Stack no coincide con el informe generado a partir del Centro de cuentas de Azure?

Siempre hay un retraso entre los datos de uso notificados por las API de uso de Azure Stack y los datos de uso que notifica el Centro de cuentas de Azure. El retraso es el tiempo que hace falta para cargar datos de uso de Azure Stack en Azure commerce. Debido a este retraso, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente. Puede ver la diferencia si usa la [API de uso de Azure Stack](azure-stack-provider-resource-api.md)y compara los resultados con el uso notificado en el portal de facturación de Azure.

## <a name="next-steps"></a>Pasos siguientes

* [API de uso de proveedor](azure-stack-provider-resource-api.md)  
* [API de uso de inquilino](azure-stack-tenant-resource-usage-api.md)
* [Preguntas más frecuentes sobre uso](azure-stack-usage-related-faq.md)
