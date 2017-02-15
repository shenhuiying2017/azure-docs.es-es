---
title: "La base de datos en el servidor no está disponible en estos momentos, al conectar con SQL Database | Microsoft Docs"
description: "Solución de problemas del error &quot;La base de datos en el servidor no está disponible en estos momentos&quot;, al conectar con la base de datos SQL."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
keywords: "la base de datos en el servidor no está disponible en estos momentos para conectar con la base de datos SQL"
ms.assetid: f61999ac-d46b-448a-8830-3b04978d84ec
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede7672196e71bc493cd9b758982445e991def1b


---
# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Error "La base de datos en el servidor no está disponible en estos momentos" al conectar con la base de datos SQL
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Cuando una aplicación se conecte a una base de datos SQL de Azure, recibirá el mensaje de error siguiente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Este mensaje de error suele ser transitorio (breve).
> 
> 

Este error se produce cuando la base de datos de Azure se está moviendo (o reconfigurando) y la aplicación pierde la conexión con la base de datos SQL. Los eventos de reconfiguración de la base de datos SQL se producen debido a un evento planeado (por ejemplo, una actualización de software) o a un evento no planeado (por ejemplo, el bloqueo de un proceso o un equilibrio de carga). La mayoría de los eventos de reconfiguración son generalmente de corta duración y se completarán en menos de 60 segundos. Sin embargo, ocasionalmente estos eventos pueden tardar más tiempo de finalizar, por ejemplo, cuando una transacción grande produce una recuperación de larga duración.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Pasos para resolver los problemas de conectividad transitorios
1. Compruebe el [panel de Estado de Microsoft Azure](https://azure.microsoft.com/status) para comprobar si hay interrupciones conocidas que se hayan producido durante el tiempo en el que la aplicación informó de los errores.
2. Para las aplicaciones que se conectan a un servicio en la nube, como la base de datos SQL de Azure, se deben prever eventos periódicos de reconfiguración e implementación de la lógica de reintento para gestionar estos errores en lugar de mostrarlos como errores de la aplicación. Consulte la sección [Errores transitorios](sql-database-connectivity-issues.md), así como los procedimientos recomendados e instrucciones de diseño en [Información general de desarrollo de SQL Database](sql-database-develop-overview.md), donde encontrará más información e instrucciones para las estrategias generales de reintento. Después, para información específica, consulte ejemplos de código en [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md) .
3. Conforme una base de datos se acerca a sus límites de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
4. Si los problemas de conectividad continúan, si el tiempo de detección del error por parte de la aplicación supera los 60 segundos o si el error se repite varias veces en un día determinado, realice una solicitud de soporte técnico a Azure; para ello, seleccione **Obtener soporte** en el sitio [Soporte técnico de Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Pasos siguientes
* Si el error que se muestra es diferente, evalúe si en el [mensaje de error](sql-database-develop-error-messages.md) se proporciona alguna indicación sobre la causa.
* Si el problema persiste, visite la página [Solución de problemas de conexión de Base de datos SQL de Azure](sql-database-troubleshoot-common-connection-issues.md)que le servirá de guía.




<!--HONumber=Nov16_HO3-->


