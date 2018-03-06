---
title: "Uso de sys_schema para operaciones de optimización del rendimiento y mantenimiento de bases de datos en Azure Database for MySQL"
description: "En este artículo se describe cómo utilizar sys_schema para detectar problemas de rendimiento y realizar el mantenimiento de bases de datos en Azure Database for MySQL."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2e5b6b859df06d686a97fc1b134da8d66df6783e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Uso de sys_schema para operaciones de optimización del rendimiento y mantenimiento de bases de datos en Azure Database for MySQL

La característica performance_schema de MySQL, disponible por primera vez en MySQL 5.5, proporciona instrumentación para muchos recursos importantes del servidor como la asignación de memoria, programas almacenados, bloqueo de metadatos, etc. No obstante, performance_schema contiene más de 80 tablas y obtener la información necesaria a menudo requiere unir las tablas de performance_schema, así como las tablas procedentes de information_schema. Tomando como base performance_schema e information_schema, la característica sys_schema proporciona un conjunto eficaz de [vistas fáciles de usar](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) en una base de datos de solo lectura y está completamente habilitada en Azure Database for MySQL versión 5.7.

![vistas de sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Hay 52 vistas en sys_schema y cada vista tiene uno de los siguientes prefijos:

- Host_summary o E/S: latencias relacionadas con E/S.
- InnoDB: estado y bloqueos del búfer InnoDB.
- Memory: uso de la memoria por parte del host y de los usuarios.
- Schema: información relacionada con los esquemas como el incremento automático, los índices, etc.
- Statement: información sobre las instrucciones SQL. Puede tratarse de una instrucción que dio como resultado un recorrido de tabla completo o un tiempo de consulta largo.
- User: los recursos que consumen y agrupan los usuarios. Algunos ejemplos son: operaciones de E/S de archivo, conexiones y memoria.
- Wait: eventos de espera agrupados por host o usuario.

Echemos un vistazo a algunos patrones de uso habituales de sys_schema. Para empezar, vamos a agrupar los patrones de uso en dos categorías: **optimización del rendimiento** y **mantenimiento de base de datos**.

## <a name="performance-tuning"></a>Optimización del rendimiento

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

E/S es la operación más costosa en la base de datos. Podemos averiguar el promedio de latencia de E/S consultando la vista *sys.user_summary_by_file_io*. Con el valor predeterminado de 125 GB de almacenamiento aprovisionado, la latencia de E/S es aproximadamente de 15 segundos.

![latencia de E/S: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Dado que Azure Database for MySQL escala E/S en relación al almacenamiento, después de aumentar mi almacenamiento aprovisionado a 1 TB, la latencia de E/S se reduce a 571 ms, lo cual representa un aumento del rendimiento hasta 26 veces superior.

![latencia de E/S: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

A pesar de efectuar un planeamiento cuidadoso, puede que muchas consultas aún resulten en recorridos de tabla completos. Para obtener información adicional acerca de los tipos de índices y cómo optimizarlos, puede consultar este artículo: [Solución de problemas relacionados con el rendimiento de consultas](./howto-troubleshoot-query-performance.md). Los recorridos de tabla completos requieren un uso intensivo de los recursos y reducen el rendimiento de la base de datos. La forma más rápida de buscar tablas con recorridos de tabla completos es consultar la vista *sys.schema_tables_with_full_table_scans*.

![recorridos de tabla completos](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Para solucionar los problemas de rendimiento de bases de datos, puede resultar útil identificar los eventos que se producen dentro de la base de datos y mediante la vista *sys.user_summary_by_statement_type* puede lograrlo.

![resumen por instrucción](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

En este ejemplo Azure Database for MySQL empleó 53 minutos en vaciar el registro de consultas slog 44579 veces. Eso es mucho tiempo y muchas E/S. Puede reducir esta actividad si deshabilita el registro de consultas lentas o reduce la frecuencia de este registro en Azure Portal.

## <a name="database-maintenance"></a>Mantenimiento de base de datos

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

El grupo de búferes InnoDB reside en la memoria y es el principal mecanismo de memoria caché entre el sistema de administración de bases de datos y el almacenamiento. El tamaño del grupo de búferes InnoDB está vinculado al nivel de rendimiento y no se puede cambiar a menos que se elija una SKU de producto diferente. Al igual que con la memoria del sistema operativo, se intercambiaron páginas antiguas para dejar espacio a los datos más recientes. Para averiguar qué tablas consumen la mayor parte de la memoria del grupo de búferes InnoDB, puede consultar la vista *sys.innodb_buffer_stats_by_table*.

![Estado de los búferes InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

En el gráfico anterior, se puede ver que aparte de las tablas del sistema y las vistas, cada tabla de la base de datos mysqldatabase033, que hospeda uno de mis sitios de WordPress, ocupa 16 KB, o 1 página, de datos en memoria.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Los índices son unas herramientas estupendas para mejorar el rendimiento de lectura, pero suponen costos adicionales por las inserciones y el almacenamiento. *Sys.schema_unused_indexes* and *sys.schema_redundant_indexes* proporcionan información sobre los índices sin usar o los duplicados.

![índices sin usar](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![índices redundantes](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusión

En resumen, sys_schema es una herramienta excelente para la optimización de rendimiento y el mantenimiento de base de datos. Asegúrese de sacarle el máximo partido a esta característica en Azure Database for MySQL. 

## <a name="next-steps"></a>pasos siguientes
- Para buscar respuestas de otros usuarios a sus preguntas o publicar una nueva pregunta o respuesta, visite el [foro de MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
