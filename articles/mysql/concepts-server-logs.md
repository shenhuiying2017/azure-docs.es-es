---
title: Registros de servidor para Azure Database for MySQL | Microsoft Docs
description: "Describe los registros disponibles en Azure Database for MySQL y los parámetros disponibles para habilitar diferentes niveles de registro."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 13b30df82c1a6c4c45a621a1f7a40148a55a7648
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Registros de servidor en Azure Database for MySQL
En Azure Database for MySQL, el registro de consultas lentas está disponible para los usuarios. No se admite el acceso al registro de transacciones. El registro de consultas lentas puede utilizarse para identificar cuellos de botella que afectan al rendimiento a fin de solucionar el problema. 

Para obtener más información sobre el registro de consultas lentas de MySQL, vea la [sección de registro de consultas lentas](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) del manual de referencia.

## <a name="access-server-logs"></a>Acceso a registros de servidor
Puede enumerar y descargar los registros de servidor de Azure Database for MySQL mediante Azure Portal y la CLI de Azure.

En Azure Portal, seleccione el servidor de Azure Database for MySQL. En el encabezado **Supervisión**, seleccione la página **Registros de servidor**.

Para obtener más información sobre la CLI de Azure, consulte [Configuración y acceso a los registros del servidor con la CLI de Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Retención de registros
Los registros están disponibles hasta siete días después de su creación. Si el tamaño total de los registros disponibles supera los 7,5 GB, se eliminan los archivos más antiguos hasta que haya espacio disponible. 

Los registros se rotan cada 24 horas o 7,5 GB, o que ocurra primero.


## <a name="configure-logging"></a>registro 
De forma predeterminada, el registro de consultas lentas está deshabilitado. Para habilitarlo, establezca low_query_log en ON.

Otros parámetros que se pueden ajustar son los siguientes:

- **long_query_time**: si una consulta tarda más que long_query_time (en segundos), esa consulta se registra. El valor predeterminado es 10 segundos.
- **log_slow_admin_statements**: si ON incluye instrucciones administrativas como ALTER_TABLE y ANALYZE_TABLE en las instrucciones escritas en slow_query_log.
- **log_queries_not_using_indexes**: determina si las consultas que no utilizan índices se registran en slow_query_log.
- **log_throttle_queries_not_using_indexes**: este parámetro limita el número de consultas no de índice que se pueden escribir en el registro de consultas lentas. Este parámetro surte efecto cuando log_queries_not_using_indexes está configurado en ON.

Consulte la [documentación rel registro de consultas lentas](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) de MySQL para obtener una descripción completa de los parámetros de registro de consultas lentas.

## <a name="next-steps"></a>Pasos siguientes
- [How to configure and access server logs from the Azure CLI](howto-configure-server-logs-in-cli.md) (Configuración y acceso de registros de servidor desde la CLI de Azure).
