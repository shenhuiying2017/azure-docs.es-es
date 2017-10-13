---
title: "Límites del programador y valores predeterminados"
description: "Límites del programador y valores predeterminados"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-limits-and-defaults"></a>Límites del programador y valores predeterminados
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Aceleradores, valores predeterminados, límites y cuotas de Programador
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Encabezado x-ms-request-id
Cada solicitud realizada en el servicio Programador devuelve un encabezado de respuesta denominado**x-ms-request-id**. Este encabezado contiene un valor opaco que identifica de forma única la solicitud.

Si una solicitud genera error sistemáticamente y se ha comprobado que la solicitud está formulada correctamente, se puede usar este valor para notificar el error a Microsoft. En el informe, incluya el valor de x-ms-request-id, la hora aproximada en que se realizó la solicitud, el identificador de la suscripción, la colección de trabajos o el trabajo, así como el tipo de operación que intenta realizar la solicitud.

## <a name="see-also"></a>Otras referencias
 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

