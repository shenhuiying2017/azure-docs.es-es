---
title: "Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure"
description: "Pasos para identificar y resolver errores de conexión comunes para la base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: felixwu
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 48ccd940efb75427461c3a8018aa6b31f46a626e


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solución de problemas de conexión de Base de datos SQL de Azure
Cuando la conexión a Base de datos SQL de Azure no se logra establecer, se reciben [mensajes de error](sql-database-develop-error-messages.md). Este artículo es un tema centralizado que ayuda a la solución de problemas de conectividad de Base de datos SQL de Azure. Se presentan [las causas habituales](#cause) de los problemas de conexión, se recomienda [una herramienta de solución de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que lo ayuda a identificar el problema y se proporcionan pasos de solución de problemas para resolver [errores transitorios](#troubleshoot-transient-errors) y [errores persistentes o no transitorios](#troubleshoot-the-persistent-errors). Por último, se enumeran [todos los artículos relacionados con la conectividad de Base de datos SQL de Azure](#all-topics-for-azure-sql-database-connection-problems).

Si encuentra problemas de conexión, pruebe los pasos de solución de problemas que se describen en este artículo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
Los problemas de conexión pueden tener alguna de las siguientes causas:

* Error al aplicar los procedimientos recomendados y las directrices de diseño durante el proceso de diseño de aplicaciones.  Para comenzar, consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) .
* Reconfiguración de la Base de datos SQL de Azure
* Configuración de firewall
* Tiempo de espera de conexión agotado
* Información de inicio de sesión incorrecta
* Límite máximo alcanzado en algunos recursos de Base de datos SQL de Azure

Por lo general, los problemas de conexión de Base de datos SQL de Azure se pueden clasificar en los siguientes:

* [Errores transitorios (corta duración o intermitentes)](#troubleshoot-transient-errors)
* [Errores persistentes o no transitorios (errores que se repiten con frecuencia)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Pruebe el solucionador de problemas para los problemas de conectividad de Base de datos SQL de Azure
Si se produce un error de conexión específico, pruebe [esta herramienta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que le ayudará a identificar y resolver el problema.

## <a name="troubleshoot-transient-errors"></a>Solución de problemas de errores transitorios
Si la aplicación está experimentando errores transitorios, revise los siguientes temas para obtener sugerencias acerca de cómo solucionar problemas y reducir la frecuencia de estos errores:

* [Troubleshooting Database &lt;x&gt; on Server &lt;y&gt; is unavailable (Error: 40613)](sql-database-troubleshoot-connection.md) (Base de datos de solución de problemas de datos x en el servidor y no está disponible [Error: 40613])
* [Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Solución de problemas de errores persistentes (no transitorios)
Si la aplicación no se puede conectar a la Base de datos SQL de Azure de forma persistente, normalmente indica un problema con uno de los siguientes elementos:

* Configuración del firewall. La base de datos SQL de Azure o el firewall del cliente están bloqueando las conexiones de la base de datos SQL de Azure.
* Reconfiguración de la red en el lado cliente: por ejemplo, un nuevo servidor de proxy o dirección IP.
* Error de usuario: por ejemplo, escribió incorrectamente los parámetros de conexión, como el nombre del servidor en la cadena de conexión.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Pasos para resolver los problemas de conectividad persistentes
1. Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente.
2. En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes. Consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) para obtener indicaciones adicionales.
3. Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de los problemas de conectividad](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Todos los temas de problemas de conexión de Base de datos SQL de Azure
En la siguiente tabla se enumeran todos los temas de problemas de conexión que se aplican directamente al servicio Base de datos SQL de Azure.

| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 1 |[Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](sql-database-troubleshoot-common-connection-issues.md) |Se trata de la página de inicio para solucionar problemas de conectividad de Base de datos SQL de Azure. En ella se describe cómo identificar y resolver errores transitorios y errores persistentes o no transitorios en Base de datos SQL de Azure. |
| 2 |[Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md) |Aprenda a solucionar problemas, diagnosticar y evitar un error de conexión de SQL o un error transitorio en la Base de datos SQL de Azure. |
| 3 |[Orientación general sobre reintentos](../best-practices-retry-general.md) |Se proporcionan instrucciones generales para el tratamiento de los errores transitorios al conectarse a Base de datos SQL de Azure. |
| 4 |[Solucionar problemas de conectividad con la Base de datos SQL de Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |Esta herramienta ayuda a identificar problemas y a resolver problemas de conexión. |
| 5 |[Solucionar el problema del mensaje "Database &lt;x&gt; v &lt;y&gt; is not currently available. Please retry the connection later" (La base de datos &lt;x&gt; del servidor &lt;y&gt; no está disponible. Vuelva a intentar la conexión más tarde)](sql-database-troubleshoot-connection.md) |Se describe cómo identificar y resolver un error 40613: "Database &lt;x&gt; on server &lt;y&gt; is not currently available. Vuelva a intentar la conexión más tarde.) |
| 6 |[Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas.](sql-database-develop-error-messages.md) |Proporciona información sobre los códigos de error de SQL de las aplicaciones cliente de Base de datos SQL, como errores de conexión de base de datos más comunes, problemas de copia de la base de datos y errores generales. |
| 7 |[Guía de rendimiento de SQL Database de Azure para bases de datos independientes](sql-database-performance-guidance.md). |Se proporcionan instrucciones para ayudarle a determinar qué nivel de servicio es adecuado para su aplicación. También se incluyen recomendaciones para optimizar la aplicación y así sacar el máximo provecho de su instancia de Base de datos SQL de Azure. |
| 8 |[Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) |Proporciona vínculos a ejemplos de código para diversas tecnologías que puede usar para conectarse a Base de datos SQL de Azure e interactuar con este servicio. |
| 9 |Actualizar a la página de la versión&12; de Azure SQL Database ([Azure portal](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) |Se proporcionan instrucciones para actualizar los servidores y bases de datos de Base de datos SQL de Azure V11 existentes a Base de datos SQL de Azure V12 mediante el Portal de Azure o PowerShell. |

## <a name="next-steps"></a>Pasos siguientes
* [Evaluación y mejora del rendimiento de la base de datos con la Base de datos SQL de Azure](sql-database-troubleshoot-performance.md)
* [Tareas administrativas comunes en Base de datos SQL de Azure](sql-database-troubleshoot-permissions.md)
* [Buscar en la documentación de Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Ver las últimas actualizaciones del servicio Base de datos SQL](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* [Orientación general sobre reintentos](../best-practices-retry-general.md)
* [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md)
* [La ruta aprendizaje para el uso de Base de datos SQL de Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
* [La ruta de aprendizaje para el uso de herramientas y características de bases de datos elásticas](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 




<!--HONumber=Dec16_HO2-->


