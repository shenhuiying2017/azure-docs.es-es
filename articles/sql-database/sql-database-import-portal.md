---
title: "Importación de un archivo BACPAC para crear una base de datos de Azure SQL | Microsoft Docs"
description: Cree una base de datos SQL de Azure importando un archivo BACPAC ya existente.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Importación de un archivo BACPAC para crear una base de datos de Azure SQL Database mediante Azure Portal

En este artículo se proporcionan instrucciones para crear una base de datos SQL de Azure a partir de un archivo BACPAC mediante [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Requisitos previos

Para importar una instancia de SQL Database de un BACPAC, necesita lo siguiente:

* Una suscripción de Azure. 
* Servidor V12 de Base de datos SQL de Azure. Si no tiene un servidor V12, siga los pasos de este artículo para crear uno: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
* Un archivo .bacpac de la base de datos que quiere importar en un contenedor de blobs de [cuenta de almacenamiento de Azure (estándar)](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> Al importar un archivo BACPAC a Almacenamiento de blobs de Azure, utilice el almacenamiento estándar. No se admite la importación de un archivo BACPAC de Almacenamiento premium.
> 

## <a name="import-the-database"></a>Importar la base de datos
Abra la hoja SQL Server:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Servidores SQL Server**.
3. Haga clic en el servidor en el que restaurará la base de datos.
4. En la hoja de SQL Server, haga clic en **Importar base de datos** para abrir la hoja **Importar base de datos**:
   
   ![Importar base de datos][1]
5. Haga clic en **Storage** y seleccione su cuenta de almacenamiento, el contenedor de blobs y el archivo .bacpac, y haga clic en **Aceptar**.
   
   ![configurar opciones de almacenamiento][2]
6. Seleccione el nivel de precios para la nueva base de datos y haga clic en **Seleccionar**. No se admite la importación de una base de datos directamente en un grupo elástico, pero puede importarla primero como una base de datos única y luego mover la base de datos a un grupo.
   
   ![seleccione nivel de precios][3]
7. Escriba un **NOMBRE DE BASE DE DATOS** para la base de datos que va a crear a partir del archivo BACPAC.
8. Elija el tipo de autenticación y, luego, proporcione la información de autenticación del servidor. 
9. Haga clic en **Crear** para crear la base de datos desde el BACPAC.
   
   ![crear base de datos][4]

Al hacer clic en **Crear** se envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

## <a name="monitor-the-progress-of-the-import-operation"></a>Supervisar el progreso de la operación de importación
1. Haga clic en **Servidores SQL Server**.
2. Haga clic en el servidor en el que va a restaurar.
3. En la hoja del servidor SQL, área Operaciones, haga clic en **Historial de importación y exportación**:
   
   ![importar historial de exportación][5]
   ![importar historial de exportación][6]

4. Para comprobar que la base de datos está activa en el servidor, haga clic en **Bases de datos SQL** y compruebe que la nueva base de datos esté **En línea**.

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a conectarse a una base de datos de SQL Database importada y realizar consultas en ella, consulte [Conexión a SQL Database con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo](sql-database-connect-query-ssms.md).
* Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.
* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

