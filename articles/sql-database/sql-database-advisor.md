<properties 
   pageTitle="Asesor de Base de datos SQL de Azure" 
   description="El Asesor de Base de datos SQL de Azure ofrece recomendaciones para las bases de datos SQL existentes que pueden mejorar el rendimiento actual de las consultas." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="06/23/2016"
   ms.author="sstein"/>

# Asesor de Base de datos SQL

> [AZURE.SELECTOR]
- [Información general del Asesor de Base de datos SQL](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

El Asesor de Base de datos SQL de Azure ofrece recomendaciones para crear y quitar índices, parametrizar consultas y solucionar problemas del esquema. El Asesor de Base de datos SQL evalúa el rendimiento mediante el análisis del historial de uso de la base de datos SQL. Se recomienda usar los índices que sean más adecuados para ejecutar la carga de trabajo habitual de su base de datos.

Las recomendaciones siguientes están disponibles para los servidores V12 (no hay recomendaciones disponibles para servidores V11). En estos momentos, puede configurar que las recomendaciones de creación y eliminación de índices se apliquen automáticamente. Para más información, consulte la sección de [administración automática de índices](sql-database-advisor-portal.md#enable-automatic-index-management).

## Recomendaciones para crear índice 

Las recomendaciones de **creación de índices** aparecen cuando el servicio Base de datos SQL detecta que falta un índice que, si se crea, puede beneficiar a la carga de trabajo de las bases de datos (solo los índices no agrupados en clústeres).

## Recomendaciones para quitar índice

Las recomendaciones de **eliminación de índices** aparecen cuando el servicio Base de datos SQL detecta índices duplicados (funcionalidad actualmente en versión preliminar; solo puede aplicarse en índices duplicados).

## Recomendaciones para parametrizar consultas

Las recomendaciones de **parametrización de consultas** aparecen cuando el servicio Base de datos SQL detecta que tiene una o varias consultas que se vuelven a compilar continuamente, pero que terminan con el mismo plan de ejecución de consulta. Esto abre la posibilidad de aplicar la parametrización forzada, que permite que los planes de consulta se almacenen en caché y se reutilicen en el futuro para mejorar el rendimiento y reducir el uso de recursos.

Inicialmente, todas las consultas enviadas a SQL Server deben compilarse con el fin de generar un plan de ejecución que se vaya a utilizar para ejecutar la consulta. Cada plan generado se agrega a la caché de planes. Además, las ejecuciones posteriores de la misma consulta pueden volver a usar este plan desde la caché, con lo que no hay que realizar más compilaciones.

Las aplicaciones que envían consultas que incluyan valores sin parámetros pueden menoscabar el rendimiento, ya que se vuelve a compilar el plan de ejecución con cada consulta de este tipo. En numerosos casos, las mismas consultas con valores de parámetro diferentes generan los mismos planes de ejecución, pero estos se siguen agregando por separado a la caché de planes. Estas nuevas compilaciones utilizan recursos de base de datos, aumentan la duración de las consultas y desbordan la caché de planes, lo que provoca que los planes se eliminen de la caché. Este comportamiento de SQL Server puede modificarse si se establece la opción de parametrización forzada en la base de datos.

Para ayudarle a estimar el impacto de esta recomendación, se le proporciona una comparación entre el uso real y el previsto de CPU (como si se aplicase la recomendación). Además de los ahorros en uso de CPU, se reducirá la duración de las compilaciones de las consultas. También habrá mucha menos sobrecarga en la caché de planes, con lo que la mayoría de los planes permanecerán en la caché y podrán reutilizarse. Puede aplicar esta recomendación de forma rápida y sencilla haciendo clic en el comando Aplicar.

Cuando lo haga, se habilitará en cuestión de minutos la opción de parametrización forzada en la base de datos y se iniciará el proceso de supervisión, que dura aproximadamente 24 horas. Cuando transcurra este período, podrá ver el informe de validación que muestra el uso de CPU de la base de datos 24 horas antes y después de haber aplicado la recomendación. El Asistente de Base de datos SQL tiene un mecanismo de seguridad que revierte automáticamente la recomendación aplicada en caso de detectarse una regresión del rendimiento.

## Recomendaciones para solucionar problemas del esquema

Las recomendaciones de **solución de problemas del esquema** aparecen cuando el servicio Base de datos SQL advierte alguna anomalía en el número de errores SQL relacionados con el esquema que se producen en Base de datos SQL de Azure. Esta recomendación suele aparecer cuando la base de datos encuentra varios errores relacionados con el esquema (nombre de columna no válido, nombre de objeto no válido, etc.) en el curso de una hora.

Los problemas del esquema constituyen una clase de errores de sintaxis en SQL Server que se producen cuando no están alineadas la definición de la consulta SQL y la del esquema de base de datos (por ejemplo, en la tabla de destino falta una de las columnas que espera la consulta, o viceversa).

La recomendación de solución de problema del esquema aparece cuando el servicio Base de datos SQL de Azure advierte alguna anomalía en el número de errores SQL relacionados con el esquema que se producen en la Base de datos SQL de Azure. En la tabla siguiente se muestran los errores relacionados con los problemas de esquema:

|Código de error SQL|Message|
|--------------|-------|
|201|El procedimiento o la función '*' espera parámetros '*', que no se han proporcionado.|
|207|Nombre de columna '*' no válido.|
|208|Nombre de objeto '*' no válido. |
|213|El nombre o el número de columna de los valores especificados no corresponden a la definición de la tabla. |
|2812|No se pudo encontrar el procedimiento almacenado '*'. |
|8144|La función o el procedimiento * tiene demasiados argumentos. |

## Pasos siguientes

Supervise las recomendaciones y siga aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. El Asesor de Base de datos SQL seguirá supervisando y ofreciendo recomendaciones que podrían mejorar el rendimiento de la base de datos.

 - Consulte [Asesor de Base de datos SQL](sql-database-advisor-portal.md) en el Portal de Azure si quiere conocer los pasos necesarios para usar el Asistente de Base de datos SQL en el Portal de Azure.
 - Consulte [Query Performance Insights](sql-database-query-performance.md) para más información sobre el impacto en el rendimiento de las principales consultas.

## Recursos adicionales

- [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
- [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
- [Control de acceso basado en rol](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0629_2016-->