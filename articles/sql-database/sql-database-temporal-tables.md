---
title: "Introducción a las tablas temporales de Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre cómo empezar a usar las tablas temporales de Base de datos SQL de Azure"
services: sql-database
documentationcenter: 
author: bonova
manager: jhubbard
editor: 
ms.assetid: c8c0f232-0751-4a7f-a36e-67a0b29fa1b8
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 01/10/2017
ms.author: bonova
ms.openlocfilehash: 58f97c142ba0b9282d8988fc1bc037b9c0c69986
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introducción a las tablas temporales de Base de datos SQL de Azure
Las tablas temporales son una nueva característica de programación de Base de datos SQL de Azure que permiten realizar un seguimiento del historial completo de los cambios en los datos y analizarlo, sin necesidad de codificación personalizada. Las tablas temporales mantienen datos estrechamente relacionados con el contexto de tiempo para que se puedan interpretar los hechos almacenados como válidos solo dentro del período específico. Esta propiedad de las tablas temporales permite un análisis eficaz basado en el tiempo y obtener información detalladas de la evolución de los datos.

## <a name="temporal-scenario"></a>Escenario temporal
Este artículo muestra los pasos para utilizar tablas temporales en un escenario de aplicación. Suponga que desea realizar un seguimiento de la actividad del usuario en un sitio web nuevo que se está desarrollando desde cero o en un sitio web existente que desea extender con análisis de la actividad del usuario. En este ejemplo simplificado, se supone que el número de páginas web visitadas durante un período de tiempo es un indicador que necesita capturarse y supervisarse en la base de datos del sitio web que se hospeda en Base de datos SQL de Azure. El objetivo del análisis histórico de la actividad del usuario es obtener entradas para rediseñar el sitio web y ofrecer la mejor experiencia para los visitantes.

