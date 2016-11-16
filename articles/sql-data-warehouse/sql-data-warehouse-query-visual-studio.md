---
title: "Realización de consultas en Azure SQL Data Warehouse (Visual Studio) | Microsoft Docs"
description: Consultas en Almacenamiento de datos SQL con Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e97fefdf3cc13f2fd3e060da901e90ef5ef9a29


---
# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Realización de consultas en Almacenamiento de datos SQL de Azure (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizaje automático de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilice Visual Studio para realizar consultas en Almacenamiento de datos SQL de Azure en unos minutos. Este método usa la extensión SQL Server Data Tools (SSDT) de Visual Studio. 

## <a name="prerequisites"></a>Requisitos previos
Para utilizar este tutorial, necesitará:

* Una cuenta de Almacenamiento de datos SQL existente. Para crear una, consulte [Creación de una instancia de SQL Data Warehouse][Creación de una instancia de SQL Data Warehouse].
* SSDT para Visual Studio. Si tiene Visual Studio, probablemente ya tenga este componente. Para ver instrucciones y opciones de instalación, consulte [Instalación de Visual Studio y SSDT][Instalación de Visual Studio y SSDT].
* El nombre del servidor SQL completo. Para encontrarlo, consulte [Connect to Azure SQL Data Warehouse (Conexión a Azure SQL Data Warehouse)][Connect to SQL Data Warehouse] (Conexión a Azure SQL Data Warehouse).

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conexión a la instancia de Almacenamiento de datos SQL
1. Abra Visual Studio 2013 o 2015
2. Abra el Explorador de objetos de SQL Server. Para ello, seleccione **Ver** > **Explorador de objetos de SQL Server**.
   
    ![Explorador de objetos de SQL Server][1]
3. Haga clic en el botón **Agregar SQL Server** .
   
    ![Agregar SQL Server][2]
4. Rellene los campos en la ventana Conectar al servidor.
   
    ![Conectar al servidor][3]
   
   * **Nombre del servidor**. Escriba el **nombre del servidor** definido anteriormente.
   * **Autenticación**. Seleccione **Autenticación de SQL Server** o **Autenticación integrada de Active Directory**.
   * **Nombre de usuario** y **Contraseña**. Escriba el nombre de usuario y la contraseña si la autenticación de SQL Server se seleccionó anteriormente.
   * Haga clic en **Conectar**.
5. Para explorar, expanda su Azure SQL Server. Puede ver las bases de datos asociadas al servidor. Expanda AdventureWorksDW para ver las tablas de la base de datos de ejemplo.
   
    ![Explorar AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Ejecución de una consulta de ejemplo
Ahora que se ha establecido una conexión a la base de datos, pasemos a escribir una consulta.

1. Haga clic con el botón derecho en la base de datos en el Explorador de objetos de SQL Server.
2. Seleccione **Nueva consulta**. Se abre una nueva ventana de consulta.
   
    ![Nueva consulta][5]
3. Copie esta consulta TSQL en la ventana de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Ejecute la consulta. Para hacerlo, haga clic en la flecha verde o use la combinación de teclas `CTRL`+`SHIFT`+`E`.
   
    ![Ejecutar consulta][6]
5. Consulte los resultados de la consulta. En este ejemplo, la tabla FactInternetSales tiene 60398 filas.
   
    ![Resultados de la consulta][7]

## <a name="next-steps"></a>Pasos siguientes
Ahora que puede conectarse y realizar consultas, pruebe a [visualizar los datos con PowerBI][visualizar los datos con PowerBI].

Para configurar el entorno para la autenticación de Azure Active Directory, consulte [Authentication to Azure SQL Data Warehouse (Autenticación en SQL Data Warehouse)][Authenticate to SQL Data Warehouse] (Autenticación en SQL Data Warehouse).

<!--Arcticles-->
[Conexión a Almacenamiento de datos SQL]: sql-data-warehouse-connect-overview.md
[Creación de una instancia de Almacenamiento de datos SQL de Azure]: sql-data-warehouse-get-started-provision.md
[Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL]: sql-data-warehouse-install-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizar los datos con PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Portal de Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png



<!--HONumber=Nov16_HO2-->


