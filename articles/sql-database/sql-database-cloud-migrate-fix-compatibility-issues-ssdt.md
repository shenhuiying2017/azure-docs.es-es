---
title: "SSDT: Corrección de problemas con la migración de compatibilidad de Azure SQL Database | Microsoft Docs"
description: "En este artículo, aprenderá a detectar y corregir problemas de compatibilidad con bases de datos de SQL Server mediante SQL Server Data Tools para Visual Studio antes de la migración a Base de datos SQL de Azure."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure, SSDT"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7ff52877-5b63-4adc-aa1a-689669a1146e
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: ea4248a5ff16e6dd8e754aa0f1be480d08aa47f3


---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>Migración de una base de datos SQL Server a Base de datos SQL de Azure mediante SQL Server Data Tools para Visual Studio
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

En este artículo, aprenderá a detectar y corregir problemas de compatibilidad con bases de datos de SQL Server mediante SQL Server Data Tools para Visual Studio antes de la migración a Base de datos SQL de Azure.

## <a name="using-sql-server-data-tools-for-visual-studio"></a>Uso de SQL Server Data Tools para Visual Studio
Use SQL Server Data Tools para Visual Studio ("SSDT") para importar el esquema de base de datos a un proyecto de base de datos de Visual Studio para análisis. Para efectuar el análisis, especifique la plataforma de destino del proyecto como Base de datos SQL V12 y, a continuación, compile el proyecto. Si la compilación se realiza correctamente, esto significa que la base de datos es compatible. Si se produce un error en la compilación, puede resolver los errores de SSDT (o una de las demás herramientas descritas en este tema). Una vez compilado correctamente el proyecto, puede publicarlo como una copia de la base de datos de origen. Puede utilizar luego la función de comparación de datos en SSDT para copiar los datos de la base de datos de origen a la base de datos compatible de Azure SQL V12. Luego podrá migrar esta base de datos actualizada. Para usar esta opción, descargue la [versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

> [!NOTE]
> Si solo es necesaria la migración del esquema, es posible publicar el esquema directamente desde Visual Studio a la base de datos SQL de Azure. Use este método cuando el esquema de la base de datos requiera más cambios de los que se pueden controlar mediante el asistente para la migración por sí solo.
> 
> 

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Detección de problemas de compatibilidad con SQL Server Data Tools para Visual Studio
1. Abra el **Explorador de objetos de SQL Server** en Visual Studio. Use **Agregar SQL Server** para conectarse a la instancia de SQL Server que contiene la base de datos que se está migrando. Busque la base de datos en el Explorador de objetos, haga clic con el botón derecho en ella y seleccione **Crear nuevo proyecto...**     
   
   ![Nuevo proyecto](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
2. Configure las opciones de importación para **solo los objetos con ámbito de aplicación de importación**. Desmarque las opciones para importar los inicios de sesión de referencia, permisos y configuraciones de base de datos.    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    
3. Haga clic en **Iniciar** para importar la base de datos y crear el proyecto que contiene un archivo de scripts T-SQL para cada objeto de la base de datos. Los archivos de scripts se anidan en las carpetas del proyecto.    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    
4. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho y seleccione Propiedades. En la página **Configuración del proyecto**, configure la plataforma de destino para Microsoft Azure SQL Database V12.    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
5. Haga clic con el botón derecho en el proyecto y seleccione **Crear** para crear el proyecto.    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
6. La **lista de errores** muestra cada incompatibilidad. En este caso, el nombre de usuario NT AUTHORITY\NETWORK SERVICE es incompatible. Puesto que es incompatible, puede comentarlo o quitarlo (y tratar las implicaciones de eliminar este inicio de sesión y rol de la solución de base de datos).     
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    

## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>Solución de problemas de compatibilidad con SQL Server Data Tools para Visual Studio
1. Haga doble clic en el primer script para abrirlo en una ventana de consulta, coméntelo y, a continuación, ejecute el script.     
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)
2. Repita este proceso para cada script que contenga incompatibilidades hasta que no quede ningún error.    
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
3. Cuando la base de datos no contenga errores, haga clic en el botón derecho en el proyecto y seleccione **Publicar**. Se compila y se publica una copia de la base de datos de origen (se recomienda encarecidamente usar una copia, al menos inicialmente).     
   
   * Antes de realizar la publicación, dependiendo de la versión de SQL Server de origen (anterior a SQL Server 2014), es posible que necesite restablecer la plataforma de destino del proyecto para habilitar la implementación.     
   * Si va a migrar una base de datos de SQL Server anterior, no especifique ninguna característica en el proyecto que no se admita en SQL Server de origen hasta que migre primero la base de datos a una versión más reciente de SQL Server.     
     
     ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
     
     ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
4. En el Explorador de objetos de SQL Server, haga clic en la base de datos de origen y haga clic en **Comparación de datos**. Mediante la comparación del proyecto con la base de datos original comprenderá mejor los cambios realizados por el asistente. Seleccione la versión Azure SQL V12 de la base de datos y, a continuación, haga clic en **Finalizar**.    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    
5. Revise las diferencias detectadas y, a continuación, haga clic en **Actualizar destino** para migrar datos de la base de datos de origen a la base de datos Azure SQL V12.     
   
   ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
6. Elija un método de implementación. Consulte [Migración de una base de datos de SQL Server a una Base de datos SQL en la nube](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


