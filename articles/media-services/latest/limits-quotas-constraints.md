---
title: Cuotas y limitaciones en Azure Media Services v3 | Microsoft Docs
description: En este tema se describen las cuotas y limitaciones en Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cuotas y limitaciones en Azure Media Services v3

En este tema se describen las cuotas y limitaciones en Azure Media Services v3.

| Recurso | Límite predeterminado | 
| --- | --- | 
| Recursos por cuenta de Azure Media Services | 1 000 000|
| JobInputs por trabajo | 100 |
| JobOutputs por trabajo | 30 (fijo) |
| Tamaño de archivo| En algunos casos, existe un límite máximo de tamaño de archivo admitido para el procesamiento en Media Services. <sup>(1)</sup> |
| Trabajos por cuenta de Media Services | 50,000<sup>(2)</sup> |
| LiveEvents por cuenta de Media Services |5|
| Cuentas de Media Services en una suscripción única | 25 (fijo) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs con el estado En ejecución por LiveEvent |3|
| LiveOutputs con el estado Detenido por LiveEvent |50|
| Cuentas de almacenamiento | 1000<sup>(4)</sup> (fija) |
| Puntos de conexión de streaming con el estado En ejecución por cuenta de Media Services|2|
| Transformaciones por cuenta de Media Services | 20 |
| StreamingLocators únicos asociados con un recurso al mismo tiempo | 20<sup>(5)</sup> |
  
<sup>1</sup>El tamaño máximo admitido para un único blob es actualmente de 5 TB en Azure Blob Storage. Sin embargo, en Azure Media Services se aplican límites adicionales en función de los tamaños de máquina virtual utilizados por el servicio. Si el archivo de origen es mayor de 260 GB, es muy probable que el trabajo presente un error. Si tiene contenido con formato 4K mayor que el límite de 260 GB, póngase en contacto con nosotros en amshelp@microsoft.com para posibles mitigaciones para admitir su escenario.

<sup>2</sup> Este número incluye los trabajos en cola, terminados, activos y cancelados. No incluye los trabajos eliminados. 

Se eliminarán automáticamente los registros de trabajo de más de 90 días de su cuenta, aunque el número total de registros no llegue a la cuota máxima. 

<sup>3</sup> Hay un límite de 1 000 000 entradas de StreamingPolicy para distintas directivas de Media Services(por ejemplo, para la directiva StreamingLocator o ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Debe utilizar el mismo identificador de directiva si siempre usa los mismos días, permisos de acceso, etc. 

<sup>4</sup> Las cuentas de almacenamiento deben proceder de la misma suscripción de Azure.

<sup>5</sup> Los StreamingLocators no están diseñados para administrar el control de acceso por usuario. Para conceder derechos de acceso diferentes a usuarios individuales, use las soluciones de administración de derechos digitales (DRM).

## <a name="support-ticket"></a>Incidencia de soporte técnico

En el caso de recursos que no son fijos, puede solicitar que se generen las cuotas abriendo una [incidencia de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Incluya información detallada en la solicitud en los cambios de cuota que se desean, en los escenarios de casos de uso y las regiones que se requieren. <br/>**No** cree cuentas adicionales de Azure Media Services para obtener límites mayores.

## <a name="next-steps"></a>Pasos siguientes

[Información general](media-services-overview.md)
