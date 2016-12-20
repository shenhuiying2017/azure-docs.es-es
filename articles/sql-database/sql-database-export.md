---
title: Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure
description: Archivo de una Azure SQL Database en un archivo BACPAC mediante Azure Portal
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 035a4b394c446d3b92e17ec6d938690504f463c5
ms.openlocfilehash: 8fbc4febad665d66c857876eb60f0165c5fc5c8e


---
# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Archivar una base de datos de SQL Azure en un archivo BACPAC mediante el Portal de Azure
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

En este artículo encontrará instrucciones para archivar la base de datos SQL de Azure en un archivo BACPAC (guardado en el almacenamiento de blobs de Azure) usando el [Portal de Azure](https://portal.azure.com).

Cuando necesite crear un archivo de una base de datos de SQL Azure, puede exportar los datos y el esquema de base de datos a un archivo BACPAC. Un archivo BACPAC es, sencillamente, un archivo ZIP con la extensión BACPAC. Este archivo BACPAC se puede guardar en el almacenamiento de blobs de Azure o en cualquier almacenamiento en una ubicación local y, luego, importarlo a Base de datos SQL de Azure o a una instalación local de SQL Server. 

***Consideraciones***

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
> 

Para completar este artículo, necesitará lo siguiente:

* Una suscripción de Azure.
* Una Base de datos SQL de Azure. 
* Una [cuenta de almacenamiento estándar de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC.

## <a name="export-your-database"></a>Exportación de la base de datos
Abra la hoja de Base de datos SQL correspondiente a la base de datos que desea exportar.

> [!IMPORTANT]
> Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](sql-database-copy.md) y después exportar dicha copia. 
> 
> 

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Bases de datos SQL**.
3. Haga clic en la base de datos que quiera archivar.
4. En la hoja de SQL Database, haga clic en **Exportar** para abrir la hoja **Exportar base de datos**:
   
   ![botón Exportar][1]
5. Haga clic en **Almacenamiento** y seleccione su cuenta de almacenamiento y contenedor de blobs donde se almacenará el archivo BACPAC:
   
   ![Exportar base de datos][2]
6. Seleccione el tipo de autenticación. 
7. Escriba las credenciales de autenticación correspondientes al servidor SQL de Azure que contiene la base de datos que va a exportar.
8. Haga clic en **Aceptar** para archivar la base de datos. Al hacer clic en **Aceptar** , se crea una solicitud de base de datos de exportación y se envía al servicio. El tiempo que tarde la exportación dependerá del tamaño y la complejidad de la base de datos y de su nivel de servicio. Recibirá una notificación.
   
   ![notificación de exportación][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Supervisar el progreso de la operación de exportación
1. Haga clic en **Servidores SQL Server**.
2. Haga clic en el servidor que contiene la base de datos original (de origen) que acaba de archivar.
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




<!--HONumber=Nov16_HO3-->


