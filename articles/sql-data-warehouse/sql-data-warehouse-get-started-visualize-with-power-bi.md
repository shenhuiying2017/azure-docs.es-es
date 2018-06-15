---
title: Visualización de datos de SQL Data Warehouse con Power BI | Microsoft Azure
description: Visualización de datos de SQL Data Warehouse con Power BI
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601584"
---
# <a name="visualize-data-with-power-bi"></a>Visualización de datos con Power BI
Este tutorial muestra cómo usar Power BI para conectar con SQL Data Warehouse y crear unas visualizaciones básicas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>requisitos previos
Para seguir paso a paso este tutorial, necesita:

* Una instancia de SQL Data Warehouse cargada previamente con la base de datos de AdventureWorksDW. Para aprovisionar un almacenamiento de datos, consulte [Creación de una instancia de Azure SQL Data Warehouse](create-data-warehouse-portal.md) y seleccione la opción para cargar los datos de ejemplo. Si ya tiene un almacenamiento de datos pero no tiene datos de ejemplo, puede [cargar WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Conectarse a la base de datos
Para abrir Power BI y conectarse a la base de datos AdventureWorksDW:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **SQL Database** y elija su base de datos de SQL Data Warehouse de AdventureWorks.
   
    ![Buscar la base de datos](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Haga clic en el botón "Abrir en Power BI".
   
    ![Botón Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Deberá ver la página de conexión de SQL Data Warehouse que muestra la dirección web de la base de datos. Haga clic en Siguiente.
   
    ![Conexión de Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Escriba el nombre de usuario y la contraseña de Azure SQL Server.
   
    ![Inicio de sesión de Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Para abrir la base de datos, haga clic en el conjunto de datos AdventureWorksDW en la hoja izquierda.
   
    ![Apertura de AdventureWorksDW en Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Creación de un informe
Ahora está listo para usar Power BI para analizar los datos de ejemplo AdventureWorksDW. Para realizar el análisis, AdventureWorksDW tiene una vista denominada AggregateSales. Esta vista contiene algunas de las métricas clave para analizar las ventas de la empresa.

1. Para crear una asignación del importe de ventas según el código postal, en los campos de la derecha, haga clic en la vista AggregateSales para expandirla. A continuación, haga clic en las columnas PostalCode y SalesAmount para seleccionarlas.
   
    ![Selección de AggregateSales en Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI reconoce automáticamente los datos geográficos y los coloca en un mapa.
   
    ![Mapa Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. Este paso crea un gráfico de barras que muestra la cantidad de ventas por los ingresos del cliente. Para crear el gráfico de barras, vaya a la vista expandida de AggregateSales. Haga clic en el campo SalesAmount. Arrastre el campo de ingresos de los clientes hacia la izquierda y colóquelo en el eje.
   
    ![Selección del eje en Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    El gráfico de barras de la izquierda.
   
    ![Barra Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. Este paso crea un gráfico de líneas que muestra el importe de ventas por fecha de pedido. Para crear el gráfico de línaes, vaya a la vista expandida de AggregateSales. Haga clic en SalesAmount y OrderDate. En la columna Visualizaciones, haga clic en el icono de gráfico de líneas, que es el primer icono de la segunda línea bajo las visualizaciones.
   
    ![Selección del gráfico de líneas en Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Ahora tiene un informe que muestra tres visualizaciones de los datos diferentes.
   
    ![LíneaPower BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Para guardar el progreso en cualquier momento, haga clic en **Archivo** y seleccione **Guardar**.

## <a name="using-direct-connnect"></a>Uso de Conexión directa
De manera similar a lo que sucede con Azure SQL Database, Conexión directa de SQL Data Warehouse permite aprovechar la aplicación lógica junto con las funcionalidades analíticas de Power BI. Con Conexión directa, las consultas se envían de vuelta a la instancia de Azure SQL Data Warehouse en tiempo real mientras explora los datos.  Esta característica, en combinación con la escala de SQL Data Warehouse, permite crear, en minutos, informes dinámicos sobre terabytes de datos. Además, la introducción del botón Abrir en Power BI permite que los usuarios conecten directamente Power BI a su instancia de SQL Data Warehouse sin recopilar información de otras partes de Azure.

Cuando use Conexión directa, tenga en cuenta lo siguiente:

* Cuando se conecte, especifique el nombre completo del servidor.
* Asegúrese de que las reglas del firewall de la base de datos estén configuradas para permitir el acceso a los servicios de Azure.
* Cada acción, como seleccionar una columna o agregar un filtro, envia directamente una consulta al almacenamiento de datos.
* Los iconos se actualizan automáticamente cada 15 minutos aproximadamente.
* La sección de Preguntas y respuestas no está disponible para los conjuntos de datos de Conexión directa.
* Los cambios en el esquema se incorporan automáticamente.
* Todas las consultas de Conexión directa agotarán el tiempo de espera al cabo de 2 minutos.

Estas restricciones y notas pueden cambiar a medida que mejoran las experiencias.

## <a name="next-steps"></a>Pasos siguientes
Ahora que le hemos dado algún tiempo para preparar los datos de ejemplo, consulte cómo los puede [desarrollar](sql-data-warehouse-overview-develop.md), [cargar](design-elt-data-loading.md) o [migrar](sql-data-warehouse-overview-migrate.md). También puede visitar el [sitio web de Power BI](http://www.powerbi.com/).
