---
title: Notificar los datos de uso de Azure Stack a Azure | Microsoft Docs
description: "Aprenda cómo configurar datos de uso de informes en Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: sngun;AlfredoPizzirani
ms.openlocfilehash: 695b93a818d3c0e615bb79e0a2861e134b2f5c89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Notificar los datos de uso de Azure Stack a Azure 

Los datos de uso, también denominados datos de consumo, representan la cantidad de recursos usados. Los sistemas integrados de Azure Stack que usan el modelo de facturación basado en el consumo deben notificar datos de uso a Azure. Estos datos de uso se usan en la facturación. Es responsabilidad del operador de Azure Stack configurar su entorno de Azure Stack con los informes de datos de uso.


> [!NOTE]
> Los informes de datos de uso son obligatorios para los usuarios de sistemas integrados de Azure Stack con una licencia de modelo de pago por uso. Sin embargo, son opcionales para los clientes con una licencia de modelo de capacidad. En Azure Stack Development Kit, los operadores de nube pueden notificar datos de uso y probar la característica, sin embargo, no se les cobra por el uso que realicen. Para aprender más sobre los precios en Azure Stack, consulte la página [Cómo comprar Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="usage-data-reporting-flow"></a>Flujo de informes de datos de uso

Los datos de uso se envían desde Azure Stack a Azure a través de Azure Bridge. En Azure, el sistema commerce procesa los datos de uso y genera la factura. Cuando se genera la factura, el propietario de la suscripción de Azure puede verla y descargarla desde el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions). Para aprender cómo se autoriza el uso de Azure Stack, consulte el [documento de precios y empaquetado de Azure Stack](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409). 

La imagen siguiente es una representación gráfica del flujo de datos de uso de Azure Stack:

![flujo de facturación](media/azure-stack-usage-reporting/billing-flow.png)

## <a name="set-up-usage-data-reporting"></a>Configuración de informes de datos de uso

Para configurar los informes de datos de uso en Azure Stack, debe [registrar la instancia de la Azure Stack con Azure](azure-stack-register.md). Como parte del proceso de registro, el componente Azure Bridge de Azure Stack está configurado para conectar Azure Stack a Azure. Después de configurar Azure Bridge, se envían los siguientes datos de uso a Azure: 

* **Id. de medidor**: Identificador único del recurso que se consumió.
* **Cantidad**: cantidad de datos de uso de recursos.
* **Ubicación**: Ubicación donde se implementa el recurso actual de Azure Stack.
* **URI de recurso**: URI completo del recurso para el que se notifica el uso. 
* **Id. de suscripción**: identificador de suscripción del usuario de Azure Stack.
* **Tiempo**: Hora de inicio y finalización de los datos de uso. Existe algún retraso entre el momento en que se usan estos recursos en Azure Stack y el momento en que se notifican los datos de uso a commerce. Azure Stack agrega los datos de uso para cada 24 horas y los datos de uso de informes para la canalización del comercio en Azure llevan algunas horas más. Por lo tanto, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente.

## <a name="test-usage-data-reporting"></a>Prueba de informes de datos de uso 

1. Para probar los informes de datos de uso, cree unos pocos recursos en Azure Stack. Por ejemplo, puede crear una [cuenta de almacenamiento](azure-stack-provision-storage-account.md), una [máquina virtual Windows Server](azure-stack-provision-vm.md) y una máquina virtual Linux con SKU estándar y básico para ver cómo se informa del uso del núcleo. Los datos de uso para diferentes tipos de recursos se notifican en medidores diferentes.  

2. Deje que los recursos se ejecuten durante horas. Información de uso se recopila aproximadamente una vez cada hora. Después de recopilarse, estos datos se transmiten a Azure y se procesan en el sistema Azure commerce. Este proceso puede tardar varias horas.  

