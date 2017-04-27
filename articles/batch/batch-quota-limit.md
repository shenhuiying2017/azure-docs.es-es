---
title: "Límites y cuotas del servicio para Azure Batch | Microsoft Docs"
description: "Obtenga información sobre las restricciones, los límites y las cuotas de Azure Batch predeterminados y cómo solicitar un aumento de la cuota."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 4d2ba8d3a5efad1be3395aae732874e7a770f64b
ms.lasthandoff: 04/06/2017


---
# <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio Lote

Al igual que en otros servicios de Azure, existen límites en determinados recursos asociados con el servicio Lote. Muchos de ellos son cuotas predeterminadas que Azure aplica en el nivel de cuenta o suscripción. En este artículo se describen esos valores predeterminados y cómo solicitar un aumento de la cuota.

Tenga presente estas cuotas cuando diseñe y escale las cargas de trabajo de Lote. Por ejemplo, si su grupo no alcanza el número objetivo de nodos de proceso especificado, es posible que se haya alcanzado el límite de cuota de núcleos de la cuenta de Lote.

Se pueden ejecutar varias cargas de trabajo de Batch en una sola cuenta de Batch, o bien distribuir las cargas de trabajo entre cuentas de Batch que se encuentren en la misma suscripción, pero en diferentes regiones de Azure.

Si planea ejecutar cargas de trabajo de producción en Lote, es posible que tenga que aumentar el valor predeterminado de una o varias de las cuotas. Si desea aumentar una cuota, puede abrir una [solicitud de soporte técnico al cliente](#increase-a-quota) en línea sin ningún costo.

> [!NOTE]
> Una cuota es un límite de crédito, no una garantía de capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure.
> 
> 

## <a name="resource-quotas"></a>Cuotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Otros límites
| **Recurso** | **Límite máximo** |
| --- | --- |
| [Tareas simultáneas](batch-parallel-node-tasks.md) por nodo de proceso |4 × número de núcleos de nodo |
| [Aplicaciones](batch-application-packages.md) por cuenta de Lote |20 | |
| Paquetes de aplicación por aplicación |40 |
| Tamaño del paquete de aplicación (cada uno) |Aprox. 195 GB<sup>1</sup> |

<sup>1</sup> Límite de Almacenamiento de Azure para el tamaño máximo de blob en bloques

## <a name="view-batch-quotas"></a>Visualización de las cuotas de Lote
Vea las cuotas de la cuenta de Batch en [Azure Portal][portal].

1. Seleccione **Cuentas de Batch** en el portal y, luego, seleccione la cuenta de Batch que le interesan.
2. Seleccione **Propiedades** en la hoja del menú de la cuenta de Batch
3. La hoja Propiedades muestra las **cuotas** que hay aplicadas actualmente en la cuenta de Batch.
   
    ![Cuotas de la cuenta de Lote][account_quotas]

## <a name="increase-a-quota"></a>Aumento de la cuota
Siga estos pasos para solicitar un aumento de la cuota mediante [Azure Portal][portal].

1. Seleccione el icono **Ayuda y soporte técnico** en el panel del portal o el signo de interrogación (**?**) en la esquina superior derecha del portal.
2. Seleccione **Nueva solicitud de soporte técnico** > **Básico**.
3. En la hoja **Básico** :
   
    a. **Tipo de problema** > **Cuota**
   
    b. Seleccione su suscripción.
   
    c. **Tipo de cuota** > **Batch**
   
    d. **Plan de soporte técnico** > **Compatibilidad con cuotas (incluida)**
   
    Haga clic en **Siguiente**.
4. En la hoja **Problema** :
   
    a. Seleccione una de las opciones en **Gravedad** según su [impacto en el negocio][support_sev].
   
    b. En **Detalles**, especifique cada cuota que desee cambiar, el nombre de cuenta de Lote y el nuevo límite.
   
    Haga clic en **Siguiente**.
5. En la hoja **Información de contacto** :
   
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

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

