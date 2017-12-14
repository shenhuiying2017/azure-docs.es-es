---
title: "Tutorial de consulta elástica con Azure SQL Data Warehouse | Microsoft Docs"
description: "Aprenda a usar la consulta elástica con Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: 20bbdbbde7edc4351563685761785874870a3c82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>Configuración de consultas elásticas con SQL Data Warehouse

En este tutorial, aprenderá a usar la consulta elástica para enviar una consulta desde SQL Database hasta SQL Data Warehouse. La consulta elástica es la funcionalidad que existe entre los productos de Azure SQL. Para más información sobre el concepto de consulta elástica, vea [**How to use Elastic Query with SQL Data Warehouse**][How to use Elastic Query with SQL Data Warehouse] (Uso de consultas elásticas con SQL Data Warehouse).

## <a name="prerequisites-for-the-tutorial"></a>Requisitos previos para el tutorial

Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

1. Tener instalado SQL Server Management Studio (SSMS).
2. Haber creado un servidor de Azure SQL con una base de datos y un almacenamiento de base de datos en este servidor.
3. Configurar reglas de firewall para acceder a Azure SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Configurar la conexión entre instancias de SQL Data Warehouse y SQL Database. 

1. Mediante SSMS u otro cliente de consulta, abra una nueva consulta para la base de datos **maestra** en el servidor lógico.

2. Cree un inicio de sesión y un usuario que represente la conexión de la base de datos SQL y el almacenamiento de datos.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Mediante SSMS u otro cliente de consulta, abra una nueva consulta para la **instancia de almacenamiento de datos SQL** en el servidor lógico.

4. Cree un usuario en la instancia de almacenamiento de datos con el inicio de sesión que creó en el paso 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Conceda permisos al usuario del paso 4 que le gustaría que ejecutara en SQL Database. En este ejemplo, solo se concede permiso para seleccionar en un esquema específico, lo que ilustra cómo podemos limitar las consultas de la base de datos SQL a un dominio específico. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Mediante SSMS u otro cliente de consulta, abra una nueva consulta para la **instancia de base de datos SQL** en el servidor lógico.

7. Si aún no tiene una, cree una clave maestra. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Cree una credencial con ámbito de base de datos mediante las credenciales que creó en el paso 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Cree un origen de datos externos que apunte a la instancia de almacenamiento de datos.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Ahora puede crear tablas externas que hagan referencia a este origen de datos externos. Las consultas que usan esas tablas se envían a la instancia de almacenamiento para procesarlas y enviarlas de vuelta a la instancia de base de datos.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Consulta elástica de base de datos SQL a almacenamiento de datos SQL

En los siguientes pasos se creará una tabla en la instancia de almacenamiento de datos con varios valores. Luego, se demostrará cómo configurar una tabla externa para consultar la instancia de almacenamiento de datos desde la instancia de base de datos.

1. Mediante SSMS u otro cliente de consulta, abra una nueva consulta para **SQL Data Warehouse** en el servidor lógico.

2. Envíe la siguiente consulta para crear una tabla **OrdersInformation** en la instancia de almacenamiento de datos.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Mediante SSMS u otro cliente de consulta, abra una nueva consulta para la **base de datos SQL** en el servidor lógico.

4. Envíe la siguiente consulta para crear una definición de tabla externa que apunte a la tabla **OrdersInformation** en la instancia de almacenamiento de datos.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Observe que ahora tiene una definición de tabla externa en la **instancia de base de datos SQL**.

   ![definición de tabla externa de consulta elástica](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Envíe la siguiente consulta, que consulta la instancia del almacenamiento de datos. Debería recibir los cinco valores que insertó en el paso 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Tenga en cuenta que a pesar de los pocos valores, esta consulta tarda bastante tiempo en devolver resultados. Al usar una consulta elástica con el almacenamiento de datos, es necesario considerar los costos generales del procesamiento de las consultas y el movimiento a través de la conexión. Use la ejecución remota de consultas elásticas cuando la eficiencia de los procesos, y no la latencia, sea la prioridad.

Enhorabuena, ha configurado los aspectos básicos de la consulta elástica. 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->
