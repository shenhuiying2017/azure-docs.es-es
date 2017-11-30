---
title: Serie de SKU no disponibles | Microsoft Docs
description: "Algunas series de SKU no están disponibles para la suscripción seleccionada para esta región."
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>Región o SKU no disponible
En este artículo se describe cómo resolver el problema de que una suscripción a Azure no tenga acceso a una región o a la SKU de una máquina virtual.

## <a name="symptoms"></a>Síntomas

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Al implementar una máquina virtual, recibirá uno de los siguientes mensajes de error:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Al comprar instancias reservadas de máquina virtual, recibirá uno de los siguientes mensajes de error:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Al crear una solicitud de soporte técnico para aumentar la cuota de núcleos de proceso, no se podrán seleccionar regiones ni familias de SKU.

## <a name="solution"></a>Solución
En primer lugar, le recomendamos que considere usar una región o SKU alternativa que se adapte sus necesidades empresariales. Si no encuentra una región o SKU que sea adecuada, cree una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) del tipo "Administración de la suscripción" siguiendo los pasos que se muestran a continuación:


- En la página Aspectos básicos, seleccione "Administración de la suscripción" como tipo de problema, seleccione la suscripción y haga clic en "Siguiente".

![Hoja Básico](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   En la página Problema, seleccione "Otras cuestiones generales" como tipo de problema.
- En la sección Detalles:
  - Indique si quiere implementar máquinas virtuales o comprar instancias reservadas de máquina virtual.
  - Especifique la región, la SKU y el número de instancias de máquina virtual que tiene previsto implementar o comprar.


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Escriba su información de contacto y haga clic en "Crear".

![Información de contacto](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Comentarios
Siempre estamos abiertos a todo tipo de comentarios y sugerencias. No dude en enviarnos sus [sugerencias](https://feedback.azure.com/forums/266794-support-feedback). Además, puede ponerse en contacto con nosotros a través de [Twitter](https://twitter.com/azuresupport) o los [foros de MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Más información
[Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq)

