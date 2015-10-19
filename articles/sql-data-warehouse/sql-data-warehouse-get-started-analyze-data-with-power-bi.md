<properties
    pageTitle="Análisis de datos de Almacenamiento de datos SQL con Power BI | Microsoft Azure"
    description="Análisis de datos de Almacenamiento de datos SQL con Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Análisis de datos con Power BI
Este tutorial muestra cómo usar Power BI para conectar con Almacenamiento de datos SQL y crear unas visualizaciones básicas.

> [AZURE.NOTE]Para completar este tutorial, necesita una base de datos de Almacenamiento de datos SQL que se haya cargado previamente con la base de datos de ejemplo AdventureWorksDW. [Creación de una nueva base de datos de Almacenamiento de datos SQL](sql-data-warehouse-get-started-provision.md) le muestra cómo crear una.
> 
> Si ya tiene una base de datos de Almacenamiento de datos SQL pero no tiene datos de ejemplo, puede [cargar manualmente los datos de ejemplo][].


## Conexión a la base de datos de Almacenamiento de datos SQL

Para abrir Power BI y conectarse a la base de datos AdventureWorksDW:

1. Inicie sesión en el [Portal de vista previa de Azure][].
2. Haga clic en **Bases de datos SQL** y elija su base de datos de Almacenamiento de datos SQL de AdventureWorks. 

    ![Buscar la base de datos][1]

3. Haga clic en el botón "Abrir en Power BI".

    ![Botón Power BI][2]

4. Deberá ver la página de conexión de Almacenamiento de datos SQL que muestra la información de su base de datos. Escriba la contraseña y se conectará completamente a la base de datos de Almacenamiento de datos SQL.


## Análisis de datos con Power BI

Ahora está listo para usar Power BI para analizar los datos de ejemplo AdventureWorksDW. Para realizar el análisis, AdventureWorksDW tiene una vista denominada AggregateSales. Esta vista contiene algunas de las métricas clave para analizar las ventas de la empresa.

1. Para crear una asignación del importe de ventas según el código postal, haga clic en la vista AggregateSales para expandirla y, a continuación, haga clic en las columnas PostalCode y SalesAmount. Power BI reconoce automáticamente estos datos como geográficos y los coloca en un mapa.

    ![Mapa Power BI][3]

2. Para crear un gráfico de barras de las ventas, haga clic en la columna SalesAmount. Para agregar detalles, arrastre el gráfico "CustomerIncome" al campo "Axis" a la izquierda de "AggregateSales" para mostrar las ventas por ingresos de cliente.

    ![Barra Power BI][4]

3. Para crear una escala de tiempo de ventas, haga clic en "SalesAmount", "OrderDate" y "Line Chart", que es el primer icono de la segunda línea debajo de "Visualizations" (visualizaciones).

    ![LíneaPower BI][5]

Puede guardar el progreso cuando lo desee,no tiene más que hacer clic en el botón **SAVE** (GUARDAR) en la esquina superior izquierda y guardar las visualizaciones como un informe.

## Pasos siguientes
Ahora que le hemos dado algún tiempo para familiarizarse con los datos de ejemplo, consulte cómo puede [desarrollar][], [cargar][] o [migrar][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[cargar manualmente los datos de ejemplo]: sql-data-warehouse-get-started-manually-load-samples.md
[Portal de vista previa de Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->