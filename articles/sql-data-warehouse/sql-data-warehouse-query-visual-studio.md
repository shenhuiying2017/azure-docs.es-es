---
title: Realización de consultas en Almacenamiento de datos SQL de Azure (Visual Studio) | Microsoft Docs
description: Realización de consultas en Almacenamiento de datos SQL con Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/16/2016
ms.author: sonyama;barbkess

---
# Realización de consultas en Almacenamiento de datos SQL de Azure (Visual Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizaje automático de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> 
> 

Utilice Visual Studio para realizar consultas en Almacenamiento de datos SQL de Azure en unos minutos. Este método usa la extensión SQL Server Data Tools (SSDT) de Visual Studio.

## Requisitos previos
Para utilizar este tutorial, necesitará:

* Una cuenta de Almacenamiento de datos SQL existente. Para crear una, consulte [Creación de una instancia de Almacenamiento de datos SQL de Azure][Creación de una instancia de Almacenamiento de datos SQL de Azure].
* SSDT para Visual Studio. Si tiene Visual Studio, probablemente ya tenga este componente. Para ver instrucciones y opciones de instalación, consulte [Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL][Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL].
* El nombre del servidor SQL completo. Para encontrarlo, consulte [Connect to Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse] \(Conexión a Almacenamiento de datos SQL).

## 1\. Conexión a la instancia de Almacenamiento de datos SQL
1. Abra Visual Studio 2013 o 2015
2. Abra el Explorador de objetos de SQL Server. Para ello, seleccione **Ver** > **Explorador de objetos de SQL Server**.
   
    ![Explorador de objetos de SQL Server][1]
3. Haga clic en el botón **Agregar SQL Server**.
   
    ![Agregar SQL Server][2]
4. Rellene los campos en la ventana Conectar al servidor.
   
    ![Conectar al servidor][3]
   
   * **Nombre del servidor**. Escriba el **nombre del servidor** definido anteriormente.
   * **Autenticación**. Seleccione **Autenticación de SQL Server** o **Autenticación integrada de Active Directory**.
   * **Nombre de usuario** y **Contraseña**. Escriba el nombre de usuario y la contraseña si la autenticación de SQL Server se seleccionó anteriormente.
   * Haga clic en **Conectar**.
5. Para explorar, expanda su Azure SQL Server. Puede ver las bases de datos asociadas al servidor. Expanda AdventureWorksDW para ver las tablas de la base de datos de ejemplo.
   
    ![Explorar AdventureWorksDW][4]

## 2\. Ejecución de una consulta de ejemplo
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

## Pasos siguientes
Ahora que puede conectarse y realizar consultas, pruebe a [visualizar los datos con PowerBI][visualizar los datos con PowerBI].

Para configurar el entorno para la autenticación de Azure Active Directory, consulte [Authentication to Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] \(Autenticación en Almacenamiento de datos SQL de Azure).

<!--Arcticles-->
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Creación de una instancia de Almacenamiento de datos SQL de Azure]: sql-data-warehouse-get-started-provision.md
[Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL]: sql-data-warehouse-install-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizar los datos con PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png

<!---HONumber=AcomDC_0622_2016-->