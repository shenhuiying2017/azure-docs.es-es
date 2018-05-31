---
title: Uso de DMS para migrar una Instancia administrada de Azure SQL Database | Microsoft Docs
description: En este artículo, se describe información sobre cómo migrar bases de datos de instancias locales de SQL Server a Instancia administrada de Azure SQL Database mediante Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: bb7cc17c36809975e26c8da8beda004a0b0cfd9e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774155"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migración de SQL Server a Instancia administrada de Azure SQL Database mediante DMS
Puede usar Azure Database Migration Service para migrar las bases de datos de una instancia de SQL Server local a una [Instancia administrada de Azure SQL Database](../sql-database/sql-database-managed-instance.md). Para ver otros métodos que requieren realizar acciones manuales, consulte [Migración de una instancia de SQL Server a la Instancia administrada de Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Los proyectos de migración de SQL Server a la Instancia administrada de Azure SQL Database se encuentran en versión preliminar y están sujetos a los [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este tutorial, migrará la base de datos **Adventureworks2012** desde una instancia local de SQL Server a una Instancia administrada de Azure SQL Database mediante Azure Database Migration Service.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración mediante Azure Database Migration Service.
> * Ejecutar la migración.
> * Supervisar la migración
> * Descargar un informe de migración.

## <a name="prerequisites"></a>requisitos previos
Para completar este tutorial, necesita:

- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Conozca las topologías de red para migrar a Instancia administrada de Azure SQL Database con Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Asegúrese de que el grupo de seguridad de red de Azure Virtual Network (VNET) no bloquea los puertos de comunicación 443, 53, 9354, 445 y 12000. Para obtener información más detallada sobre el filtrado de tráfico con NSG de Azure VNET, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configure el [Firewall de Windows para acceder al motor de base de datos de origen](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra el Firewall de Windows para permitir que Azure Database Migration Service acceda a la instancia de SQL Server de origen que, de manera predeterminada, es el puerto TCP 1433.
- Si se ejecutan varias instancias con nombre de SQL Server con puertos dinámicos, puede que quiera habilitar el servicio SQL Browser y permitir el acceso al puerto UDP 1434 a través de los firewalls para que Azure Database Migration Service pueda conectarse a una instancia con nombre en el servidor de origen.
- Si va a usar un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración, así como archivos a través del puerto SMB 445.
- Cree una Instancia administrada de Azure SQL Database mediante los pasos que se describen en el artículo [Create an Azure SQL Database Managed Instance in the Azure portal](https://aka.ms/sqldbmi) (Creación de una instancia administrada de Azure SQL Database en Azure Portal).
- Asegúrese de que los inicios de sesión usados para conectar la instancia de SQL Server de origen y la instancia administrada de destino son miembros del rol de servidor sysadmin.
- Cree un recurso compartido de red que pueda usar Azure Database Migration Service para hacer copia de seguridad de la base de datos de origen.
- Asegúrese de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tiene privilegios de escritura en el recurso compartido de red que haya creado, y que la cuenta del equipo del servidor de origen tiene acceso de lectura y escritura para el mismo recurso compartido.
- Anote un usuario de Windows (y su contraseña) que tenga privilegios de control total sobre el recurso compartido de red que creó anteriormente. Azure Database Migration Service suplanta la credencial de usuario para cargar los archivos de copia de seguridad en el contenedor de Azure Storage para la operación de restauración.
- Cree un contenedor de blobs y recupere su URI de SAS mediante los pasos del artículo [Administración de recursos Azure Blob Storage con el Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), asegúrese de seleccionar todos los permisos (lectura, escritura, eliminación, lista) en la ventana de directiva al crear el URI de SAS. Estos detalles proporcionan a Azure Database Migration Service acceso al contenedor de cuentas de almacenamiento para cargar los archivos de copia de seguridad que se usaron en la migración de bases de datos a la Instancia administrada de Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration

1. Inicie sesión en Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.

    ![Mostrar suscripciones en el portal](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.

    ![Mostrar los proveedores de recursos](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Busque la migración y después, a la derecha de **Microsoft.DataMigration**, seleccione **Registrar**.

    ![Registro del proveedor de recursos](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Creación de una instancia de Azure Database Migration Service

1. En Azure Portal, seleccione **+ Crear un recurso**, busque **Azure Database Migration Service** y, a continuación, seleccione **Azure Database Migration Service** en la lista desplegable.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. En la pantalla **Azure Database Migration Service**, seleccione **Crear**.

    ![Creación de una instancia de Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. En la pantalla **Crear el servicio de migración**, especifique un nombre para el servicio, la suscripción y un grupo de recursos nuevo o existente.

4. Seleccione una red virtual (VNET) existente o cree una nueva.
 
    La VNET proporciona a Azure Database Migration Service acceso a la instancia de origen de SQL Server y a la Instancia administrada de Azure SQL Database.

    Para obtener más información sobre cómo crear una VNET en Azure Portal, consulte el artículo [Create a virtual network using the Azure portal](https://aka.ms/DMSVnet) (Crear una red virtual con Azure Portal).

    Para obtener detalles adicionales, consulte el artículo [Topologías de red para migraciones a la Instancia administrada de Azure SQL Database con Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

5. Seleccione un plan de tarifa.

    Para más información sobre los costos y planes de tarifa, vea la [página de precios](https://aka.ms/dms-pricing).
   
    ![Creación de servicio DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Seleccione **Crear** para crear el servicio.

## <a name="create-a-migration-project"></a>Creación de un proyecto de migración

Después de crear el servicio, búsquelo en Azure Portal, ábralo y cree un proyecto de migración.

1. En Azure Portal, seleccione **Todos los servicios**, busque Azure Database Migration Service y, luego, elija **Azure Database Migration Services**.

    ![Búsqueda de todas las instancias de Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. En la pantalla **Azure Database Migration Services**, busque el nombre de la instancia creada y, a continuación, elija esa instancia.
 
3. Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).

4. En la pantalla **New Migration Project** (Nuevo proyecto de migración), especifique un nombre para el proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server** y, luego, en el cuadro de texto **Target server type** (Tipo de servidor de destino), elija **Instancia administrada de Azure SQL Database**.

   ![Creación de proyecto DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Seleccione **Crear** para crear el proyecto.

## <a name="specify-source-details"></a>Especificación de los detalles de origen

1. En la pantalla **Detalles del origen**, especifique los detalles de conexión de la instancia de SQL Server de origen.

2. Si no ha instalado ningún certificado de confianza en el servidor, seleccione la casilla **Certificado de servidor de confianza**.

    Si no hay ningún certificado de confianza instalado, SQL Server genera un certificado autofirmado cuando se inicia la instancia. Este certificado se usa para cifrar las credenciales de las conexiones del cliente.

    > [!CAUTION]
    > Las conexiones SSL cifradas mediante un certificado autofirmado no proporcionan una gran seguridad. Son susceptibles de sufrir ataques de tipo "Man in the middle". No debe confiar en SSL mediante certificados autofirmados en un entorno de producción o en servidores que están conectados a Internet.

   ![Detalles del origen](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Seleccione **Guardar**.

4. En la pantalla **Seleccionar las bases de datos de origen**, seleccione la base de datos **Adventureworks2012** para la migración.

   ![Selección de las bases de datos de origen](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Seleccione **Guardar**.

## <a name="specify-target-details"></a>Especificación de los detalles de destino

1.  En la pantalla **Detalles del destino**, especifique los detalles de conexión del destino, que es la Instancia administrada de Azure SQL Database que se aprovisionó previamente, y a la que se migrará la base de datos **AdventureWorks2012**.

    Si no proporcionó la Instancia administrada de Azure SQL Database, seleccione **No** para obtener un vínculo que le ayudará a aprovisionar la instancia. Aún así, puede continuar con la creación del proyecto y, a continuación, cuando la Instancia administrada de Azure SQL Database esté lista, regrese a este proyecto específico para ejecutar la migración.   
 
       ![Selección del destino](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Seleccione **Guardar**.

3.  En la pantalla **Resumen del proyecto**, revise y compruebe los detalles relacionados con el proyecto de migración.
 
    ![Resumen del proyecto de migración](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Seleccione **Guardar**.   

## <a name="run-the-migration"></a>Ejecución de la migración

1.  Seleccione el proyecto guardado recientemente, elija **+ Nueva actividad** y, a continuación, seleccione **Ejecutar la migración**.

    ![Creación de nueva actividad](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Cuando se le solicite, escriba las credenciales de los servidores de origen y de destino y, luego, seleccione **Guardar**.

3.  En la pantalla **Seleccionar las bases de datos de origen**, seleccione las bases de datos de origen que quiera migrar.

    ![Selección de las bases de datos de origen](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Seleccione **Guardar** y, en la pantalla **Configure migration settings** (Configurar valores de migración), proporcione los detalles siguientes:

    | | |
    |--------|---------|
    |**Recurso compartido de la ubicación de red** | El recurso compartido de red local que pueda usar Azure Database Migration Service para hacer copias de seguridad de la base de datos de origen. La cuenta de servicio que ejecuta la instancia de SQL Server de origen debe tener privilegios de escritura en este recurso compartido de red. Proporcione un FQDN o direcciones IP del servidor en el recurso compartido de red como, por ejemplo, "\\\servername.domainname.com\backupfolder" o "\\\IP address\backupfolder".|
    |**Nombre de usuario** | El nombre de usuario de Windows que Azure Database Migration Service puede suplantar para cargar los archivos de copia de seguridad en el contenedor de almacenamiento de Azure para la operación de restauración. |
    |**Password** | Contraseña del usuario. |
    |**Configuración de cuentas de almacenamiento** | Identificador URI de SAS que proporciona a Azure Database Migration Service acceso al contenedor de la cuenta de almacenamiento en el que el servicio carga los archivos de copia de seguridad y que se usa para la migración de bases de datos a la Instancia administrada de Azure SQL Database. [Más información sobre cómo obtener el identificador URI de SAS para el contenedor de blobs](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Configuración de valores de migración](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Seleccione **Guardar** y, en la pantalla **Migration summary** (Resumen de migración), en el cuadro de texto **Nombre de actividad**, especifique un nombre para la actividad de migración.

6. Expanda la sección **Opción de validación** para que se muestre la pantalla **Elegir la opción de validación**, en la que se especifica si quiere validar las bases de datos migradas para la exactitud de la consulta y, a continuación, seleccione **Guardar**.  

    ![Resumen de migración](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Seleccione **Ejecutar migración**.

    Aparecerá la ventana de actividad de migración y el estado de la actividad será **Pendiente**.

   ![Actividad de migración pendiente](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>Supervisión de la migración

1. En la pantalla de la actividad de migración, seleccione **Actualizar** para actualizar la pantalla hasta que vea que el estado de las migraciones aparezca como **Completado**.
 
   ![Actividad de migración completada](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. Una vez concluida la migración, seleccione **Descargar informe** para obtener un informe que muestre los detalles relacionados con el proceso de migración.
 
3. Compruebe la base de datos de destino en el entorno de destino de la Instancia administrada de Azure SQL Database.

## <a name="next-steps"></a>Pasos siguientes

- Para consultar un tutorial que muestra cómo migrar una base de datos a una Instancia administrada mediante el comando T-SQL RESTORE, consulte [Restore a backup to a Managed Instance using the restore command](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) (Restauración de una copia de seguridad para una Instancia administrada mediante el comando Restore).
- Para más información acerca de Instancia administrada, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](../sql-database/sql-database-managed-instance.md).
- Para obtener más información sobre cómo conectar las aplicaciones a una Instancia administrada, consulte [Conexión de aplicaciones](../sql-database/sql-database-managed-instance-connect-app.md).
