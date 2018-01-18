---
title: 'Recomendaciones de rendimiento: Azure SQL Database | Microsoft Docs'
description: Azure SQL Database ofrece recomendaciones para las bases de datos SQL que pueden mejorar el rendimiento actual de las consultas.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: ea1069d4ec29ad66562a6798a8b13998d0d2ef89
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="performance-recommendations"></a>Recomendaciones de rendimiento

Azure SQL Database aprende y se adapta a su aplicación, y proporciona recomendaciones personalizadas que le permiten maximizar el rendimiento de las bases de datos SQL. Se evalúa continuamente el rendimiento mediante el análisis de su historial de uso de SQL Database. Las recomendaciones que se proporcionan se basan en un patrón de carga de trabajo único de la base de datos y ayudan a mejorar su rendimiento.

> [!TIP]
> [Ajuste automático](sql-database-automatic-tuning.md) es la manera recomendada de realizar el ajuste de rendimiento. [Intelligent Insights](sql-database-intelligent-insights.md) es la manera recomendada de supervisar el rendimiento. 
>

## <a name="create-index-recommendations"></a>Recomendaciones Crear índice
Azure SQL Database supervisa continuamente las consultas que se ejecutan e identifica los índices que podrían mejorar el rendimiento. Una vez que se crea suficiente confianza de que falta un determinado índice, se crea una recomendación **Crear índice**. Azure SQL Database genera confianza calculando la mejora de rendimiento que el índice aportaría a lo largo del tiempo. Según la ganancia de rendimiento estimada, una recomendación se clasifica como Alta, Media o Baja. 

Los índices creados mediante recomendaciones se marcan siempre como índices auto_created. Puede ver qué índices son auto_created en la vista sys.indexes. Los índices creados de forma automática no bloquean los comandos ALTER/RENAME. Si intenta quitar la columna en la que se ha creado un índice de forma automática, el comando pasa y también se quita el índice creado automáticamente con el comando. Los índices normales bloquearían el comando ALTER/RENAME en las columnas que están indexadas.

Una vez aplicada la recomendación Crear índice, Azure SQL Database comparará el rendimiento de las consultas con el de línea de base. Si el nuevo índice aportó mejoras de rendimiento, la recomendación se marcará como correcta y el informe de impacto estará disponible. Si no aportó ventajas, se revertirá automáticamente. De esta forma, Azure SQL Database se asegura de que usar recomendaciones solo reporte mejoras para el rendimiento de la base de datos.

Cualquier recomendación **Crear índice** tiene una directiva de rechazo que no permitirá aplicar la recomendación si la utilización de recursos de una base de datos o de un grupo es elevada. La directiva de rechazo tiene en cuenta la CPU, la E/S de datos, la E/S de registros y el almacenamiento disponible. Si la CPU, la E/S de datos o la E/S de registros supera el 80 % en los últimos treinta minutos, la creación del índice se aplazará. Si almacenamiento disponible es inferior al 10 % después de crear el índice, la recomendación adoptará un estado de error. Si después de un par de días, el ajuste automático considera que el índice resultaría beneficioso, el proceso comenzará de nuevo. Este proceso se repetirá hasta que haya suficiente espacio disponible para crear un índice o hasta que el índice ya no se considere beneficioso.

## <a name="drop-index-recommendations"></a>Recomendaciones Quitar índice
Además de detectar un índice que falta, Azure SQL Database analiza continuamente el rendimiento de los índices existentes. Si no se usa un índice, Azure SQL Database recomendará quitarlo. Se recomienda quitar un índice en dos casos:
* El índice es un duplicado de otro (misma columna indexada e incluida, esquema de partición y filtros)
* El índice lleva sin usarse un período prolongado (93 días)

Las recomendaciones Quitar índice también llevan a cabo la comprobación después de la implementación. Si se ha mejorado el rendimiento, el informe de impacto estará disponible. Si se detecta una degradación del rendimiento, se revertirá la recomendación.


