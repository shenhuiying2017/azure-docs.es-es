---
title: "Límites y cuotas del servicio para Azure Batch | Microsoft Docs"
description: "Obtenga información sobre las restricciones, los límites y las cuotas de Azure Batch predeterminados y cómo solicitar un aumento de la cuota."
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bb5470918aa3ad15cb9081f5c02b40984d83f51
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio Lote

Al igual que en otros servicios de Azure, existen límites en determinados recursos asociados con el servicio Lote. Muchos de ellos son cuotas predeterminadas que Azure aplica en el nivel de cuenta o suscripción. En este artículo se describen esos valores predeterminados y cómo solicitar un aumento de la cuota.

Tenga en cuenta estas cuotas al diseñar y escalar verticalmente sus cargas de trabajo de Batch. Por ejemplo, si su grupo no alcanza el número objetivo de nodos de proceso especificado, es posible que se haya alcanzado el límite de cuota de núcleos de la cuenta de Batch.

Se pueden ejecutar varias cargas de trabajo de Batch en una sola cuenta de Batch, o bien distribuir las cargas de trabajo entre cuentas de Batch que se encuentren en la misma suscripción, pero en diferentes regiones de Azure.

Si planea ejecutar cargas de trabajo de producción en Lote, es posible que tenga que aumentar el valor predeterminado de una o varias de las cuotas. Si desea aumentar una cuota, puede abrir una [solicitud de soporte técnico al cliente](#increase-a-quota) en línea sin ningún costo.

> [!NOTE]
> Una cuota es un límite de crédito, no una garantía de capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure.
> 
> 

## <a name="resource-quotas"></a>Cuotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="quotas-in-user-subscription-mode"></a>Cuotas en el modo de suscripción de usuario

Si usó una versión anterior de la API de Batch para crear una cuenta de Batch con el modo de asignación de grupo establecido en **suscripción de usuario**, las cuotas se aplican de forma diferente. En este modo, que ya no se recomienda, las máquinas virtuales de Batch y otros recursos se crean directamente en su suscripción cuando se crea un grupo. La cuota de núcleos de Azure Batch no se aplica a una cuenta creada en este modo. En su lugar, se aplican las cuotas en la suscripción para núcleos de proceso regionales y otros recursos. Aprenda más sobre estas cuotas en [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Otros límites
| **Recurso** | **Límite máximo** |
| --- | --- |
| [Tareas simultáneas](batch-parallel-node-tasks.md) por nodo de proceso |4 × número de núcleos de nodo |
| [Aplicaciones](batch-application-packages.md) por cuenta de Lote |20 | |
| Paquetes de aplicación por aplicación |40 |
| Tamaño del paquete de aplicación (cada uno) |Aprox. 195 GB<sup>1</sup> |
| Tamaño máximo de la tarea de inicio | 32 768 caracteres<sup>2</sup> |

<sup>1</sup> Límite de Almacenamiento de Azure para el tamaño máximo de blob en bloques<br />
<sup>2</sup> Incluye archivos de recursos y variables de entorno



## <a name="view-batch-quotas"></a>Visualización de las cuotas de Lote
Vea las cuotas de la cuenta de Batch en [Azure Portal][portal].

1. Seleccione **Cuentas de Batch** en el portal y, luego, seleccione la cuenta de Batch que le interesan.
2. Seleccione **Cuotas** en el menú de la cuenta de Batch.
3. Visualización de las cuotas aplicadas actualmente a la cuenta de Batch
   
    ![Cuotas de la cuenta de Lote][account_quotas]



## <a name="increase-a-quota"></a>Aumento de la cuota
Siga estos pasos para solicitar un aumento de la cuota para la cuenta de Batch o la suscripción con [Azure Portal][portal]. El tipo de aumento de cuota depende del modo de asignación de grupo de su cuenta de Batch.

### <a name="increase-a-batch-cores-quota"></a>Aumento de la cuota de núcleos de Batch 

1. Seleccione el icono **Ayuda y soporte técnico** en el panel del portal o el signo de interrogación (**?**) en la esquina superior derecha del portal.
2. Seleccione **Nueva solicitud de soporte técnico** > **Básico**.
3. En **Aspectos básicos**:
   
    a. **Tipo de problema** > **Cuota**
   
    b. Seleccione su suscripción.
   
    c. **Tipo de cuota** > **Batch**
   
    d. **Plan de soporte técnico** > **Compatibilidad con cuotas (incluida)**
   
    Haga clic en **Siguiente**.
4. En **Problema**:
   
    a. Seleccione una de las opciones en **Gravedad** según su [impacto en el negocio][support_sev].
   
    b. En **Detalles**, especifique cada cuota que desee cambiar, el nombre de cuenta de Lote y el nuevo límite.
   
    Haga clic en **Siguiente**.
5. En **Información de contacto**:
   
    a. Seleccione un valor en **Método de contacto preferido**.
   
    b. Compruebe y especifique los detalles de contacto necesarios.
   
    Haga clic en **Crear** para enviar la solicitud de soporte técnico.

Una vez que haya enviado la solicitud de soporte técnico, el servicio de soporte técnico de Azure se comunicará con usted. Tenga en cuenta que se puede tardar hasta 2 días laborables en completar la solicitud.


## <a name="related-topics"></a>Temas relacionados
* [Creación de una cuenta de Lote de Azure con el Portal de Azure](batch-account-create-portal.md)
* [Información general de las características de Lote de Azure](batch-api-basics.md)
* [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
