---
title: "Simultaneidad y administración de cargas de trabajo en SQL Data Warehouse | Microsoft Docs"
description: "Obtenga información sobre la simultaneidad y la administración de cargas de trabajo en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL
Para proporcionar un rendimiento predecible a escala, Almacenamiento de datos SQL de Microsoft Azure le permite controlar los niveles de simultaneidad y las asignaciones de recursos, como la asignación de prioridades de CPU y memoria. En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo, y se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos. La administración de cargas de trabajo de Almacenamiento de datos SQL está diseñada para admitir entornos de varios usuarios. No está diseñada para cargas de trabajo de multiinquilino.

## <a name="concurrency-limits"></a>Límites de simultaneidad
Almacenamiento de datos SQL permite hasta 1.024 conexiones simultáneas. Todas las 1.024 conexiones pueden enviar consultas al mismo tiempo. Sin embargo, para optimizar el rendimiento, Almacenamiento de datos SQL puede poner en cola algunas consultas para asegurarse de que cada una de ellas tiene garantizado un mínimo de memoria. Durante el tiempo de ejecución de las consultas, estas se empiezan a poner en cola. Al iniciar una cola con las consultas cuando se alcanzan los límites de simultaneidad, Almacenamiento de datos SQL puede aumentar el rendimiento total, asegurándose de que las consultas activas obtienen el acceso a los recursos de la memoria que tanto necesitan.  

Los límites de simultaneidad se rigen por dos conceptos: *consultas simultáneas* y *espacios de simultaneidad*. Para que una consulta se ejecute, lo ha de hacer tanto dentro de su límite de simultaneidad como dentro de la asignación de espacio de simultaneidad.

