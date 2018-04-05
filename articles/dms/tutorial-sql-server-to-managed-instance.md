---
title: Uso de Azure Database Migration Service para migrar bases de datos de SQL Server a Instancia administrada de SQL Database | Microsoft Docs
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
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Migración de SQL Server a Instancia administrada de Azure SQL Database
Puede usar Azure Database Migration Service para migrar las bases de datos de una instancia de SQL Server local a Azure SQL Database. En este tutorial, migrará la base de datos **Adventureworks2012** desde una instancia local de SQL Server a una instancia de Azure SQL Database mediante Azure Database Migration Service.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración mediante Azure Database Migration Service.
> * Ejecutar la migración.
> * Supervisar la migración

## <a name="prerequisites"></a>requisitos previos
Para completar este tutorial, necesita:

- Crear una red virtual para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local utilizando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Conozca las topologías de red para migrar a Instancia administrada de Azure SQL Database con Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Asegúrese de que el grupo de seguridad de red de Azure Virtual Network (VNET) no bloquea los puertos de comunicación 443, 53, 9354, 445 y 12000. Para obtener información más detallada sobre el filtrado de tráfico con NSG de Azure VNET, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Configure el [Firewall de Windows para acceder al motor de base de datos de origen](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra el Firewall de Windows para permitir que Azure Database Migration Service acceda a la instancia de SQL Server de origen que, de manera predeterminada, es el puerto TCP 1433.
- Si se ejecutan varias instancias con nombre de SQL Server con puertos dinámicos, puede que quiera habilitar el servicio SQL Browser y permitir el acceso al puerto UDP 1434 a través de los firewalls para que Azure Database Migration Service pueda conectarse a una instancia con nombre en el servidor de origen.
- Si va a usar un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración, así como archivos a través del puerto SMB 445.
- Cree una instancia de Instancia administrada de Azure SQL Database mediante los pasos que se describen en el artículo [Creación de una instancia de Instancia administrada de Azure SQL Database en Azure Portal](https://aka.ms/sqldbmi).
- Asegúrese de que los inicios de sesión usados para conectar la instancia de SQL Server de origen y la instancia administrada de destino son miembros del rol de servidor sysadmin.
- Cree un recurso compartido de red que pueda usar Azure Database Migration Service para hacer copia de seguridad de la base de datos de origen.
- Asegúrese de que la cuenta de servicio que ejecuta la instancia de SQL Server de origen tenga privilegios de escritura sobre el recurso compartido de red que ha creado.
- Anote un usuario de Windows (y una contraseña) que tenga privilegio de control total sobre el recurso compartido de red que creó anteriormente. Azure Database Migration Service suplanta la credencial de usuario para cargar los archivos de copia de seguridad en el contenedor de Azure Storage para la operación de restauración.
- Cree un contenedor de blobs y recupere su URI de SAS mediante los pasos del artículo [Administración de recursos Azure Blob Storage con el Explorador de Storage (versión preliminar)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), asegúrese de seleccionar todos los permisos (lectura, escritura, eliminación, lista) en la ventana de directiva al crear el URI de SAS. Esto proporciona a Azure Database Migration Service acceso al contenedor de cuentas de almacenamiento para cargar los archivos de copia de seguridad usados para la migración de bases de datos a Instancia administrada de Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registro del proveedor de recursos Microsoft.DataMigration

1.  Inicie sesión en Azure Portal, seleccione **Todos los servicios** y, después, **Suscripciones**.
![Mostrar suscripciones en el portal](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Seleccione la suscripción en la que quiere crear la instancia de Azure Database Migration Service y después seleccione **Proveedores de recursos**.
![Mostrar los proveedores de recursos](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Busque la migración y después, a la derecha de **Microsoft.DataMigration**, seleccione **Registrar**.
![Registrar proveedor de recursos](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creación de una instancia

1.  En Azure Portal, seleccione **+ Crear un recurso**, busque **Azure Database Migration Service** y, luego, seleccione **Azure Database Migration Service** en la lista desplegable.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  En la pantalla **Azure Database Migration Service (preview)** [Azure Database Migration Service (versión preliminar)], seleccione **Crear**.

    ![Creación de una instancia de Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  En la pantalla **Database Migration Service**, especifique un nombre para el servicio, la suscripción, el grupo de recursos, una red virtual y el plan de tarifa.

    Para más información sobre los costos y planes de tarifa, vea la [página de precios](https://aka.ms/dms-pricing). *Azure Database Migration Service está actualmente en vista previa y no se le cobrará.*

    **Red:** seleccione una existente o cree una nueva red virtual que proporcione a Azure Database Migration Service acceso a la instancia de SQL Server de origen y a la instancia de destino de Instancia administrada de Azure SQL Database. [Conozca las topologías de red para migrar a Instancia administrada de Azure SQL Database con Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    Para más información sobre cómo crear la red virtual en Azure Portal, consulte [Creación de una red virtual con varias subredes mediante Azure Portal](https://aka.ms/DMSVnet).

    ![Creación de servicio DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Seleccione **Crear** para crear el servicio.


## <a name="create-a-migration-project"></a>Creación de un proyecto de migración

Después de crear el servicio, búsquelo en Azure Portal y ábralo.

1.  Seleccione **+ New Migration Project** (+ Nuevo proyecto de migración).

1.  En la pantalla **New Migration Project** (Nuevo proyecto de migración), especifique un nombre para el proyecto. En el cuadro de texto **Source server type** (Tipo de servidor de origen), seleccione **SQL Server** y, luego, en el cuadro de texto **Target server type** (Tipo de servidor de destino), elija **Instancia administrada de Azure SQL Database**.

    ![Creación de proyecto DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Seleccione **Crear** para crear el proyecto.

## <a name="specify-source-details"></a>Especificación de los detalles de origen

1.  En la pantalla **Detalles del origen**, especifique los detalles de conexión de la instancia de SQL Server de origen.

    ![Detalles del origen](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Seleccione **Guardar** y, luego, elija la base de datos **Adventureworks2012** para la migración.

    ![Selección de las bases de datos de origen](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Especificación de los detalles de destino

1.  Seleccione **Guardar** y, luego, en la pantalla **Detalles del destino**, especifique los detalles de conexión del destino, que es la instancia de Instancia administrada de SQL Database que se aprovisionó previamente, a la que se migrará la base de datos **AdventureWorks2012**.

    ![Selección del destino](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Seleccione **Guardar**.

1.  En la pantalla **Resumen del proyecto**, revise y compruebe los detalles relacionados con el proyecto de migración.

## <a name="run-the-migration"></a>Ejecución de la migración

1.  Seleccione el proyecto guardado recientemente, elija **+ Nueva actividad** y, luego, seleccione **Ejecutar la migración**.

    ![Creación de nueva actividad](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Cuando se le solicite, escriba las credenciales de los servidores de origen y de destino y, luego, seleccione **Guardar**.

1.  En la pantalla **Map to target databases** (Asignar a bases de datos de destino), seleccione las bases de datos de origen que desea migrar.

    ![Selección de las bases de datos de origen](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Seleccione **Guardar** y, en la pantalla **Configure migration settings** (Configurar valores de migración), proporcione los detalles siguientes:

    | | |
    |--------|---------|
    |**Ubicación de la copia de seguridad del servidor** | El recurso compartido de red local que pueda usar Azure Database Migration Service para hacer copias de seguridad de la base de datos de origen. La cuenta de servicio que ejecuta la instancia de SQL Server de origen debe tener privilegios de escritura en este recurso compartido de red. |
    |**Nombre de usuario** | El nombre de usuario de Windows que Azure Database Migration Service puede suplantar para cargar los archivos de copia de seguridad en el contenedor de almacenamiento de Azure para la operación de restauración. |
    |**Password** | Contraseña del usuario anterior. |
    |**Identificador URI de SAS de Storage** | Identificador URI de SAS que proporciona a Azure Database Migration Service acceso al contenedor de la cuenta de almacenamiento en el que el servicio carga los archivos de copia de seguridad y que se usa para la migración de bases de datos a Instancia administrada de Azure SQL Database. [Más información sobre cómo obtener el identificador URI de SAS para el contenedor de blobs](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Configuración de valores de migración](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Seleccione **Guardar**, en la pantalla Migration summary (Resumen de migración) y, en el cuadro de texto **Nombre de actividad**, especifique un nombre para la actividad de migración.

    ![Resumen de migración](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Supervisión de la migración

1.  Seleccione la actividad de migración para revisar el estado de la actividad.

1.  Una vez completada la migración, compruebe las bases de datos de destino en Instancia administrada de Azure SQL Database.

    ![Supervisión de la migración](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

