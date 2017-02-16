---
title: "Azure Portal: exportación de una base de datos Azure SQL Database a un archivo BACPAC | Microsoft Docs"
description: "Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 12/20/2016
ms.author: sstein;carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c9aea5089d535b63983b93076edd74f15055fa5d
ms.openlocfilehash: 4eb89c2e6db82ba887a094abf53da835060368b9


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

En este artículo encontrará instrucciones para exportar la base de datos Azure SQL Database a un archivo BACPAC (almacenado en Azure Blob Storage) mediante [Azure Portal](https://portal.azure.com).

Cuando necesite crear un archivo de una base de datos de SQL Azure, puede exportar los datos y el esquema de base de datos a un archivo BACPAC. Un archivo BACPAC es, sencillamente, un archivo ZIP con la extensión BACPAC. Este archivo BACPAC se puede guardar en el almacenamiento de blobs de Azure o en cualquier almacenamiento en una ubicación local y, luego, importarlo a Base de datos SQL de Azure o a una instalación local de SQL Server. 

> [!IMPORTANT]
> La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier base de datos SQL. Los trabajos de exportación automatizada existentes continuarán funcionando hasta que el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) o [Azure Automation](../automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este que está disponible en Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="considerations"></a>Consideraciones

* Para que un archivo sea transaccionalmente coherente, debe asegurarse de que no haya ninguna actividad de escritura durante la exportación, o bien de exportar desde una [copia transaccionalmente coherente](sql-database-copy.md) de Base de datos SQL de Azure.
* El tamaño máximo de un archivo BACPAC guardado en el almacenamiento de blobs de Azure es de 200 GB. Use la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para guardar archivos BACPAC de mayor tamaño en un almacenamiento local. Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.
* No se puede archivar en Almacenamiento premium de Azure usando un archivo BACPAC.
* Si la operación de exportación tarda más de 20 horas, es posible que se cancele. Para aumentar el rendimiento durante la exportación, puede hacer lo siguiente:
  * Aumentar temporalmente el nivel de servicio.
  * Detener toda actividad de lectura y escritura durante la exportación.
  * Use un [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) con valores distintos de NULL en todas las tablas de gran tamaño. Sin índices agrupados, la exportación podría no producirse si tarda más de 6-12 horas. Esto se debe a que el servicio de exportación necesita completar el recorrido de tabla para tratar de exportar toda la tabla. Una buena forma de determinar si las tablas están optimizadas para la exportación es ejecutar **DBCC SHOW_STATISTICS** y asegurarse de que *RANGE_HI_KEY* no es NULL y su valor tiene buena distribución. Para obtener más información, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).  
> 

Para completar este artículo, necesitará lo siguiente:

* Una suscripción de Azure.
* Una Base de datos SQL de Azure. 
* Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC.

## <a name="export-your-database"></a>Exportación de la base de datos
Abra la hoja de Base de datos SQL correspondiente a la base de datos que desea exportar.

> [!IMPORTANT]
> Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](sql-database-copy.md) y, después, exportar dicha copia. 
> 
> 

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Bases de datos SQL**.
3. Haga clic en la base de datos que quiera archivar.
4. En la hoja de SQL Database, haga clic en **Exportar** para abrir la hoja **Exportar base de datos**:
   
   ![botón Exportar][1]
5. Haga clic en **Almacenamiento** y seleccione su cuenta de almacenamiento y el contenedor de blobs donde desea guardar el archivo BACPAC:
   
   ![Exportar base de datos][2]
6. Seleccione el tipo de autenticación. 
7. Escriba las credenciales de autenticación correspondientes al servidor SQL de Azure que contiene la base de datos que va a exportar.
8. Haga clic en **Aceptar** para archivar la base de datos. Al hacer clic en **Aceptar** , se crea una solicitud de base de datos de exportación y se envía al servicio. El tiempo que tarde la exportación dependerá del tamaño y la complejidad de la base de datos y de su nivel de servicio. Vea la notificación recibirá.
   
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
* Para obtener información sobre cómo importar un BACPAC a una base de datos SQL de Azure, consulte [Importación de un BACPCAC en una Base de datos SQL de Azure](sql-database-import.md)
* Para obtener información sobre cómo importar un BACPAC a una base de datos de SQL Server, consulte [Importación de un BACPCAC en una Base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Jan17_HO4-->


