---
title: Aprovisionamiento de Integration Runtime de SSIS de Azure con el portal | Microsoft Docs
description: "En este artículo se explica cómo crear una instancia de Integration Runtime de SSIS de Azure con Azure Portal."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Aprovisionamiento de Integration Runtime de SSIS de Azure con la interfaz de usuario de Data Factory
En este tutorial se describen los pasos necesarios para usar Azure Portal para aprovisionar Integration Runtime de SSIS de Azure en Azure Data Factory. Posteriormente, podrá usar SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar paquetes de SQL Server Integration Services (SSIS) en este entorno de ejecución de Azure. Para obtener información conceptual acerca de Integration Runtime de SSIS de Azure, consulte [Introducción a Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

En este tutorial realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación e inicio de un entorno de ejecución para la integración de SSIS de Azure

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>requisitos previos
- **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar. 
- **Servidor de Azure SQL Database**. Si aún no tiene ningún servidor de base de datos, cree uno en Azure Portal antes de empezar a trabajar. Azure Data Factory crea la base de datos de catálogo de SSIS (SSISDB) en este servidor de bases de datos. Le recomendamos que cree el servidor de base de datos en la misma región de Azure que la instancia de Integration Runtime. Esta configuración permite que la instancia de Integration Runtime escriba registros de ejecución en SSISDB sin traspasar regiones de Azure. 
    - Confirme que el valor "**Permitir el acceso a servicios de Azure**" está activado para el servidor de bases de datos. Para más información, consulte [Protección de Azure SQL Database](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar este valor mediante PowerShell, consulte [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Agregue la dirección IP del equipo cliente o un intervalo de direcciones IP que incluya la dirección IP del equipo cliente a la lista de direcciones IP de cliente en la configuración del firewall del servidor de bases de datos. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Confirme que el servidor de Azure SQL Database no tiene un catálogo de SSIS (base de datos SSIDB). El aprovisionamiento del entorno de ejecución de integración de Azure-SSIS no admite el uso de un catálogo de SSIS existente.
 
## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).    
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, escriba **MyAzureSsisDataFactory** en el **nombre**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameMyAzureSsisDataFactory) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista solo se muestran las ubicaciones que se admiten para la creación de factorías de datos.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionamiento de Integration Runtime de SSIS de Azure

1. En la página de introducción, haga clic en el icono **Configure SSIS Integration Runtime** (Configuración de Integration Runtime de SSIS). 

   ![Icono Configure SSIS Integration Runtime (Configuración de Integration Runtime de SSIS)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. En la página **General Settings** (Configuración general) de **Integration Runtime Setup** (Configuración de Integration Runtime), realice los pasos siguientes: 

   ![Configuración general](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Especifique un **nombre** para la instancia de Integration Runtime.
    2. Seleccione la **ubicación** para la instancia de Integration Runtime. Se muestran solo las ubicaciones admitidas.
    3. Seleccione el **tamaño del nodo** que se va a configurarse con la instancia de IR de SSIS.
    4. Especifique el **número de nodos** del clúster.
    5. Haga clic en **Next**. 
1. En **SQL Settings** (Configuración de SQL), realice los pasos siguientes: 

    ![Configuración de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Especifique la **suscripción** de Azure que tiene el servidor de Azure SQL. 
    2. Seleccione el servidor de Azure SQL Server de **Catalog Database Server Endpoint** (Punto de conexión del servidor de bases de datos de catálogo).
    3. Escriba el nombre de usuario del **administrador**.
    4. Escriba la **contraseña** del administrador.  
    5. Seleccione el **nivel de servicio** de la base de datos SSISDB. El valor predeterminado es Básico.
    6. Haga clic en **Next**. 
1.  En la página **Advanced Settings** (Configuración avanzada), seleccione un valor para **Maximum Parallel Executions Per Node** (Máximo de ejecuciones paralelas por nodo).   

    ![Configuración avanzada](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Este paso es **opcional**. Si tiene una red virtual clásica (VNet) a la que desee unir Integration Runtime, seleccione la opción **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Seleccionar una red virtual a la que unir IR de SSIS de Azure y permitir que los servicios de Azure configuren los permisos y la configuración de la red) y realice los pasos siguientes: 

    ![Configuración avanzada con red virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Especifique la **suscripción** de la red virtual clásica. 
    2. Seleccione la **red virtual**. <br/>
    4. Seleccione la **subred**.<br/> 
1. Haga clic en **Finish** (Finalizar) para iniciar la creación la instancia de Integration Runtime de SSIS de Azure. 

    > [!IMPORTANT]
    > - Este proceso tarda aproximadamente veinte minutos.
    > - El servicio Data Factory se conecta a Azure SQL Database para preparar la base de datos de catálogo de SSIS (SSISDB). El script también configura los permisos y la configuración de la red virtual, si se especifica, y une la nueva instancia de Integration Runtime de SSIS de Azure a la red virtual.
    > - Al aprovisionar una instancia de SQL Database para hospedar SSISDB, también se instalan el paquete de características de Azure para SSIS y el acceso redistribuible. Estos componentes proporcionan conectividad a los archivos de Excel y Access y a diversos orígenes de datos de Azure, además de a los orígenes de datos admitidos por los componentes integrados. No puede instalar componentes de terceros para SSIS en este momento (incluidos los componentes de terceros de Microsoft, como los componentes de Oracle y Teradata de Attunity y los componentes de inteligencia empresarial de SAP).

7. En la ventana **Connections**, cambie a **Integration Runtime** si es necesario. Haga clic en **Refresh** (Actualizar) para actualizar el estado. 

    ![Estado de la creación](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use los vínculos de la columna **Actions** (Acciones) para supervisar, detener, iniciar, editar o eliminar la instancia de Integration Runtime. Utilice el último vínculo para ver el código JSON de Integration Runtime. Los botones de edición y eliminación se habilitan únicamente cuando la instancia de IR se detiene. 

    ![IR de SSIS de Azure: acciones](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Haga clic en **Monitor** (Supervisar) de **Actions** (Acciones).  

    ![IR de SSIS de Azure: detalles](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Si se ha producido un **error** asociado con IR de SSIS de Azure, consulte los errores de esta página y el vínculo para ver sus detalles. Por ejemplo, si el catálogo de SSIS ya existe en el servidor de bases de datos, verá un error que indica la existencia de la base de datos SSISDB.  
11. Haga clic en **Integration Runtime** de la parte superior para volver a la página anterior y ver todas las instancias de Integration Runtime asociadas a la factoría de datos.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Instancias de Integration Runtime de SSIS de Azure en el portal

1. En la interfaz de usuario de Azure Data Factory, cambie a la pestaña **Edit** (Editar), haga clic en **Connections** (Conexiones) y cambie a la pestaña **Integration Runtime** para ver las instancias de Integration Runtime existentes en la factoría de datos. 
    ![Visualización de las instancias de IR existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Haga clic en **New** (Nuevo) para crear una instancia de IR de SSIS de Azure. 

    ![Integration Runtime a través del menú](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Para crear una instancia de Integration Runtime de SSIS de Azure, haga clic en **New** (Nuevo) tal como se muestra en la imagen. 
3. En la ventana de configuración de Integration Runtime, seleccione **Lift-and-shift existing SSIS packages to execute in Azure** (Migrar los paquetes de SSIS existentes mediante lift-and-shift para la ejecución en Azure) y haga clic en **Next** (Siguiente).

    ![Especificación del tipo de instancia de Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consulte la sección [Aprovisionamiento de Integration Runtime de SSIS de Azure](#provision-an-azure-ssis-integration-runtime) para el resto de pasos de configuración de IR de SSIS de Azure. 

 
## <a name="deploy-ssis-packages"></a>Implementación de paquetes SSIS
Luego, use SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) para implementar los paquetes de SSIS en Azure. Conéctese a la instancia de SQL Server de Azure que hospeda el catálogo de SSIS (SSISDB). El nombre del servidor de Azure SQL Server tiene el siguiente formato: `<servername>.database.windows.net` (para Azure SQL Database). 

Consulte los siguientes artículos de la documentación de SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Implementación, ejecución y supervisión de un paquete SSIS en Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Conexión al catálogo de SSIS en Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Programación de la ejecución de un paquete en Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 

## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación e inicio de un entorno de ejecución para la integración de SSIS de Azure

Pase al tutorial siguiente para obtener información sobre cómo copiar datos desde el entorno local a la nube: 

> [!div class="nextstepaction"]
>[Copia de datos en la nube](tutorial-copy-data-portal.md)
