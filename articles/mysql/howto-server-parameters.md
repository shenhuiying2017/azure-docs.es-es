---
title: "Cómo configurar parámetros del servidor en Azure Database para MySQL | Microsoft Docs"
description: "En este artículo se explica cómo configurar parámetros del servidor en Azure Database para MySQL mediante el Azure Portal."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Cómo configurar parámetros del servidor en Azure Database para MySQL mediante el Azure Portal

Azure Database para MySQL admite la configuración de algunos parámetros del servidor. En este artículo se explica cómo configurar estos parámetros mediante el Azure Portal y se indican los parámetros admitidos, los valores predeterminados y el intervalo de valores válidos. No es posible ajustar todos los parámetros del servidor. Solo se admiten los indicados aquí.

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Ir a la hoja Parámetros del servidor de Azure Portal

Inicie sesión en Azure Portal y luego haga clic en el nombre del servidor de Azure Database para MySQL. En la sección **CONFIGURACIÓN**, haga clic en **Parámetros del servidor** para abrir la hoja Parámetros del servidor de Azure Database para MySQL.

![Hoja Parámetros del servidor de Azure Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parámetros configurables del servidor

En la tabla siguiente se indican los parámetros del servidor admitidos actualmente. Los parámetros se pueden configurar conforme a los requisitos de la aplicación.

> [!div class="mx-tableFixed"]
|Nombre de parámetro|Valor predeterminado|Intervalo|Descripción|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|Controla cuántos microsegundos espera la confirmación de registro binario antes de sincronizar el archivo de registro binario en el disco.|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|Número máximo de transacciones que se espera antes de anular el retraso actual según la especificación de binlog-group-commit-sync-delay.|
|character_set_server|LATIN1|BIG5, UTF8MB4, etc.|Use charset_name como juego de caracteres predeterminado del servidor.|
|div_precision_increment|4|0-30|Número de dígitos por el que se va a aumentar la escala del resultado de las operaciones de división.|
|group_concat_max_len|1024|4-16777216|Longitud máxima permitida del resultado en bytes para GROUP_CONCAT().|
|innodb_adaptive_hash_index|ACTIVAR|ON, OFF|Si están habilitados o deshabilitados los índices de hash adaptables de InnoDB.|
|innodb_lock_wait_timeout|50|1-3600|Período de tiempo en segundos que una transacción de InnoDB espera un bloqueo de fila antes de desistir.|
|interactive_timeout|1.800|10-1800|Número de segundos que espera el servidor alguna actividad en una conexión interactiva antes de cerrarla.|
|log_bin_trust_function_creators|Apagado|ON, OFF|Esta variable se aplica cuando el registro binario está habilitado. Controla si se puede confiar en que los creadores de funciones almacenadas no creen funciones almacenadas que ocasionen la escritura de eventos no seguros en el registro binario.|
|log_queries_not_using_indexes|Apagado|ON, OFF|Registra aquellas consultas que se espera que recuperen todas las filas en el registro de consultas lento.|
|log_slow_admin_statements|Apagado|ON, OFF|Incluye instrucciones administrativas lentas en las instrucciones escritas en el registro de consultas lento.|
|log_throttle_queries_not_using_indexes|0|0-4294967295|Limita el número de estas consultas por minuto que se puede escribir en el registro de consultas lento.|
|long_query_time|10|1-1E+100|Si una consulta tarda más de este número de segundos, el servidor incrementa la variable de estado Slow_queries.|
|max_allowed_packet|536870912|1024-1073741824|Tamaño máximo de un paquete o de cualquier cadena generada o intermedia, o cualquier parámetro enviado por la función de API de C mysql_stmt_send_long_data().|
|min_examined_row_limit|0|0-18446744073709551615|Registra aquellas consultas con más filas del número configurado en el registro de consultas lento. |
|server_id|3293747068|1000-4294967295|Identificador de servidor que se usa en la replicación para proporcionar a cada maestro y subordinado una identidad única.|
|slave_net_timeout|60|30-3600|Número de segundos de espera de más datos del maestro antes de que el subordinado considere interrumpida la conexión, anule la lectura e intente volver a conectar.|
|slow_query_log|Apagado|ON, OFF|Habilita o deshabilita el registro de consultas lento.|
|sql_mode|0 seleccionado|ALLOW_INVALID_DATES, IGNORE_SPACE, etc.|Modo de SQL Server actual.|
|time_zone|SYSTEM|Ejemplos: -8:00, +05:30|Zona horaria del servidor.|
|wait_timeout|120|60-240|Número de segundos que espera el servidor alguna actividad en una conexión no interactiva antes de cerrarla.|

## <a name="next-steps"></a>Pasos siguientes
- [Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md)

