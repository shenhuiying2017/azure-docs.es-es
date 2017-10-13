---
title: "¿Qué es el Programador de Azure? | Microsoft Docs"
description: "Programador de Azure le permite describir mediante declaración las acciones para ejecutar en la nube. A continuación, programa y ejecuta esas acciones de forma automática."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-scheduler"></a>¿Qué es el Programador de Azure?
Programador de Azure le permite describir mediante declaración las acciones para ejecutar en la nube. A continuación, programa y ejecuta esas acciones de forma automática.  Para hacerlo, Programador usa [Azure Portal](scheduler-get-started-portal.md), código, [API de REST](https://msdn.microsoft.com/library/mt629143.aspx) o Azure PowerShell.

El Programador crea, mantiene e invoca el trabajo programado.  El Programador no hospeda ninguna carga de trabajo ni ejecuta código. Solo *invoca* código hospedado en cualquier otro lugar (en Azure, localmente o con otro proveedor). Realiza la invocación mediante HTTP, HTTPS, una cola de almacenamiento, una cola de bus de servicio o un tema de bus de servicio.

El Programador programa [trabajos](scheduler-concepts-terms.md), mantiene un historial de los resultados de la ejecución de trabajos que se puede consultar y programa, de manera determinante y confiable, las cargas de trabajo que se ejecutarán. Azure WebJobs (parte de la característica de Aplicaciones web del Servicio de aplicaciones de Azure) y otras funcionalidades de programación de Azure usan el Programador en segundo plano. La [API de REST de Programador](https://msdn.microsoft.com/library/mt629143.aspx) ayuda a administrar la comunicación para estas acciones. De ese modo, el Programador admite [programaciones complejas y periodicidad avanzada](scheduler-advanced-complexity.md) con facilidad.

Existen varios escenarios que se prestan para el uso del Programador. Por ejemplo:

* *Acciones de aplicaciones recurrentes*: recopilación periódica de datos desde Twitter en una fuente.
* *Mantenimiento diario*: eliminación diaria de registros, realización de copias de seguridad y otras tareas de mantenimiento. Por ejemplo, un administrador puede optar por hacer una copia de seguridad de su base de datos a la 1:00 a.m. todos los días durante los próximos 9 meses.

Programador permite crear, actualizar, eliminar, ver y administrar [colecciones de trabajos y trabajos](scheduler-concepts-terms.md) de manera programática, mediante scripts y en el portal.

## <a name="see-also"></a>Otras referencias
 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