* Las consultas simultáneas son consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 consultas simultáneas en los tamaños de DWU más grandes.
* Los espacios de simultaneidad se asignan según DWU. Cada 100 DWU proporciona 4 espacios de simultaneidad. Por ejemplo, un DW100 asigna 4 espacios de simultaneidad y DW1000 asigna 40. Cada consulta consume uno o más espacios de simultaneidad, en función de la [clase de recursos](#resource-classes) de la consulta. Las consultas que se ejecutan en la clase de recurso smallrc consumen una ranura de simultaneidad. Las consultas que se ejecutan en una clase de recurso superior consumen más intervalos de simultaneidad.

La tabla siguiente describe los límites de consultas simultáneas y espacios de simultaneidad en los distintos tamaños de DWU.

### <a name="concurrency-limits"></a>Límites de simultaneidad
| DWU | N.º máximo de consultas simultáneas | Espacios de simultaneidad asignados |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 | |20 | |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Cuando se alcanza alguno de estos umbrales, se ponen a la cola nuevas consultas y se ejecutan según la regla "primero en entrar, primero en salir".  A medida que las consultas finalizan y el número de consultas y ranuras desciende por debajo de los límites, las consultas en cola se liberan. 

> [!NOTE]  
> *Select* que se ejecutan exclusivamente en vistas de administración dinámica (DMV) o vistas de catálogo no están reguladas por ninguno de los límites de simultaneidad. Puede supervisar el sistema independientemente del número de consultas que se ejecutan en él.
> 
> 

## <a name="resource-classes"></a>Clases de recursos
Las clases de recursos le permiten controlar la asignación de memoria y los ciclos de CPI que se asignan a una consulta. Puede asignar dos tipos de recursos a un usuario en forma de roles de base de datos. Los dos tipos de clases de recursos son los siguientes:
1. Clases de recursos dinámicos (**smallrc, mediumrc, largerc, xlargerc**) asignan una cantidad variable de memoria en función de la unidad de almacenamiento de datos actual. Esto significa que, al escalar a una unidad mayor, las consultas obtienen más memoria automáticamente. 
2. Las clases de recursos estáticos (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) asignan la misma cantidad de memoria, independientemente de la unidad de almacenamiento de datos actual (siempre que la propia unidad tenga memoria suficiente). Esto significa que, con unidades mayores, puede ejecutar más consultas en cada clase de recursos al mismo tiempo.

A los usuarios con **smallrc** y **staticrc10** se les concede una cantidad de memoria menor y pueden aprovechar una mayor simultaneidad. Por el contrario, los usuarios asignados a **xlargerc** o **staticrc80** reciben grandes cantidades de memoria y, por tanto, son menos las consultas que pueden ejecutar de manera simultánea.

De forma predeterminada, cada usuario es miembro de la clase de recursos pequeña: **smallrc**. El procedimiento `sp_addrolemember` se usa para aumentar la clase de recursos, mientras que `sp_droprolemember` se usa para disminuirla. Por ejemplo, este comando aumentaría la clase de recursos de loaduser a **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Consultas que no respetan las clases de recursos

Existen algunos tipos de consultas que no se benefician de una mayor asignación de memoria. El sistema omite su asignación de clase de recursos y siempre ejecuta estas consultas en la clase de recursos pequeña. Si estas consultas se ejecutan siempre en la clase de recursos small, se pueden ejecutar cuando los espacios de simultaneidad estén bajo presión y no consumirán más espacios que los necesarios. Consulte [Excepciones de clase de recursos](#query-exceptions-to-concurrency-limits) para más información.

## <a name="details-on-resource-class-assignment"></a>Detalles sobre la asignación de la clase de recursos


Más detalles en la clase de recursos:

* *Alter role* es necesario para cambiar la clase de recursos de un usuario.
* Aunque puede agregar un usuario a una o varias de las clases de recursos más altas, los dinámicos tienen prioridad sobre los estáticos. Es decir, si un usuario está asignado tanto a **mediumrc**(dinámico) como a **staticrc80**(estático), **mediumrc** es la clase de recursos que se respeta.
 * Cuando a un usuario se le asigna más de una clase de recurso en un tipo de clase de recursos específico (más de uno dinámico o más de uno estático), se respeta la más alta. Es decir, si a un usuario se le asigna mediumrc y largerc, se aplica la clase de recursos más alta, largerc. Y, si a un usuario se le asignan ambas, **staticrc20** y **statirc80**, se usa **staticrc80**.
* No se puede cambiar la clase de recursos del usuario administrativo.

Para un ejemplo detallado, consulte [Cambio de ejemplo de clase de recursos de usuario](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Asignación de memoria
Aumentar clase de recursos de un usuario tiene ventajas y desventajas. Al aumentar una clase de recursos para un usuario, a sus consultas se les concede acceso a más memoria, lo que puede implicar que las consultas se ejecuten más rápido.  Sin embargo, las clases de recursos más altas también reducen el número de consultas simultáneas que se pueden ejecutar. Esto es consecuencia del principio de mantener el equilibrio entre asignar grandes cantidades de memoria a una sola consulta o permitir que otras consultas, que también necesitan asignaciones de memoria, se ejecuten al mismo tiempo. Si un usuario recibe asignaciones altas de memoria para una consulta, otros usuarios no tendrán acceso a esa misma memoria a fin de ejecutar una consulta.

La tabla siguiente presenta un esquema de la memoria asignada a cada distribución por DWU y clases de recursos.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Asignaciones de memoria por distribución para las clases de recursos dinámicos (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1600 |
| DW500 |100 |400 |800 |1600 |
| DW600 |100 |400 |800 |1600 |
| DW1000 |100 |800 |1600 |3.200 |
| DW1200 |100 |800 |1600 |3.200 |
| DW1500 |100 |800 |1600 |3.200 |
| DW2000 |100 |1600 |3.200 |6.400 |
| DW3000 |100 |1600 |3.200 |6.400 |
| DW6000 |100 |3.200 |6.400 |12.800 |

La tabla siguiente presenta un esquema de la memoria asignada a cada distribución por unidad de almacenamiento de datos y clase de recursos estáticos. Tenga en cuenta que las clases de recursos más altas tienen una memoria reducida para respetar los límites de unidad globales.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Asignaciones de memoria por distribución para las clases de recursos estáticos (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1600 |1600 |1600 |1600 |
| DW500 |100 |200 |400 |800 |1600 |1600 |1600 |1600 |
| DW600 |100 |200 |400 |800 |1600 |1600 |1600 |1600 |
| DW1000 |100 |200 |400 |800 |1600 |3.200 |3.200 |3.200 |
| DW1200 |100 |200 |400 |800 |1600 |3.200 |3.200 |3.200 |
| DW1500 |100 |200 |400 |800 |1600 |3.200 |3.200 |3.200 |
| DW2000 |100 |200 |400 |800 |1600 |3.200 |6.400 |6.400 |
| DW3000 |100 |200 |400 |800 |1600 |3.200 |6.400 |6.400 |
| DW6000 |100 |200 |400 |800 |1600 |3.200 |6.400 |12.800 |

A partir de la tabla anterior, puede ver que una consulta que se ejecuta en DW2000 en la clase de recurso **xlargerc**, tendría acceso a 6 400 MB de memoria en cada una de las 60 bases de datos distribuidas.  En Almacenamiento de datos SQL, existe 60 distribuciones. Por lo tanto, para calcular la asignación de memoria total para una consulta en una clase de recurso dada, los valores anteriores se deben multiplicar por 60.

### <a name="memory-allocations-system-wide-gb"></a>Asignaciones de memoria en todo el sistema (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Por esta tabla de asignaciones de memoria de todo el sistema, puede ver que a una consulta que se ejecuta en DW2000 en la clase de recurso xlargercs se le asigna un total de 375 GB de memoria (6400 MB * 60 distribuciones / 1024 para pasar a GB) sobre la totalidad del almacenamiento de SQL Data Warehouse.

El mismo cálculo se aplica a las clases de recursos estáticos.
 
## <a name="concurrency-slot-consumption"></a>Consumo de ranuras de simultaneidad  
Almacenamiento de datos SQL concederá más memoria a las consultas que se ejecutan en clases de recursos superiores. La memoria es un recurso fijo.  Por lo tanto, cuanta más memoria asignada por consulta, menos consultas simultáneas se pueden ejecutar. En la tabla siguiente se reiteran todos los conceptos anteriores en una vista única donde se muestra el número de intervalos de simultaneidad disponibles por DWU, así como los espacios que consume cada clase de recurso.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Asignación y consumo de espacios de simultaneidad para las clases de recursos dinámicos  
| DWU | N.º máximo de consultas simultáneas | Espacios de simultaneidad asignados | Ranuras utilizadas por smallrc | Ranuras utilizadas por mediumrc | Ranuras utilizadas por largerc | Ranuras utilizadas por xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 | |20 | |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Asignación y consumo de espacios de simultaneidad para las clases de recursos estáticos  
| DWU | N.º máximo de consultas simultáneas | Espacios de simultaneidad asignados |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20 || 20 || 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

En esta tabla, puede ver que SQL Data Warehouse, que se ejecuta como DW1000, ofrece 32 consultas simultáneas como máximo y 40 ranuras de simultaneidad en total. Si todos los usuarios se ejecutan en la clase smallrc, se permitirían 32 consultas simultáneas, ya que cada una consumiría 1 espacio de simultaneidad. Si todos los usuarios de DW1000 se ejecutaran en la clase mediumrc, se asignaría a cada consulta 800 MB por distribuciones para una asignación de memoria total de 47 GB por consulta y la simultaneidad se limitaría a 5 usuarios (40 espacios de simultaneidad/8 espacios por usuario mediumrc).

## <a name="selecting-proper-resource-class"></a>Selección de la clase de recurso correcta  
Es una práctica recomendada asignar usuarios permanentemente a una clase de recursos en lugar de cambiar sus clases de recursos. Por ejemplo, las cargas a tablas de almacén de columnas en clúster crean índices de mayor calidad cuando se les asigna más memoria. Para asegurarse de que las cargas tienen acceso a una memoria superior, cree un usuario específico para cargar datos y asigne este usuario de forma permanente a una clase de recursos más alta.
Aquí puede consultar varios procedimientos recomendados. Como se mencionó anteriormente, SQL DW admite dos tipos de clase de recurso: estáticos y dinámicos.
### <a name="loading-best-practices"></a>Carga de procedimientos recomendados
1.  Si se espera tener cargas con una cantidad regular de datos, una clase de recurso estático es una buena elección. Más adelante, al escalar para obtener una mayor capacidad de proceso, el almacenamiento de datos será capaz de ejecutar más consultas simultáneas sin hacer cambios, ya que el usuario que realiza la carga no consume más memoria.
2.  Si se espera tener mayores cargas en algunas ocasiones, una clase de recurso dinámico es una buena elección. Más adelante, al escalar para obtener una mayor capacidad de proceso, el usuario que realiza la carga obtendrá más memoria directamente, con lo que la carga se podrá realizar más rápido.

La memoria necesaria para procesar las cargas eficazmente depende de la naturaleza de la tabla cargada y de la cantidad de datos procesados. Por ejemplo, cargar datos en tablas CCI requiere memoria para permitir que se llegue a los grupos de filas CCI de forma óptima. Para más información, consulte la guía sobre los índices de almacén de columnas y la carga de datos.

Como procedimiento recomendado, le aconsejamos que utilice al menos 200 MB de memoria para las cargas.

### <a name="querying-best-practices"></a>Procedimientos recomendados sobre las consultas
Las consultas tienen requisitos diferentes en función de su complejidad. Tanto el aumento de la memoria por cada consulta como la simultaneidad constituyen mecanismos válidos para aumentar el rendimiento general según las necesidades de la consulta.
1.  Si se espera tener consultas normales y complejas (por ejemplo, para generar informes diarios y semanales) y no es necesario aprovechar las ventajas de la simultaneidad, una clase de recurso dinámico es una buena elección. Si el sistema tiene más datos que se van a procesar, al escalar el almacenamiento de datos se proporcionará, por tanto, automáticamente más memoria para el usuario que ejecuta la consulta.
2.  Si se espera tener modelos de simultaneidad variables o diurnos (por ejemplo, si se consulta la base de datos a través de la interfaz de usuario de un sitio web ampliamente accesible), una clase de recurso estático es una buena elección. Más adelante, cuando se escale a un almacenamiento de datos, el usuario asociado a la clase de recurso estático será capaz automáticamente de ejecutar más consultas simultáneas.

La selección de la concesión de memoria adecuada según las necesidades de las consultas no resulta trivial, ya que depende de muchos factores, como la cantidad de datos consultados, la naturaleza de los esquemas de tabla y los diversos predicados de grupo, selección y combinación. Desde un punto de vista general, la asignación de más memoria permitirá que las consultas se completen más rápidamente, pero podría reducir la simultaneidad global. Si la simultaneidad no es un problema, asignar una cantidad de memoria mayor de la necesaria no resulta perjudicial. Para ajustar el rendimiento, puede ser preciso probar con distintas combinaciones de clases de recursos.

Puede usar el siguiente procedimiento almacenado para averiguar la concesión de memoria y simultaneidad por clase de recurso en un SLO determinado y la clase de recurso recomendada más apropiada para operaciones de CCI que usen mucho la memoria en la tabla CCI sin particiones en una clase de recurso determinado:

#### <a name="description"></a>Description:  
Este es el propósito de este procedimiento almacenado:  
1. Ayudar a los usuarios a averiguar la concesión de memoria y simultaneidad por clase de recurso en un SLO determinado. El usuario tiene que proporcionar un valor NULL tanto para el esquema como para el nombre de tabla, tal como se muestra en el ejemplo siguiente.  
2. Ayudar al usuario a averiguar la clase de recurso recomendada más adecuada para las operaciones CCI que usan mucha memoria (carga, tabla de copia, regeneración de índices, etc.) en la tabla CCI sin particiones con una clase de recurso determinado. El procedimiento almacenado usa el esquema de tabla para averiguar la concesión de memoria necesaria en este caso.

#### <a name="dependencies--restrictions"></a>Dependencias y restricciones:
- Este procedimiento almacenado no está diseñado para calcular los requisitos de memoria de la tabla CCI con particiones.    
- Este procedimiento almacenado no tiene en cuenta los requisitos de memoria de la parte SELECT de CTAS/INSERT-SELECT y se da por supuesto que es una instrucción SELECT simple.
- Este procedimiento almacenado utiliza una tabla temporal, por lo que se puede utilizar en la sesión en la que se creó.    
- Este procedimiento almacenado depende de las ofertas actuales (por ejemplo, configuración de hardware, configuración DMS) y, si algo cambiara, no funcionaría correctamente.  
- Este procedimiento almacenado depende del límite de simultaneidad ofrecido actualmente y, si este cambiara, no funcionaría correctamente.  
- Este procedimiento almacenado depende de las ofertas de clase de recursos actuales y, si estas cambiaran, no funcionaría correctamente.  

>  [!NOTE]  
>  Si no se obtienen resultados después de ejecutar el procedimiento almacenado con los parámetros proporcionados, podrían darse dos circunstancias. <br />1. Algún parámetro del almacenamiento de datos contiene un valor de SLO no válido <br />2. O bien, no hay ninguna clase de recursos coincidente para la operación CCI, si se proporcionó el nombre de la tabla. <br />Por ejemplo, en DW100, la concesión de memoria máxima disponible es de 400 MB y el esquema de tabla es lo suficientemente ancho como para superar el requisito de 400 MB.
      
#### <a name="usage-example"></a>Ejemplo de uso:
Sintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: proporcione un parámetro NULL para extraer la unidad de almacenamiento de datos actual de la base de datos de almacenamiento de datos o proporcione una unidad admitida con el formato 'DW100'
2. @SCHEMA_NAME: proporcione un nombre de esquema de la tabla
3. @TABLE_NAME: proporcione un nombre de tabla del interés

Ejemplos de ejecución de este procedimiento almacenado:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

La instancia de Table1 utilizada en los ejemplos anteriores se pudo crear como sigue:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Aquí puede ver la definición del procedimiento almacenado:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Importancia de las consultas
Almacenamiento de datos SQL implementa las clases de recursos mediante el uso de grupos de cargas de trabajo. Hay un total de ocho grupos de cargas de trabajo que controlan el comportamiento de las clases de recursos en los distintos tamaños de DWU. Para cualquier DWU, Almacenamiento de datos SQL solo usa cuatro de los ocho grupos de cargas de trabajo. Esto tiene sentido porque cada grupo de cargas de trabajo está asignado a una de las cuatro clases de recursos: smallrc, mediumrc, largerc o xlargerc. La importancia de comprender los grupos de cargas de trabajo es que algunos de estos grupos se establecen con un nivel de *importancia*más alto. El nivel de importancia se usa para la programación de la CPU. Las consultas que se ejecutan con importancia alta obtendrán tres veces más ciclos de CPU que aquellas con importancia media. Por lo tanto, las asignaciones de espacio de simultaneidad también determinan la prioridad en la CPU. Si una consulta utiliza 16 o más espacios, se ejecuta con importancia alta.

La tabla siguiente muestra las asignaciones de importancia para cada grupo de cargas de trabajo.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Asignaciones de grupos de cargas de trabajo a los espacios de simultaneidad e importancia
| Grupos de carga de trabajo | Asignación de espacio de simultaneidad | MB/Distribución | Asignación de importancia |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Mediano |
| SloDWGroupC01 |2 |200 |Mediano |
| SloDWGroupC02 |4 |400 |Mediano |
| SloDWGroupC03 |8 |800 |Mediano |
| SloDWGroupC04 |16 |1600 |Alto |
| SloDWGroupC05 |32 |3.200 |Alto |
| SloDWGroupC06 |64 |6.400 |Alto |
| SloDWGroupC07 |128 |12.800 |Alto |

Desde el gráfico **Asignación y consumo de espacios de simultaneidad** , es posible ver que un DW500 usa 1, 4, 8 o 16 espacios de simultaneidad para smallrc, mediumrc, largerc y xlargerc, respectivamente. Puede buscar estos valores en el gráfico anterior para encontrar la importancia de cada clase de recursos.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Asignación de DW500 de las clases de recursos a importancia
| clase de recursos | Grupo de cargas de trabajo | Espacios de simultaneidad usados | MB/Distribución | importancia |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Mediano |
| mediumrc |SloDWGroupC02 |4 |400 |Mediano |
| largerc |SloDWGroupC03 |8 |800 |Mediano |
| xlargerc |SloDWGroupC04 |16 |1600 |Alto |
| staticrc10 |SloDWGroupC00 |1 |100 |Mediano |
| staticrc20 |SloDWGroupC01 |2 |200 |Mediano |
| staticrc30 |SloDWGroupC02 |4 |400 |Mediano |
| staticrc40 |SloDWGroupC03 |8 |800 |Mediano |
| staticrc50 |SloDWGroupC03 |16 |1600 |Alto |
| staticrc60 |SloDWGroupC03 |16 |1600 |Alto |
| staticrc70 |SloDWGroupC03 |16 |1600 |Alto |
| staticrc80 |SloDWGroupC03 |16 |1600 |Alto |

Puede usar la siguiente consulta DMV para ver las diferencias en la asignación de recursos de memoria en detalle desde la perspectiva del regulador de recursos, o bien para analizar el uso activo e histórico de los grupos de cargas de trabajo en el momento de solucionar problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que respetan los límites de simultaneidad
La mayoría de las consultas se rigen por las clases de recursos. Estas consultas deben encontrarse dentro de los umbrales de ranuras de simultaneidad y consultas simultáneas. Un usuario no puede elegir excluir una consulta del modelo de espacio de simultaneidad.

Nuevamente, las siguientes instrucciones respetan las clases de recursos:

* INSERT-SELECT
* UPDATE
* DELETE
* SELECT (al consultar las tablas de usuario)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Carga de datos
* Operaciones de movimiento de datos llevadas a cabo por el servicio de movimiento de datos (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Excepciones de la consulta a los límites de simultaneidad
Algunas consultas no respetan la clase de recursos a la que está asignado el usuario. Estas excepciones a los límites de simultaneidad se realizan cuando los recursos de memoria necesarios para un determinado comando son bajos, a menudo porque el comando es una operación de metadatos. El objetivo de estas excepciones es evitar asignaciones mayores de memoria a las consultas que nunca las necesitarán. En estos casos, siempre se usa la clase de recursos predeterminada pequeña (smallrc) con independencia de la clase de recursos real asignada al usuario. Por ejemplo, `CREATE LOGIN` se ejecutará siempre en la clase smallrc. Los recursos necesarios para llevar a cabo esta operación son muy bajos, por lo que no tiene sentido incluir la consulta en el modelo de espacio de simultaneidad.  Estas consultas tampoco están limitadas por el límite de simultaneidad de 32 usuarios; sino que puede ejecutar un número ilimitado de estas consultas hasta llegar al límite de 1024 sesiones.

Las instrucciones siguientes no respetan las clases de recursos:

* CREATE o DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT o MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE o DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER o DROP USER
* CREATE, ALTER o DROP PROCEDURE
* CREATE o DROP VIEW
* INSERT VALUES
* SELECT (desde vistas del sistema y DMV)
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Ejemplo de cambio de una clase de recursos de usuario
1. **Cree un inicio de sesión:** abra una conexión con la base de datos **maestra** en el servidor SQL Server que hospeda la base de datos de SQL Data Warehouse y ejecute los comandos siguientes.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Es una buena idea crear un usuario en la base de datos maestra para los usuarios de Almacenamiento de datos SQL de Azure. La creación de un usuario en la base de datos maestra posibilita el inicio de sesión mediante herramientas como SSMS sin especificar un nombre de base de datos.  También permite el uso del Explorador de objetos para ver todas las bases de datos en un servidor SQL Server.  Para obtener más información sobre cómo crear y administrar usuarios, consulte [Proteger una base de datos en SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Cree un usuario de SQL Data Warehouse**: abra una conexión con la base de datos de **SQL Data Warehouse** y ejecute el comando siguiente.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Conceder permisos:** el siguiente ejemplo se concede `CONTROL` en la base de datos de **SQL Data Warehouse**. `CONTROL` en la base de datos de nivel es el equivalente de db_owner en SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Aumente la clase de recursos:** use la consulta siguiente para agregar un usuario a un rol de administración de cargas de trabajo con privilegios más altos.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Disminuya la clase de recursos:** use la consulta siguiente para eliminar un usuario de un rol de administración de cargas de trabajo.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > No se puede quitar un usuario de smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Detección de consulta en cola y otras DMV
Puede usar la DMV `sys.dm_pdw_exec_requests` para identificar las consultas que están a la espera en una cola de simultaneidad. Las consultas que esperan un espacio de simultaneidad tendrán el estado de **suspendido**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Los roles de administración de cargas de trabajo se pueden ver con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La consulta siguiente muestra qué rol tiene asignado cada usuario.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

Almacenamiento de datos SQL tiene los siguientes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: se refiere a las consultas que residen fuera del marco del espacio de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.
* **UserConcurrencyResourceType**: se refiere a las consultas que residen dentro del marco del espacio de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.
* **DmsConcurrencyResourceType**: se refiere a las esperas que son el resultado de operaciones de movimiento de datos.
* **BackupConcurrencyResourceType**: se refiere a una espera que indica que se está creando la copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se solicitaron al mismo tiempo, las demás se pondrán en la cola.

La DMV `sys.dm_pdw_waits` puede utilizarse para ver por qué recursos está esperando una solicitud.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_resource_waits` muestra solo las esperas de recursos consumidas por una consulta determinada. El tiempo de espera del recurso solo mide el tiempo que se espera a que se proporcionen los recursos, por oposición al tiempo de espera de la señal, que es el tiempo que tardan los servidores SQL Server subyacentes en programar la consulta en la CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_wait_stats` se puede utilizar para analizar las tendencias históricas de las esperas.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo administrar los usuarios y la seguridad de la base de datos, consulte [Proteger una base de datos en SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para más información sobre cómo las clases de recursos mayores pueden mejorar la calidad de los índices de almacén de columnas agrupado, consulte [Regeneración de índices para mejorar la calidad de los segmentos].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Regeneración de índices para mejorar la calidad de los segmentos]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
