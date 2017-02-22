---
title: "Azure Portal: exportación de una base de datos Azure SQL Database a un archivo BACPAC | Microsoft Docs"
description: "Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal

En este artículo encontrará instrucciones para exportar la base de datos Azure SQL Database a un archivo BACPAC (almacenado en Azure Blob Storage) mediante [Azure Portal](https://portal.azure.com). Para obtener información general de la exportación a un archivo BACPAC, consulte [Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal](sql-database-export.md).

> [!NOTE]
> También puede exportar su archivo de Azure SQL Database a un archivo BACPAC mediante [SQL Server Management Studio](sql-database-export-ssms.md), [PowerShell](sql-database-export-powershell.md) o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Requisitos previos

Para completar este artículo, necesitará lo siguiente:

* Una suscripción de Azure.
* Una Base de datos SQL de Azure. 
* Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC.

## <a name="export-your-database"></a>Exportación de la base de datos

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Abra la hoja de Base de datos SQL correspondiente a la base de datos que desea exportar.
3. Asegúrese de que no se realicen transacciones durante la exportación. 

   > [!IMPORTANT]
   > Para garantizar un archivo BACPAC transaccionalmente coherente, se recomienda [crear una copia de la base de datos](sql-database-copy.md) y después exportar dicha copia. 
   > 

4. Haga clic en **Bases de datos SQL**.
5. Haga clic en la base de datos que quiera archivar.
6. En la hoja de SQL Database, haga clic en **Exportar** para abrir la hoja **Exportar base de datos**:
   
   ![botón Exportar][1]
7. Haga clic en **Almacenamiento** y seleccione su cuenta de almacenamiento y el contenedor de blobs donde desea guardar el archivo BACPAC:
   
   ![Exportar base de datos][2]
8. Seleccione el tipo de autenticación. 
9. Escriba las credenciales de autenticación correspondientes al servidor SQL de Azure que contiene la base de datos que va a exportar.
10. Haga clic en **Crear** para exportar la base de datos. Al hacer clic en **Aceptar** , se crea una solicitud de base de datos de exportación y se envía al servicio. El tiempo que tarde la exportación dependerá del tamaño y la complejidad de la base de datos y de su nivel de servicio. 
11. Vea la notificación recibirá.
   
   ![notificación de exportación][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Supervisar el progreso de la operación de exportación
1. Haga clic en **Servidores SQL Server**.
2. Haga clic en el servidor que contiene la base de datos original (de origen) que ha archivado.
3. Vaya a Operaciones.
4. En la hoja del servidor SQL, haga clic en **Historial de importación y exportación**:
   
   ![importar historial de exportación][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Comprobar que el BACPAC está en su contenedor de almacenamiento
1. Haga clic en **Cuentas de almacenamiento**.
2. Haga clic en la cuenta de almacenamiento donde ha guardado el archivo BACPAC.
3. Haga clic en **Contenedores** y seleccione el contenedor en el que exportó la base de datos para obtener detalles (puede descargar y guardar el archivo BACPAC desde aquí).
   
   ![detalles de archivo .bacpac][5]    

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información acerca de la conservación de copias de seguridad a largo plazo de una copia de seguridad de Azure SQL Database como una alternativa a la exportación de una base de datos para archivarla, consulte [Retención a largo plazo](sql-database-long-term-retention.md).
* Para obtener información sobre cómo importar un BACPAC a una base de datos de SQL Server, consulte [Importación de un BACPCAC en una Base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