## <a name="parameterize-queries-recommendations"></a>Recomendaciones para parametrizar consultas
Las recomendaciones de **parametrización de consultas** aparecen cuando tiene una o varias consultas que se vuelven a compilar continuamente, pero que terminan con el mismo plan de ejecución de consultas. Esta condición abre la posibilidad de aplicar la parametrización forzada, que permite que los planes de consulta se almacenen en caché y se reutilicen en el futuro para mejorar el rendimiento y reducir el uso de recursos. 

Inicialmente, todas las consultas enviadas a SQL Server deben compilarse para generar un plan de ejecución. Cada plan generado se agrega a la caché de planes. Además, las ejecuciones posteriores de la misma consulta pueden volver a usar este plan desde la caché, lo que hace innecesarias más compilaciones. 

Las aplicaciones que envían consultas que incluyan valores sin parámetros pueden menoscabar el rendimiento, ya que se vuelve a compilar el plan de ejecución con cada consulta de este tipo. En numerosos casos, las mismas consultas con valores de parámetro diferentes generan los mismos planes de ejecución, pero estos se siguen agregando por separado a la caché de planes. Los planes de ejecución de nuevas compilaciones utilizan recursos de base de datos, aumentan la duración de las consultas y desbordan la caché de planes, lo que provoca que estos se retiren de la caché. Este comportamiento de SQL Server puede modificarse estableciendo la opción de parametrización forzada en la base de datos. 

Para ayudarlo a estimar el impacto de esta recomendación, se le proporciona una comparación entre el uso real y el previsto de la CPU (como si se aplicase la recomendación). Además de los ahorros en uso de la CPU, se reduce la duración de las compilaciones de las consultas. También habrá mucha menos sobrecarga en la caché de planes, con lo que la mayoría de los planes permanecerán en la caché y podrán reutilizarse. Puede aplicar esta recomendación de forma rápida y sencilla haciendo clic en el comando **Aplicar**. 

Cuando lo haga, se habilitará en cuestión de minutos la opción de parametrización forzada en la base de datos y se iniciará el proceso de supervisión, que dura aproximadamente 24 horas. Cuando transcurra este periodo, podrá ver el informe de validación que muestra el uso de la CPU de la base de datos 24 horas antes y después de haber aplicado la recomendación. SQL Database Advisor tiene un mecanismo de seguridad que revierte automáticamente la recomendación aplicada en caso de detectarse una regresión del rendimiento.

## <a name="fix-schema-issues-recommendations-preview"></a>Recomendaciones para solucionar problemas del esquema (vista previa)

> [!IMPORTANT]
> Microsoft está dejando de usar las recomendaciones sobre la solución de problemas del esquema. Debería empezar a usar [Intelligent Insights](sql-database-intelligent-insights.md) para la supervisión automática de los problemas de rendimiento de la base de datos, que incluyen problemas del esquema que previamente se abordaban en las recomendaciones sobre la solución de problemas del esquema.
> 

Las recomendaciones sobre la **solución de problemas del esquema** aparecen cuando el servicio SQL Database advierte alguna anomalía en el número de errores de SQL relacionados con el esquema que se producen en Azure SQL Database. Esta recomendación suele aparecer cuando la base de datos encuentra varios errores relacionados con el esquema (nombre de columna no válido, nombre de objeto no válido, etc.) en el curso de una hora.

Los problemas del esquema constituyen una clase de errores de sintaxis en SQL Server que se producen cuando no están alineadas la definición de la consulta SQL y la del esquema de base de datos. Por ejemplo, en la tabla de destino falta una de las columnas que espera la consulta o viceversa. 

La recomendación de solución de problema del esquema aparece cuando el servicio Azure SQL Database advierte alguna anomalía en el número de errores SQL relacionados con el esquema que se producen en Azure SQL Database. En la tabla siguiente se muestran los errores relacionados con los problemas del esquema:

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

* Vea [Ajuste automático de Azure SQL Database](sql-database-automatic-tuning.md) para realizar un ajuste automático de los índices de base de datos y consultar los planes de ejecución.
* Vea [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) para supervisar de forma automática el rendimiento de la base de datos con diagnósticos automatizados y análisis de causa principal de problemas de rendimiento.
* Consulte [Recomendaciones de rendimiento en Azure Portal](sql-database-advisor-portal.md) para ver cómo usar recomendaciones de rendimiento en Azure Portal.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.