3. Inicie sesión en el [Centro de cuentas de Azure](https://account.windowsazure.com/Subscriptions) como administrador de cuenta de Azure y seleccione la suscripción de Azure que usa para registrar Azure Stack. Puede ver los datos de uso de Azure Stack, la cantidad que se cobra por cada uno de los recursos usados, como se muestra en la siguiente imagen:  

   ![flujo de facturación](media/azure-stack-usage-reporting/pricing-details.png)

En Azure Stack Development Kit, no se cobra por los recursos, así que el precio se muestra como 0,00 USD. En los sistemas integrados de Azure Stack, se muestra el costo real de cada uno de estos recursos.

## <a name="are-users-charged-for-the-infrastructure-virtual-machines"></a>¿Se cobra a los usuarios por las máquinas virtuales de infraestructura?
No, los datos de uso de algunas máquinas virtuales del proveedor de recursos de Azure Stack y de las máquinas virtuales de infraestructura que se crean durante la implementación se notifican a Azure, pero no se cobra por ellos. 

Los usuarios solo pagan por las máquinas virtuales que se crean con suscripciones de usuario. Por tanto, todas las cargas de trabajo se deben implementar con suscripciones de usuario a fin de cumplir los términos de licencia de Azure Stack.

## <a name="how-do-i-use-my-existing-windows-server-licenses-in-azure-stack"></a>¿Cómo se usan las licencias de Windows Server existentes en Azure Stack? 
Las licencias de Windows Server existentes se pueden usar en Azure Stack. Este modelo se conoce como BYOL (traiga su propia licencia). El uso de licencias que ya posee evita la generación de metros de uso adicionales. Para usar sus licencias existentes, debe implementar las máquinas virtuales Windows Server como se describe en el tema [Ventaja de uso híbrido de Azure para Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). 

## <a name="what-azure-meters-are-used-when-reporting-usage-data-in-integrated-systems"></a>¿Qué medidores de Azure se usan para notificar datos de uso en sistemas integrados?
* **Medidores de precio total**: Utilizados para los recursos asociados a las cargas de trabajo del usuario.  
* **Medidores de administración**: Utilizados para los recursos de infraestructura. Estos medidores no tienen costo.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>¿Qué suscripción se cobra por los recursos consumidos?
La suscripción que se proporciona cuando [se registra Azure Stack con Azure](azure-stack-register.md) se cobra.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>¿Qué tipos de suscripciones se admiten para informar de los datos de uso?
En sistemas integrados de Azure Stack, se admiten suscripciones de Enterprise Agreement (EA) y CSP. 

En Azure Stack Development Kit, las suscripciones Enterprise Agreement (EA), pago por uso, CSP y MSDN admiten la notificación de datos de uso.

## <a name="which-sovereign-clouds-support-usage-data-reporting"></a>¿Que nubes independientes admiten la notificación de datos de uso?
Para la notificación de datos de uso en Azure Stack Development Kit, es necesario que se creen suscripciones en el sistema global de Azure. No se pueden registrar las suscripciones creadas en una de las nubes soberanas (nubes del Azure Government, Azure Germany y Azure China) con Azure, por lo que no admiten informes de datos de uso. 

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>¿Cómo pueden identificar los usuarios los datos de uso de Azure Stack en el portal de facturación de Azure?
Los usuarios pueden ver los datos de uso del Azure Stack en el archivo de detalles de uso. Para saber cómo obtener el archivo de detalles de uso, consulte el artículo [Descargar archivo de uso desde el Centro de cuentas de Azure](../billing/billing-download-azure-invoice-daily-usage-date.md#download-usage-from-the-account-center-csv). El archivo de detalles de uso contiene los medidores de Azure Stack que identifican las máquinas virtuales y el almacenamiento en Azure Stack. Todos los recursos usados en Azure Stack se notifican en la región denominada "Azure Stack".

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>¿Por qué el uso notificado en Azure Stack no coincide con el informe generado a partir del Centro de cuentas de Azure?
Existe un retraso entre los datos de uso notificados por las API de uso de Azure Stack y los datos de uso que notifica el Centro de cuentas de Azure. El retraso es el tiempo que hace falta para cargar datos de uso de Azure Stack en Azure commerce. Debido a este retraso, el uso que se produce brevemente antes de medianoche puede aparecer en Azure al día siguiente. Puede ver la diferencia si usa las [API de uso de Azure Stack](azure-stack-provider-resource-api.md) y compara los resultados con el uso notificado en el portal de facturación de Azure.

## <a name="next-steps"></a>Pasos siguientes

* [API de uso de proveedor](azure-stack-provider-resource-api.md)  
* [API de uso de inquilino](azure-stack-tenant-resource-usage-api.md)
* [Preguntas más frecuentes sobre uso](azure-stack-usage-related-faq.md)