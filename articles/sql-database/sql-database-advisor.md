---
title: 'Recomendaciones de ajuste del rendimiento de consultas: Azure SQL Database | Microsoft Docs'
description: El Asesor de Base de datos SQL de Azure ofrece recomendaciones para las bases de datos SQL existentes que pueden mejorar el rendimiento actual de las consultas.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: a8d0b08abc7e3c688f9ab79499b3459b33f06848


---
# <a name="sql-database-advisor"></a>Asesor de Base de datos SQL

Azure SQL Database aprende y se adapta a su aplicación, y proporciona recomendaciones personalizadas que le permiten maximizar el rendimiento de las bases de datos SQL. SQL Database Advisor ofrece recomendaciones para crear y quitar índices, parametrizar consultas y solucionar problemas del esquema. El asesor evalúa el rendimiento mediante el análisis del historial de uso de la base de datos SQL. Se recomienda usar los índices que sean más adecuados para ejecutar la carga de trabajo habitual de su base de datos. 

Las recomendaciones siguientes están disponibles para los servidores de Azure SQL Database. En estos momentos, puede configurar que las recomendaciones de creación y eliminación de índices se apliquen automáticamente. Para más información, consulte la sección de [administración automática de índices](sql-database-advisor-portal.md#enable-automatic-index-management).

## <a name="create-index-recommendations"></a>Recomendaciones para crear índice
**creación de índices** aparecen cuando el servicio Base de datos SQL detecta que falta un índice que, si se crea, puede beneficiar a la carga de trabajo de las bases de datos (solo en el caso de los índices no agrupados en clústeres).

## <a name="drop-index-recommendations"></a>Recomendaciones para quitar índice
**eliminación de índices** aparecen cuando el servicio Base de datos SQL detecta índices duplicados (funcionalidad actualmente en versión preliminar; solo aplicable a índices duplicados).

## <a name="parameterize-queries-recommendations"></a>Recomendaciones para parametrizar consultas
Las recomendaciones de **parametrización de consultas** aparecen cuando tiene una o varias consultas que se vuelven a compilar continuamente, pero que terminan con el mismo plan de ejecución de consultas. Esta condición abre la posibilidad de aplicar la parametrización forzada, que permite que los planes de consulta se almacenen en caché y se reutilicen en el futuro para mejorar el rendimiento y reducir el uso de recursos. 

Inicialmente, todas las consultas enviadas a SQL Server deben compilarse para generar un plan de ejecución. Cada plan generado se agrega a la caché de planes. Además, las ejecuciones posteriores de la misma consulta pueden volver a usar este plan desde la caché, lo que hace innecesarias más compilaciones. 

Las aplicaciones que envían consultas que incluyan valores sin parámetros pueden menoscabar el rendimiento, ya que se vuelve a compilar el plan de ejecución con cada consulta de este tipo. En numerosos casos, las mismas consultas con valores de parámetro diferentes generan los mismos planes de ejecución, pero estos se siguen agregando por separado a la caché de planes. Los planes de ejecución de nuevas compilaciones utilizan recursos de base de datos, aumentan la duración de las consultas y desbordan la caché de planes, lo que provoca que estos se retiren de la caché. Este comportamiento de SQL Server puede modificarse estableciendo la opción de parametrización forzada en la base de datos. 

Para ayudarlo a estimar el impacto de esta recomendación, se le proporciona una comparación entre el uso real y el previsto de la CPU (como si se aplicase la recomendación). Además de los ahorros en uso de la CPU, se reduce la duración de las compilaciones de las consultas. También habrá mucha menos sobrecarga en la caché de planes, con lo que la mayoría de los planes permanecerán en la caché y podrán reutilizarse. Puede aplicar esta recomendación de forma rápida y sencilla haciendo clic en el comando **Aplicar**. 

Cuando lo haga, se habilitará en cuestión de minutos la opción de parametrización forzada en la base de datos y se iniciará el proceso de supervisión, que dura aproximadamente 24 horas. Cuando transcurra este periodo, podrá ver el informe de validación que muestra el uso de la CPU de la base de datos 24 horas antes y después de haber aplicado la recomendación. SQL Database Advisor tiene un mecanismo de seguridad que revierte automáticamente la recomendación aplicada en caso de detectarse una regresión del rendimiento.

## <a name="fix-schema-issues-recommendations"></a>Recomendaciones para solucionar problemas del esquema
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

## <a name="next-steps"></a>Pasos siguientes
Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. SQL Database Advisor sigue supervisando y ofreciendo recomendaciones que pueden mejorar el rendimiento de la base de datos. 

* Consulte el artículo del [Asistente de Base de datos SQL en el Portal de Azure](sql-database-advisor-portal.md) si quiere conocer los pasos necesarios para usar el Asistente de Base de datos SQL en el Portal de Azure.
* Consulte [Query Performance Insight](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.

## <a name="additional-resources"></a>Recursos adicionales
* [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Control de acceso basado en rol](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Feb17_HO1-->


