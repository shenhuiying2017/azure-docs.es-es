<properties
	pageTitle="Solución del problema de que la base de datos del servidor no esté disponible para Base de datos SQL de Azure"
	description="Pasos para identificar y resolver errores de conexión para la Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="daleche"/>

# Solución del error "Database &lt;x&gt; on server &lt;y&gt; is not currently available. Please retry the connection later" (La base de datos &lt;x&gt; del servidor &lt;y&gt; no está disponible. Vuelva a intentar la conexión más tarde.)
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Cuando una aplicación se conecte a una base de datos SQL de Azure, recibirá el mensaje de error siguiente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Este mensaje de error suele ser transitorio (breve).

Este error se produce cuando la base de datos de Azure se está moviendo (o reconfigurando) y la aplicación pierde la conexión con la base de datos SQL. Los eventos de reconfiguración de la base de datos SQL se producen debido a un evento planeado (por ejemplo, una actualización de software) o a un evento no planeado (por ejemplo, el bloqueo de un proceso o un equilibrio de carga). La mayoría de los eventos de reconfiguración son generalmente de corta duración y se completarán en menos de 60 segundos. Sin embargo, ocasionalmente estos eventos pueden tardar más tiempo de finalizar, por ejemplo, cuando una transacción grande produce una recuperación de larga duración.

## Pasos para resolver los problemas de conectividad transitorios
1.	Compruebe el [panel de estado de Microsoft Azure](https://azure.microsoft.com/status) durante las interrupciones conocidas que se produzcan durante el tiempo en el que la aplicación haya informado de los errores.
2. Para las aplicaciones que se conectan a un servicio en la nube, como la base de datos SQL de Azure, se deben prever eventos periódicos de reconfiguración e implementación de la lógica de reintento para gestionar estos errores en lugar de mostrarlos como errores de la aplicación. Consulte la sección sobre [errores transitorios](sql-database-connectivity-issues.md), y las prácticas recomendadas e instrucciones de diseño de [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) para obtener más información y conocer las estrategias de reintentos generales. Después, consulte ejemplos de código en [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md) para información específica.
3.	Conforme una base de datos se acerca a sus límites de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
4.	Si los problemas de conectividad continúan, si el tiempo de detección del error por parte de la aplicación supera los 60 segundos o si hay varias repeticiones del error en un día determinado, realice una solicitud de soporte técnico a Azure; para ello, seleccione **Obtener soporte** del sitio [Opciones de soporte técnico de Azure](https://azure.microsoft.com/support/options).

## Pasos siguientes
- Si el error que se muestra es diferente, evalúe si en el [mensaje de error](sql-database-develop-error-messages.md) se proporciona alguna indicación sobre la causa.
- Si el problema persiste, visite la página de ayuda de [Solución de problemas de conexión comunes relacionados con la base de datos SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

<!---HONumber=AcomDC_0629_2016-->