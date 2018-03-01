---
title: 'Recomendaciones de rendimiento: Azure SQL Database | Microsoft Docs'
description: Azure SQL Database ofrece recomendaciones para las bases de datos SQL que pueden mejorar el rendimiento actual de las consultas.
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: 
ms.openlocfilehash: b3cd5f2138f4d16a1a1590b025d020410ebcce3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Recomendaciones de rendimiento para SQL Database

Azure SQL Database comprende su aplicación y se adapta a ella. Proporciona recomendaciones personalizadas que permiten maximizar el rendimiento de las bases de datos SQL. SQL Database evalúa y analiza continuamente el historial de uso de las bases de datos SQL. Las recomendaciones que se proporcionan se basan en patrones de carga de trabajo únicos de la base de datos y ayudan a mejorar su rendimiento.

> [!TIP]
> [Ajuste automático](sql-database-automatic-tuning.md) es el método recomendado de realizar el ajuste de rendimiento. [Intelligent Insights](sql-database-intelligent-insights.md) es el método recomendado de supervisar el rendimiento. 
>

## <a name="create-index-recommendations"></a>Recomendaciones Crear índice
SQL Database supervisa continuamente las consultas que se ejecutan e identifica los índices que podrían mejorar el rendimiento. Después de que se sabe con bastante confianza que falta un índice, se crea una nueva recomendación **Crear índice**.

 Para generar confianza, Azure SQL Database calcula la ganancia de rendimiento que el índice aportaría a lo largo del tiempo. Según la ganancia de rendimiento estimada, las recomendaciones se clasifican como alta, media o baja. 

Los índices creados mediante recomendaciones se marcan siempre como índices auto_created. Puede ver qué índices son auto_created en la vista sys.indexes. Los índices creados de forma automática no bloquean los comandos ALTER/RENAME. 

Si intenta descartar la columna que tiene un índice creado automáticamente por encima, el comando se pasa. El índice creado automáticamente también se descarta con el comando. Los índices normales bloquean el comando ALTER/RENAME en las columnas que están indexadas.

Después de aplicar la recomendación Crear índice, Azure SQL Database compara el rendimiento de las consultas con el de línea de base. Si el nuevo índice mejoró el rendimiento, la recomendación está marcada como correcta y el informe del impacto está disponible. Si el índice no mejoró el rendimiento, se revierte de forma automática. SQL Database emplea este proceso para asegurarse de que las recomendaciones mejoran el rendimiento de la base de datos.

Cualquier recomendación **Crear índice** tiene una directiva de rechazo que no permite aplicar la recomendación si la utilización de recursos de una base de datos o de un grupo es elevada. La directiva de rechazo tiene en cuenta la CPU, la E/S de datos, la E/S de registros y el almacenamiento disponible. 

Si los valores de estos elementos son superiores al 80 % en los 30 minutos anteriores, se pospone la recomendación Crear índice. Si el almacenamiento disponible va a ser inferior al 10 % una vez creado el índice, la recomendación entra en un estado de error. Si al cabo de un par de días el ajuste automático considera que el índice resultaría beneficioso, el proceso comienza de nuevo. 

Este proceso se repite hasta que haya suficiente almacenamiento disponible para crear un índice o hasta que el índice ya no se considere beneficioso.

## <a name="drop-index-recommendations"></a>Recomendaciones Quitar índice
Además de detectar índices que faltan, SQL Database analiza continuamente el rendimiento de los índices existentes. Si no se usa un índice, Azure SQL Database recomienza descartarlo. Se recomienda quitar un índice en dos casos:
* El índice es un duplicado de otro (misma columna indexada e incluida, esquema de partición y filtros).
* El índice no se ha usado durante un período prolongado (93 días).

Las recomendaciones Quitar índice también llevan a cabo la comprobación después de la implementación. Si mejora el rendimiento, está disponible el informe del impacto. Si se degrada el rendimiento, se revierte la recomendación.


## <a name="parameterize-queries-recommendations"></a>Recomendaciones para parametrizar consultas
Las recomendaciones de *parametrización de consultas* aparecen cuando tiene una o varias consultas que se vuelven a compilar continuamente, pero que terminan con el mismo plan de ejecución de consultas. Esta condición crea una oportunidad de aplicar la parametrización forzada. A su vez, la parametrización forzada permite que se almacenen en caché planes de consulta y se vuelvan a usar en el futuro, lo que mejora el rendimiento y reduce el uso de recursos. 

