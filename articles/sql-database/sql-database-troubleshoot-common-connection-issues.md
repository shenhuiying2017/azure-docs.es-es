---
title: "Solución de problemas de conexión comunes relacionados con Azure SQL Database"
description: "Pasos para identificar y resolver errores de conexión comunes para Azure SQL Database."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: 1d756aa023ae143608acc988ddd0ae8acee1a113
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solución de problemas de conexión de Azure SQL Database
Cuando la conexión a Azure SQL Database no se logra establecer, se reciben [mensajes de error](sql-database-develop-error-messages.md). Este artículo es un tema centralizado que ayuda a la solución de problemas de conectividad de Azure SQL Database. Se presentan [las causas habituales](#cause) de los problemas de conexión, se recomienda [una herramienta de solución de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que lo ayuda a identificar el problema y se proporcionan pasos de solución de problemas para resolver [errores transitorios](#troubleshoot-transient-errors) y [errores persistentes o no transitorios](#troubleshoot-persistent-errors). 

Si encuentra problemas de conexión, pruebe los pasos de solución de problemas que se describen en este artículo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
Los problemas de conexión pueden tener alguna de las siguientes causas:

* Error al aplicar los procedimientos recomendados y las directrices de diseño durante el proceso de diseño de aplicaciones.  Para comenzar, consulte [Información general de desarrollo de SQL Database](sql-database-develop-overview.md) .
* Reconfiguración de Azure SQL Database
* Configuración de firewall
* Tiempo de espera de conexión agotado
* Información de inicio de sesión incorrecta
* Límite máximo alcanzado en algunos recursos de Azure SQL Database

Por lo general, los problemas de conexión de Azure SQL Database se pueden clasificar en los siguientes:

* [Errores transitorios (corta duración o intermitentes)](#troubleshoot-transient-errors)
* [Errores persistentes o no transitorios (errores que se repiten con frecuencia)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Pruebe el solucionador de problemas para los problemas de conectividad de Azure SQL Database
Si se produce un error de conexión específico, pruebe [esta herramienta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que le ayudará a identificar y resolver el problema.

## <a name="troubleshoot-transient-errors"></a>Solución de problemas de errores transitorios

Cuando una aplicación se conecte a una base de datos SQL de Azure, recibirá el mensaje de error siguiente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Este mensaje de error suele ser transitorio (breve).
> 
> 

Este error se produce cuando la base de datos de Azure se está moviendo (o reconfigurando) y la aplicación pierde la conexión con la base de datos SQL. Los eventos de reconfiguración de la base de datos SQL se producen debido a un evento planeado (por ejemplo, una actualización de software) o a un evento no planeado (por ejemplo, el bloqueo de un proceso o un equilibrio de carga). La mayoría de los eventos de reconfiguración son generalmente de corta duración y se completarán en menos de 60 segundos. Sin embargo, ocasionalmente estos eventos pueden tardar más tiempo de finalizar, por ejemplo, cuando una transacción grande produce una recuperación de larga duración.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Pasos para resolver los problemas de conectividad transitorios

1. Compruebe el [panel de Estado de Microsoft Azure](https://azure.microsoft.com/status) para comprobar si hay interrupciones conocidas que se hayan producido durante el tiempo en el que la aplicación informó de los errores.
2. Para las aplicaciones que se conectan a un servicio en la nube, como Azure SQL Database, se deben prever eventos periódicos de reconfiguración e implementación de la lógica de reintento para gestionar estos errores en lugar de mostrarlos como errores de la aplicación. Consulte la sección [Errores transitorios](sql-database-connectivity-issues.md), así como los procedimientos recomendados e instrucciones de diseño en [Información general de desarrollo de SQL Database](sql-database-develop-overview.md), donde encontrará más información e instrucciones para las estrategias generales de reintento. Después, para información específica, consulte ejemplos de código en [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md) .
3. Conforme una base de datos se acerca a sus límites de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
4. Si los problemas de conectividad continúan, si el tiempo de detección del error por parte de la aplicación supera los 60 segundos o si el error se repite varias veces en un día determinado, realice una solicitud de soporte técnico a Azure; para ello, seleccione **Obtener soporte** en el sitio [Soporte técnico de Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Solución de problemas de los errores persistentes
Si la aplicación no se puede conectar a Azure SQL Database de forma persistente, normalmente indica un problema con uno de los siguientes elementos:

* Configuración del firewall. Azure SQL Database o el firewall del cliente están bloqueando las conexiones de Azure SQL Database.
* Reconfiguración de la red en el lado cliente: por ejemplo, un nuevo servidor de proxy o dirección IP.
* Error de usuario: por ejemplo, escribió incorrectamente los parámetros de conexión, como el nombre del servidor en la cadena de conexión.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Pasos para resolver los problemas de conectividad persistentes
1. Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente. Con fines de prueba temporales, configure una regla de firewall mediante 0.0.0.0 como intervalo de direcciones IP inicial y 255.255.255.255 como intervalo de dirección IP final. Se abrirá el servidor a todas las direcciones IP. Si se resuelve el problema de conectividad, quite esta regla y cree una regla de firewall para una dirección IP o intervalo de direcciones apropiadamente limitados. 
2. En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes. Consulte [Configure the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) (Configuración del Firewall de Windows para permitir el acceso de SQL Server) y [Puertos y protocolos requeridos para la identidad híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) para obtener información sobre punteros relacionados con los puertos adicionales que necesite abrir para la autenticación de Azure Active Directory.
3. Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de los problemas de conectividad](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.

## <a name="next-steps"></a>Pasos siguientes
* [Evaluación y mejora del rendimiento de la base de datos con Azure SQL Database](sql-database-troubleshoot-performance.md)
* [Buscar en la documentación de Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Ver las últimas actualizaciones del servicio SQL Database](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Información general de desarrollo de SQL Database](sql-database-develop-overview.md)
* [Orientación general sobre reintentos](../best-practices-retry-general.md)
* [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md)

