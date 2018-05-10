---
title: Restauración de una copia de seguridad a Instancia administrada de Azure SQL Database | Microsoft Docs
description: Restaure una copia de seguridad de datos a una instancia administrada de Azure SQL Database mediante SSMS.
keywords: sql database tutorial, sql database managed instance, restore a backup
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Restauración de una copia de seguridad de datos a una instancia administrada de Azure SQL Database

En este tutorial se muestra cómo restaurar una copia de seguridad de una base de datos almacenada en Azure Blob Storage en la instancia administrada mediante el archivo de copia de seguridad Wide World Importers - Standard. Este método requiere cierto tiempo de inactividad. Para consultar un tutorial con Azure Database Migration Service (DMS) para la migración, consulte [Migración a Instancia administrada con DMS](../dms/tutorial-sql-server-to-managed-instance.md). Para ver una descripción de los diversos métodos de migración, consulte [Migración de una instancia de SQL Server a Instancia administrada de Azure SQL Database](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * Descarga del archivo de copia de seguridad de Wide World Importers: estándar
> * Creación de cuenta de almacenamiento de Azure y carga del archivo de copia de seguridad
> * Restauración de la base de datos de Wide World Importers desde un archivo de copia de seguridad

## <a name="prerequisites"></a>requisitos previos

En este tutorial se parte de los recursos creados en el tutorial [Create an Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) (Creación de una instancia administrada de Azure SQL Database).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Descarga del archivo de copia de seguridad de Wide World Importers: estándar

Utilice los pasos siguientes para descargar el archivo de copia de seguridad Wide World Importers: estándar.

Mediante Internet Explorer, escriba https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak en el cuadro de direcciones URL y, cuando se le solicite, haga clic en **Guardar** para guardar el archivo en la carpeta **Descargas**.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Creación de cuenta de almacenamiento de Azure y carga del archivo de copia de seguridad

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque **Storage** y haga clic en **Cuenta de almacenamiento** para abrir el formulario de cuenta de almacenamiento.

   ![Cuenta de almacenamiento](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Rellene el formulario de la cuenta de almacenamiento con la información solicitada, utilizando los datos de la siguiente tabla:

   | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Modelo de implementación**|Modelo de recursos||
   |**Tipo de cuenta**|Almacenamiento de blobs||
   |**Rendimiento**|Estándar o premium|Unidades magnéticas o SSD|
   |**Replicación**|Almacenamiento con redundancia local||
   |**Nivel de acceso (predeterminado)|Estático o Dinámico||
   |**Se requiere transferencia segura**|Disabled||
   |**Suscripción**|Su suscripción|Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions).|
   |**Grupos de recursos**|El grupo de recursos que ha creado anteriormente|| 
   |**Ubicación**|La ubicación que ha seleccionado anteriormente||
   |**Redes virtuales**|Disabled||

4. Haga clic en **Create**(Crear).

   ![detalles de la cuenta de almacenamiento](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. Después de que la implementación de la cuenta de almacenamiento se haya realizado correctamente, abra la nueva cuenta de almacenamiento.
6. En **Configuración**, haga clic en **Firma de acceso compartido** para abrir el formulario de SAS correspondiente.

   ![formulario de sas](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. En el formulario de SAS, modifique los valores predeterminados según sea necesario. Observe que, de forma predeterminada, la fecha y hora de expiración es de solo 8 horas.
8. Haga clic en **Generar SAS**.

   ![formulario de sas completado](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Copie y guarde el **token de SAS** y la **dirección URL de SAS de servidor de blobs**.
10. En **Configuración**, haga clic en **Contenedores**.

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. Haga clic en **+ Contenedor** para crear un contenedor para almacenar el archivo de copia de seguridad.
12. Rellene el formulario del contenedor con la información solicitada, utilizando los datos de la siguiente tabla:

    | Configuración| Valor sugerido | Descripción |
   | ------ | --------------- | ----------- |
   |**Name**|Cualquier nombre válido|Para conocer cuáles son los nombres válidos, consulte el artículo [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nivel de acceso público**|Privado (sin acceso anónimo)||

    ![detalle de contenedor](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Haga clic en **OK**.
14. Una vez creado el contenedor, ábralo.

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. Haga clic en **Propiedades del contenedor** y, después, copie la dirección URL para el contenedor.

    ![URL del contenedor](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Haga clic en **Cargar** para abrir el formulario **Cargar blob**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Busque la carpeta de descarga y seleccione el archivo **WideWorldIimporters Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Haga clic en **Cargar**.
19. No continúe hasta que la carga esté completa.

    ![carga completa](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Restauración de la base de datos de Wide World Importers desde un archivo de copia de seguridad

Con SSMS, siga estos pasos para restaurar la base de datos de Wide World Importers en la instancia administrada desde el archivo de copia de seguridad.

1. En SSMS, abra una nueva ventana de consulta.
2. Utilice el siguiente script para crear una credencial SAS: proporcione la dirección URL del contenedor de la cuenta de almacenamiento y la clave SAS como se indica.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. Utilice el siguiente script para crear la comprobación de la credencial SAS y la validez de la copia de seguridad; y proporcione la dirección URL del contenedor con el archivo de copia de seguridad:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![lista de archivos](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Utilice el siguiente script para restaurar la base de datos Adventure Works 2012 de una copia de seguridad; y proporcione la dirección URL del contenedor con el archivo de copia de seguridad:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![ejecución de la restauración](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Para realizar un seguimiento del estado de la restauración, ejecute la consulta siguiente en una nueva sesión de consulta:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![porcentaje de restauración completado](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Cuando se completa la restauración, se puede ver en el Explorador de objetos. 

    ![restauración completa](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió a restaurar una copia de seguridad de una base de datos almacenada en Azure Blob Storage en la instancia administrada mediante el archivo de copia de seguridad Wide World Importers - Standard. Ha aprendido a: 

> [!div class="checklist"]
> * Descarga del archivo de copia de seguridad de Wide World Importers: estándar
> * Creación de cuenta de almacenamiento de Azure y carga del archivo de copia de seguridad
> * Restauración de la base de datos de Wide World Importers desde un archivo de copia de seguridad

Avance al siguiente tutorial para saber cómo migrar de SQL Server a Instancia administrada de Azure SQL Database mediante DMS.

> [!div class="nextstepaction"]
>[Migración de SQL Server a Instancia administrada de Azure SQL Database mediante DMS](../dms/tutorial-sql-server-to-managed-instance.md)