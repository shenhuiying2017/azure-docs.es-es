---
title: "Ejecución de consultas de análisis ad-hoc entre varias bases de datos de Azure SQL| Microsoft Docs"
description: "Ejecute consultas de análisis ad-hoc entre varias bases de datos SQL en un ejemplo de aplicación multiinquilino."
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.openlocfilehash: a27a65627fecf35b59122110250a40c6fe8077b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ejecución de consultas de análisis ad-hoc entre varias bases de datos SQL de Azure

En este tutorial, se ejecutan consultas distribuidas en todo el conjunto de bases de datos multiinquilino para habilitar el análisis ad hoc. La consulta elástica se usa para habilitar las consultas distribuidas, lo que requiere la implementación de una base de datos de análisis adicional (en el servidor de catálogo). Estas consultas pueden extraer información incluida en los datos operativos diarios de la aplicación SaaS Wingtip.


En este tutorial, obtendrá información:

> [!div class="checklist"]

> * Acerca de las vistas globales en cada base de datos; estas vistas habilitan la realización eficiente de consultas en varios inquilinos
> * Acerca de la implementación de una base de datos de análisis ad hoc
> * Acerca de cómo ejecutar consultas distribuidas en todas las bases de datos de inquilinos



Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) está instalado. Para descargar e instalar SSMS, consulte [Descarga de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-analytics-pattern"></a>Patrón de análisis ad hoc

Una de las grandes oportunidades que ofrecen las aplicaciones SaaS es el uso de la gran cantidad de datos de inquilino almacenados centralmente en la nube. Puede usar dichos datos para obtener información sobre el funcionamiento y el uso de la aplicación. Esta información puede guiarle en el desarrollo de características, mejoras de facilidad de uso y otras inversiones en sus aplicaciones y servicios.

Es fácil obtener acceso a estos datos en una sola base de datos multiinquilino, pero no es tan fácil cuando se distribuyen a escala en miles de bases de datos. Un enfoque es usar la [consulta elástica](sql-database-elastic-query-overview.md), que permite consultas en un conjunto distribuido de bases de datos con un esquema común. La consulta elástica usa una única base de datos *principal* en la que se definen tablas externas que reflejan tablas o vistas en las bases de datos (de inquilino) distribuidas. Las consultas que se envían a esta base de datos principal se compilan para producir un plan de consulta distribuido, con partes de la consulta aplicadas en las bases de datos de inquilino según sea necesario. La consulta elástica usa el mapa de particiones de la base de datos del catálogo para proporcionar la ubicación de las bases de datos de inquilino. La configuración y la consulta son sencillas y utilizan [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) estándar; además, admiten consultas ad hoc desde herramientas como Power BI y Excel.

Mediante la distribución de las consultas en las bases de datos de inquilino, la consulta elástica proporciona una visión inmediata de los datos de producción activos. Sin embargo, como la consulta elástica puede extraer los datos de muchas bases de datos, la latencia de las consultas a veces puede ser mayor que las consultas equivalentes enviadas a una sola base de datos multiinquilino. Asegúrese de diseñar las consultas para minimizar los datos que se devuelven. La consulta elástica a menudo resulta más apropiada para consultar pequeñas cantidades de datos en tiempo real, en lugar de generar informes o consultas de análisis complejos o usados con frecuencia. Si las consultas no funcionan correctamente, examine el [plan de ejecución](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) para ver qué parte de la consulta se ha transferido a la base de datos remota y cuántos datos se devuelven. Las consultas que requieren un procesamiento analítico complejo en algunos casos pueden ser mejor atendidas si se extraen los datos de inquilino en una base de datos dedicada o un almacenamiento de datos optimizado para las consultas de análisis. Este enfoque se explica en el [tutorial de análisis de inquilino](sql-database-saas-tutorial-tenant-analytics.md). 

## <a name="get-the-wingtip-application-scripts"></a>Obtener scripts de la aplicación Wingtip

Los scripts SaaS de Wingtip y el código fuente de la aplicación están disponibles en el repositorio de GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Pasos para descargar los scripts SaaS de Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-ticket-sales-data"></a>Creación de datos de ventas de entradas

Para ejecutar consultas en un conjunto de datos más interesante, ejecute el generador de entradas para crear datos de ventas de entradas.

1. En *PowerShell ISE*, abra el script ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* y establezca los valores siguientes:
   * **$DemoScenario** = 1, **Purchase tickets for events at all venues** (comprar entradas para eventos de todas las ubicaciones).
2. Presione **F5** para ejecutar el script y generar datos de ventas de entradas. Mientras se ejecuta el script, siga con los pasos de este tutorial. Los datos de entradas se consultan en la sección *Ejecución de consultas distribuidas ad hoc*, de modo que espere a que el generador de entradas finalice.

## <a name="explore-the-global-views"></a>Exploración de las vistas globales

La aplicación SaaS de Wingtip se compila siguiendo un modelo de inquilino por cada base de datos, por lo que el esquema de base de datos de inquilino se define desde la perspectiva del inquilino único. Existe información específica del inquilino en una tabla, *Venue*, que siempre tiene una sola fila y, además, se ha diseñado como montón, sin clave principal. No es necesario que otras tablas del esquema estén relacionadas con la tabla *Venue*, ya que, en el uso normal, no hay nunca duda de a qué inquilino pertenecen los datos.

Sin embargo, al consultar en todas las bases de datos, es importante que la consulta elástica pueda tratar los datos como si formaran parte de una sola base de datos lógica compartida por el inquilino. Para simular este patrón, se agrega un conjunto de vistas 'globales' a la base de datos de inquilino que proyectan un identificador de inquilino en cada una de las tablas que se consultan globalmente. Por ejemplo, la vista *VenueEvents* agrega un identificador *VenueId* calculado a las columnas que se proyectan desde la tabla *Events*. Al definir la tabla externa en la base de datos principal con la tabla *VenueEvents* (en lugar de con la tabla subyacente *Events*), la consulta elástica puede transferir las combinaciones basadas en el identificador *VenueId* de modo que se pueden ejecutar en paralelo en cada base de datos remota (en lugar de en la base de datos principal). Así se reduce considerablemente la cantidad de datos que se devuelven, lo que da como resultado un aumento sustancial del rendimiento para muchas consultas. Estas vistas globales se han creado previamente en todas las bases de datos de inquilino (y en *basetenantdb*).

1. Abra SSMS y [conéctese al servidor tenants1-&lt;USUARIO&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Expanda **Bases de datos**, haga clic con el botón derecho en **contosoconcerthall** y seleccione **Nueva consulta**.
3. Ejecute las siguientes consultas para explorar la diferencia entre las tablas de inquilino único y las vistas globales:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

En estas vistas, el identificador *VenueId* se ha calculado como un valor hash del nombre de Venue, pero se puede usar cualquier estrategia para introducir un valor único. Esta estrategia es similar a cómo se calcula la clave de inquilino para usarse en el catálogo.

Para examinar la definición de la vista *Venues*:

1. En **Explorador de objetos**, expanda **contosoconcerthall** > **Vistas**:

   ![vistas](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. Haga clic con el botón derecho en **dbo.Venues**.
3. Seleccione **Incluir vista como** > **CREATE To** > **Nueva ventana del Editor de consultas**.

Incluya en el script cualquiera de las otras vistas *Venue* para ver cómo agregan el identificador *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Implementación de la base de datos usada para las consultas distribuidas ad hoc

En este ejercicio se implementa la base de datos *adhocanalytics*. Esta es la base de datos principal que contendrá el esquema utilizado para realizar consultas en todas las bases de datos de inquilino. La base de datos se implementa en el servidor de catálogo existente, que es el utilizado para todas las bases de datos relacionadas con la administración en la aplicación de ejemplo.

1. Abra ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* en *PowerShell ISE* y establezca los valores siguientes:
   * **$DemoScenario** = 2, **Implementar una base de datos de análisis ad hoc**.

2. Presione **F5** para ejecutar el script y crear la base de datos *adhocanalytics*.

En la siguiente sección, se agrega el esquema a la base de datos de modo que se pueda usar para ejecutar consultas distribuidas.

## <a name="configure-the-database-for-running-distributed-queries"></a>Configuración de la base de datos para ejecutar consultas distribuidas

En este ejercicio se agrega el esquema (el origen de datos externo y las definiciones de tabla externas) a la base de datos de análisis ad hoc que permite realizar consultas en todas las bases de datos de inquilino.

1. Abra SQL Server Management Studio y conéctese a la base de datos de análisis ad hoc que creó en el paso anterior. El nombre de la base de datos será *adhocanalytics*.
2. Abra ...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql* en SSMS.
3. Revise el script SQL y tenga en cuenta lo siguiente:

   La consulta elástica usa una credencial de ámbito de base de datos para acceder a cada una de las bases de datos de inquilino. Esta credencial debe estar disponible en todas las bases de datos y normalmente se le deberían conceder los derechos mínimos necesarios para permitir estas consultas ad hoc.

    ![crear credencial](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   El origen de datos externo, que se define para usar el mapa de particiones de inquilino en la base de datos de catálogo. Al utilizarlo como origen de datos externo, las consultas se distribuyen a todas las bases de datos registradas en el catálogo cuando se ejecuta la consulta. Dado que los nombres de servidor son diferentes para cada implementación, este script de inicialización obtiene la ubicación de la base de datos de catálogo mediante la recuperación del servidor actual (@@servername) donde se ejecuta el script.

    ![crear origen de datos externos](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   Las tablas externas que hacen referencia a las vistas globales descritas en la sección anterior y definidas con **DISTRIBUTION = SHARDED(VenueId)**. Dado que cada identificador *VenueId* se asigna a una sola base de datos, así se mejora el rendimiento para muchos escenarios, como se muestra en la sección siguiente.

    ![crear tablas externas](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   La tabla local *VenueTypes* que se crea y rellena. Esta tabla de datos de referencia es común en todas las bases de datos de inquilino, por lo que se puede representar como una tabla local y rellenarse con los datos comunes. En algunas consultas, así se puede reducir la cantidad de datos movidos entre las bases de datos de inquilino y la base de datos *adhocanalytics*.

    ![crear tabla](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   Si incluye tablas de referencia de esta manera, asegúrese de actualizar el esquema de tabla y los datos cada vez que actualice las bases de datos de inquilino.

4. Presione **F5** para ejecutar el script e inicializar la base de datos *adhocanalytics*. 

Ahora puede ejecutar consultas distribuidas y recopilar información de todos los inquilinos.

## <a name="run-ad-hoc-distributed-queries"></a>Ejecución de consultas distribuidas ad hoc

Ahora que la base de datos *adhocanalytics* está configurada, continúe y ejecute algunas consultas distribuidas. Incluya el plan de ejecución para comprender mejor dónde sucede el procesamiento de las consultas. 

Al inspeccionar el plan de ejecución, mantenga el mouse sobre los iconos de plan para obtener más información. 

Es importante tener en cuenta que la configuración **DISTRIBUTION = SHARDED(VenueId)** cuando se define el origen de datos externo, mejora el rendimiento en muchos escenarios. Dado que cada identificador *VenueId* se asigna a una sola base de datos, el filtrado se lleva a cabo fácilmente de forma remota, y devuelve solo los datos que se necesitan.

1. Abra ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* en SSMS.
2. Asegúrese de que está conectado a la base de datos **adhocanalytics**.
3. Seleccione el menú **Consulta** y haga clic en **Incluir plan de ejecución real**.
4. Resalte la consulta *Which venues are currently registered?* (¿Qué ubicaciones están registradas actualmente?) y presione **F5**.

   La consulta devuelve la lista de ubicaciones completa e ilustra lo rápido y fácil que es realizar consultas en todos los inquilinos y devolver los datos de cada uno.

   Inspeccione el plan y compruebe que el costo íntegro es la consulta remota porque simplemente vamos a cada base de datos de inquilino y seleccionamos la información de las ubicaciones.

   ![SELECT * FROM dbo.Venues](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. Seleccione la siguiente consulta y presione **F5**.

   Esta consulta combina datos de las bases de datos de inquilino y de la tabla local *VenueTypes* (local porque es una tabla de la base de datos *adhocanalytics*).

   Inspeccione el plan y compruebe que la mayor parte del costo es la consulta remota, ya que se consulta la información de ubicación de cada inquilino (dbo.Venues) y, a continuación, se realiza una combinación rápida local con la tabla local *VenueTypes* para mostrar el nombre descriptivo.

   ![Combinación de datos remotos y locales](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. Ahora seleccione la consulta *On which day were the most tickets sold?* (¿Qué día se vendieron más entradas?) y presione **F5**.

   Esta consulta hace una combinación y una agregación un poco más complejas. Es importante tener en cuenta que la mayor parte del procesamiento se realiza de forma remota y, una vez más, se devuelven solo las filas que se necesitan, una única fila para el recuento total diario de ventas de entradas de cada ubicación.

   ![query](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Ejecutar consultas distribuidas en todas las bases de datos de inquilinos
> * Implementar una base de datos de análisis ad hoc y agregarle el esquema para ejecutar consultas distribuidas.


Ahora pruebe el [tutorial de análisis de inquilinos](sql-database-saas-tutorial-tenant-analytics.md) para explorar la extracción de datos en una base de datos de análisis independiente para llevar a cabo un procesamiento de análisis más complejo.

## <a name="additional-resources"></a>Recursos adicionales

* Otros [tutoriales basados en la aplicación SaaS de Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)