Inicialmente, todas las consultas enviadas a SQL Server deben compilarse para generar un plan de ejecución. Cada plan generado se agrega a la caché de planes. Las sucesivas ejecuciones de la misma consulta pueden volver a usar este plan desde la caché, lo que elimina la necesidad de compilación adicional. 

Las consultas con valores sin parámetros pueden llevar a una sobrecarga en el rendimiento porque el plan de ejecución se vuelve a compilar cada vez que los valores sin parámetros son diferentes. En muchos casos, las mismas consultas con distintos valores de parámetro generan los mismos planes de ejecución. Estos planes, sin embargo, se agregan aún por separado a la caché de planes. 

El proceso de volver a compilar planes de ejecución usa recursos de base de datos, aumenta el tiempo de duración de la consulta y desborda la caché de planes. Estos eventos, a su vez, hacen que los planes se expulsen de la caché. Este comportamiento de SQL Server puede modificarse estableciendo la opción de parametrización forzada en la base de datos. 

Para ayudarlo a estimar el impacto de esta recomendación, se le proporciona una comparación entre el uso real y el previsto de la CPU (como si se aplicase la recomendación). Esta recomendación puede ayudarle a obtener ahorros de CPU. También puede ayudarle a reducir la duración de la consulta y la sobrecarga de la caché de planes, lo que significa que varios de los planes pueden permanecer en la caché y volverse a utilizar. Para aplicar esta recomendación rápidamente, seleccione el comando **Aplicar**. 

Después de aplicar esta recomendación, se habilita la parametrización forzada en cuestión de minutos en la base de datos. Se inicia el proceso de supervisión, que tiene una validez de aproximadamente 24 horas. Después de este período, puede ver el informe de validación. Este informe muestra el uso de CPU de la base de datos 24 horas antes y después de haber aplicado la recomendación. SQL Database Advisor cuenta con un mecanismo de seguridad que revierte automáticamente la recomendación aplicada en caso de detectarse una regresión del rendimiento.

## <a name="fix-schema-issues-recommendations-preview"></a>Recomendaciones para solucionar problemas del esquema (vista previa)

> [!IMPORTANT]
> Microsoft está dejando de usar las recomendaciones de corrección de problemas de esquema. Se recomienda usar [Intelligent Insights](sql-database-intelligent-insights.md) para supervisar los problemas de rendimiento de la base de datos, incluidos los problemas de esquema que anteriormente trataban las recomendaciones de corrección de problemas de esquema.
> 

Las recomendaciones de **corrección de problemas de esquema** aparecen cuando el servicio SQL Database advierte alguna anomalía en el número de errores de SQL relacionados con el esquema que se producen en la base de datos SQL. Esta recomendación suele aparecer cuando la base de datos encuentra varios errores relacionados con el esquema (nombre de columna no válido, nombre de objeto no válido, etc.) en el curso de una hora.

Los "problemas de esquema" son una clase de errores de sintaxis de SQL Server. Se producen cuando la definición de la consulta SQL y la definición del esquema de base de datos no concuerdan. Por ejemplo, puede que en la tabla de destino falte una de las columnas que espera la consulta o viceversa. 

La recomendación de corrección de problemas de esquema aparece cuando el servicio Azure SQL Database advierte alguna anomalía en el número de errores SQL relacionados con el esquema que se producen en la base de datos SQL. En la tabla siguiente se muestran los errores relacionados con los problemas del esquema:

| Código de error SQL | Message |
| --- | --- |
| 201 |El procedimiento o la función '*' espera parámetros '*', que no se han proporcionado. |
| 207 |Nombre de columna '*' no válido. |
| 208 |Nombre de objeto '*' no válido. |
| 213 |El nombre de columna o los valores especificados no corresponden a la definición de la tabla. |
| 2812 |No se pudo encontrar el procedimiento almacenado '*'. |
| 8144 |La función o el procedimiento * tiene demasiados argumentos. |

## <a name="next-steps"></a>pasos siguientes
Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. SQL Database Advisor sigue supervisando y ofreciendo recomendaciones que pueden mejorar el rendimiento de la base de datos. 

* Para más información sobre cómo realizar un ajuste automático de los índices de base de datos y consultar los planes de ejecución, consulte [Ajuste automático de Azure SQL Database](sql-database-automatic-tuning.md).
* Para más información sobre cómo supervisar de forma automática el rendimiento de la base de datos con diagnósticos automatizados y análisis de causa principal de problemas de rendimiento, consulte [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Para más información sobre cómo usar las recomendaciones de rendimiento de Azure Portal, consulte [Recomendaciones de rendimiento en Azure Portal](sql-database-advisor-portal.md).
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.