El modelo de base de datos para este escenario es muy simple: la métrica de la actividad del usuario se representa con un solo campo de entero, **PageVisited**, y se captura junto con información básica sobre el perfil del usuario. Además, para el análisis basado en el tiempo, se mantendría una serie de filas para cada usuario, donde cada fila representa el número de páginas que un usuario determinado visitó dentro de un período de tiempo específico.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Afortunadamente, no es necesario esforzarse mucho en la aplicación para mantener esta información de actividad. Con las tablas temporales, este proceso se automatiza, ofreciéndole total flexibilidad durante el diseño del sitio web y más tiempo para centrarse en el propio análisis de los datos. Lo único que tiene que hacer es asegurarse de que la tabla **WebSiteInfo** está configurada como [temporal con versión del sistema](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Los pasos exactos para utilizar tablas temporales en este escenario se describen a continuación.

## <a name="step-1-configure-tables-as-temporal"></a>Paso 1: Configurar tablas como temporales
Dependiendo de si inicia un nuevo desarrollo o actualiza una aplicación existente, creará tablas temporales o modificará las existentes agregando atributos temporales. En general, el escenario puede ser una combinación de estas dos opciones. Realice estas acciones utilizando [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) o cualquier otra herramienta de desarrollo de Transact-SQL.

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Creación de una nueva tabla
Utilice el elemento del menú contextual para nueva tabla con versión del sistema en el explorador de objetos de SSMS para abrir el editor de consultas con un script de plantilla de tabla temporal y después utilice "Especificar valores para parámetros de plantilla" (Ctrl+Mayús+M) para rellenar la plantilla:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

En SSDT, elija la plantilla “Tabla temporal (con versión del sistema)” cuando agregue nuevos elementos al proyecto de base de datos. Se abrirá el diseñador de tablas y podrá especificar fácilmente el diseño de tabla:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

También puede crear una tabla temporal especificando las instrucciones de Transact-SQL directamente, como se muestra en el ejemplo siguiente. Tenga en cuenta que los elementos obligatorios de todas las tablas temporales son la definición PERIOD y la cláusula SYSTEM_VERSIONING con una referencia a otra tabla de usuario que almacenará versiones de filas históricas:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Cuando se crea la tabla temporal con versión del sistema, la tabla del historial correspondiente se crea automáticamente con la configuración predeterminada. La tabla de historial predeterminada contiene un índice de árbol B agrupado en las columnas del período (fin, inicio) con la compresión de página habilitada. Esta configuración es óptima para la mayoría de los escenarios en los que se usan tablas temporales, especialmente para [auditoría de datos](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

En este caso particular, nuestro objetivo es realizar un análisis de tendencias basado en el tiempo a través de un historial de datos más largo y con conjuntos de datos más grandes, por lo que la elección de almacenamiento para la tabla de historial es un índice de almacén de columnas agrupado. Un almacén de columnas agrupado proporciona muy buena compresión y rendimiento de consultas analíticas. Las tablas temporales proporcionan flexibilidad para configurar índices en las tablas actuales y temporales de forma totalmente independiente. 

> [!NOTE]
> Los índices de almacén de columnas solo están disponibles en el nivel de servicio Premium.
>

El siguiente script muestra cómo se puede cambiar el índice predeterminado en la tabla de historial para el almacén de columnas agrupado:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Las tablas temporales se representan en el Explorador de objetos con el icono específico para facilitar la identificación, mientras que su tabla de historial se muestra como un nodo secundario.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Cambio de una tabla existente a temporal
Vamos a tratar el escenario alternativo en el que la tabla WebsiteUserInfo ya existe, pero no se diseñó para mantener un historial de cambios. En este caso, simplemente puede extender la tabla existente para convertirla en temporal, tal y como se muestra en el ejemplo siguiente:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Paso 2: Ejecutar la carga de trabajo regularmente
La principal ventaja de las tablas temporales es que no es necesario cambiar o ajustar el sitio web de ninguna forma para realizar el seguimiento de cambios. Una vez creadas, las tablas temporales conservan las versiones de fila anteriores cada vez que realiza modificaciones en los datos. 

Para poder aprovechar el seguimiento de cambios automático para este escenario en particular, simplemente vamos a actualizar la columna **PagesVisited** cada vez que el usuario finaliza la sesión en el sitio web:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Es importante tener en cuenta que la consulta de actualización no necesita saber la hora exacta en la que se produjo la operación real ni cómo se conservarán los datos históricos para futuros análisis. Base de datos SQL de Azure controla ambos aspectos automáticamente. El siguiente diagrama ilustra cómo se generan los datos del historial en cada actualización.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Paso 3: Realizar análisis de datos históricos
Ahora, cuando el control de versiones del sistema temporal está habilitado, el análisis de datos históricos se realiza con una sola consulta. En este artículo, proporcionaremos algunos ejemplos que abordan escenarios comunes de análisis. Para obtener todos los detalles, explore diversas opciones presentadas con la cláusula [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3).

Para ver los 10 usuarios principales ordenados por el número de páginas web visitadas desde hace una hora, ejecute esta consulta:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Puede modificar fácilmente esta consulta para analizar las visitas al sitio desde hace un día, un mes o en cualquier punto del pasado que desee.

Para realizar un análisis estadístico básico del día anterior, utilice el siguiente ejemplo:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Para buscar actividades de un usuario específico dentro de un período de tiempo, utilice la cláusula CONTAINED IN:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

La visualización de gráficos es especialmente conveniente para consultas temporales, ya que puede mostrar tendencias y patrones de uso de una forma intuitiva muy fácilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Evolución del esquema de tabla
Normalmente, necesita cambiar el esquema de tabla temporal mientras se está realizando el desarrollo de aplicaciones. Para ello, simplemente ejecute instrucciones ALTER TABLE convencionales y Base de datos SQL de Azure propagará los cambios adecuadamente a la tabla de historial. El siguiente script muestra cómo puede agregar atributos adicionales para el seguimiento:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

De forma similar, puede cambiar la definición de columna mientras la carga de trabajo esté activa:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Por último, puede quitar una columna que ya no necesite.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

También puede usar la versión más reciente de [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para cambiar el esquema de tabla temporal mientras esté conectado a la base de datos (modo en línea) o como parte del proyecto de base de datos (modo sin conexión).

## <a name="controlling-retention-of-historical-data"></a>Control de la retención de datos históricos
Con tablas temporales con versión del sistema, la tabla de historial puede aumentar el tamaño de la base de datos más que las tablas convencionales. Una tabla de historial grande y creciente puede ser un problema debido a los costos de almacenamiento puro y a la imposición de un impuesto de rendimiento sobre las consultas temporales. Por lo tanto, el desarrollo de una directiva de retención de datos para administrar datos en la tabla de historial es un aspecto importante del planeamiento y la administración del ciclo de vida de cada tabla temporal. Con Base de datos SQL de Azure, dispone de los siguientes enfoques para administrar datos históricos en la tabla temporal:

* [Partición de tabla](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de limpieza personalizado](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Pasos siguientes
Para obtener información detallada sobre tablas temporales, consulte la [documentación de MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite Channel 9 para oír un [caso de éxito de implementación temporal de cliente real](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) y ver una [demostración temporal en directo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

