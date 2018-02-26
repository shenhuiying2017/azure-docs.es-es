---
title: "Aprovisionamiento de una instancia de Integration Runtime para la integración de SSIS con Azure Data Factory | Microsoft Docs"
description: "En este artículo se explica cómo crear una instancia de Integration Runtime para la integración de SSIS en Azure con Azure Data Factory."
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
ms.date: 01/29/2018
ms.author: spelluru
ms.openlocfilehash: 52df89ea8562b343e1bcfb3175016c415e78a1a1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>Aprovisionamiento de una instancia de Integration Runtime para la integración de SSIS en Azure con la interfaz de usuario de Azure Data Factory
En este tutorial se describen los pasos necesarios para usar Azure Portal para aprovisionar Integration Runtime de SSIS de Azure en Azure Data Factory. Posteriormente, puede usar SQL Server Data Tools o SQL Server Management Studio para implementar paquetes de SQL Server Integration Services (SSIS) en este entorno de ejecución de Azure. Para obtener información conceptual acerca de Integration Runtime para la integración de SSIS en Azure, consulte [Introducción a Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).

En este tutorial, va a completar los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Aprovisionamiento de una instancia de Integration Runtime de SSIS en Azure.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de esta versión de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>requisitos previos
- **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. 
- **Servidor de Azure SQL Database**. Si aún no tiene ningún servidor de base de datos, cree uno en Azure Portal antes de empezar a trabajar. Azure Data Factory crea la base de datos del catálogo de SSIS (SSISDB) en este servidor de bases de datos. Le recomendamos que cree el servidor de base de datos en la misma región de Azure que la instancia de Integration Runtime. Esta configuración permite que la instancia de Integration Runtime escriba registros de ejecución en la base de datos SSISDB sin traspasar las regiones de Azure. 
- Confirme que el valor **Permitir el acceso a servicios de Azure** está habilitado para el servidor de bases de datos. Para más información, consulte [Protección de Azure SQL Database](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar este valor mediante PowerShell, consulte [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Agregue la dirección IP de la máquina cliente o un intervalo de direcciones IP que incluya la dirección IP de la máquina cliente en la lista de direcciones IP de cliente en la configuración del firewall del servidor de bases de datos. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
- Confirme que el servidor de Azure SQL Database no tiene un catálogo de SSIS (base de datos SSIDB). El aprovisionamiento de una instancia de Integration Runtime para la integración de SSIS en Azure no admite el uso de un catálogo de SSIS existente.

> [!NOTE]
> - Puede crear una factoría de datos de la versión 2 en las siguientes regiones: Este de EE. UU., Este de EE. UU. 2, Sudeste Asiático y Europa Occidental. 
> - Puede crear una instancia de Integration Runtime de SSIS de Azure en las siguientes regiones: Este de EE. UU., Este de EE. UU. 2, Centro de EE. UU., Europa del Norte, Europa Occidental y Este de Australia. 

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Inicie sesión en el [Azure Portal](https://portal.azure.com/).    
3. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Datos y análisis** y, después, seleccione **Data Factory**. 
   
   ![Selección de la factoría de datos en el panel Nuevo](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, escriba **MyAzureSsisDataFactory** en **Nombre**. 
      
   ![Página de la nueva factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser *único de forma global*. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, **&lt;sunombre&gt;MyAzureSsisDataFactory**) e intente crearla de nuevo. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos. 
5. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista. 
   - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
   Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
6. En **Versión**, seleccione **V2 (versión preliminar)**.
7. En **Ubicación**, seleccione la ubicación de la factoría de datos. En la lista solo se muestran las ubicaciones que se admiten para la creación de factorías de datos.
8. Seleccione **Anclar al panel**.     
9. Seleccione **Crear**.
10. En el panel, verá el icono siguiente con el estado **Deploying data factory** (Implementando factoría de datos): 

   ![Icono "Deploying Data Factory" (Implementando Data Factory)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Una vez completada la creación, verá la página **Data Factory**.
   
   ![Página principal de la factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Haga clic en **Author & Monitor** (Creación y supervisión) para abrir la interfaz de usuario de Data Factory en una pestaña independiente. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprovisionamiento de una instancia de Integration Runtime para la integración de SSIS en Azure

1. En la página **Let's get started** (Comencemos), seleccione el icono **Configure SSIS Integration Runtime** (Configuración de Integration Runtime de SSIS). 

   ![Icono "Configure SSIS Integration Runtime" (Configuración de Integration Runtime de SSIS)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. En la página **General Settings** (Configuración general) de **Integration Runtime Setup** (Configuración de Integration Runtime), realice los pasos siguientes: 

   ![Configuración general](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. En **Name** (Nombre), especifique un nombre para la instancia de Integration Runtime.

   b. En **Location** (Ubicación), seleccione la ubicación para la instancia de Integration Runtime. Se muestran solo las ubicaciones admitidas.

   c. En **Node Size** (Tamaño de nodo), seleccione el tamaño del nodo que se va a configurar con la instancia de Integration Runtime para SSIS.

   d. En **Node Number** (Número de nodos), especifique el número de nodos del clúster.
   
   e. Seleccione **Siguiente**. 
3. En la página **SQL Settings** (Configuración de SQL), realice los pasos siguientes: 

   ![Configuración de SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. En **Subscription** (Suscripción), especifique la suscripción de Azure que tiene el servidor de bases de datos de Azure.

   b. En **Catalog Database Server Endpoint** (Punto de conexión del servidor de bases de datos de catálogo), seleccione el servidor de bases de datos de Azure.

   c. En **Admin Username** (Nombre de usuario de administrador): escriba el nombre de usuario del administrador.

   d. En **Admin Password** (Contraseña de administrador), escriba la contraseña del administrador.

   e. En **Catalog Database Server Tier** (Nivel de servidor de bases de datos de catálogo), seleccione el nivel de servicio para la base de datos SSISDB. El valor predeterminado es **Basic** (Básico).

   f. Seleccione **Siguiente**. 
4. En la página **Advanced Settings** (Configuración avanzada), seleccione un valor para **Maximum Parallel Executions Per Node** (Máximo de ejecuciones paralelas por nodo).   

   ![Configuración avanzada](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Este paso es *opcional*. Si tiene una red virtual (creada a través del modelo de implementación clásica o con Azure Resource Manager) a la que desee unir la instancia de Integration Runtime, seleccione la casilla **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Seleccionar una red virtual a la que unir IR de SSIS de Azure y permitir que los servicios de Azure configuren los permisos y la configuración de la red). Después, complete los siguientes pasos: 

   ![Configuración avanzada con una red virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. En **Subscription** (Suscripción), especifique la suscripción que tiene la red virtual.

   b. En **VNet Name** (Nombre de red virtual), seleccione el nombre de la red virtual.

   c. En **Subnet Name** (Nombre de subred), seleccione el nombre de la subred en la red virtual. 
6. Seleccione **Finish** (Finalizar) para iniciar la creación de la instancia de Integration Runtime para la integración de SSIS en Azure. 

   > [!IMPORTANT]
   > Este proceso tarda aproximadamente veinte minutos.
   >
   > El servicio Data Factory se conecta a la base de datos Azure SQL para preparar el catálogo de SSIS (base de datos SSISDB). El script también configura los permisos y las opciones de la red virtual, si se especifica. Asimismo, une la nueva instancia de Integration Runtime para la integración de SSIS en Azure a la red virtual.
   > 
   > Al aprovisionar una instancia de Integration Runtime para la integración de SSIS en Azure, también se instala el paquete de características de Azure para SSIS y el acceso redistribuible. Estos componentes proporcionan conectividad a los archivos de Excel y Access, y a diversos orígenes de datos de Azure, además de los admitidos por los componentes integrados. No se puede instalar componentes de terceros para SSIS en este momento. Esta restricción incluye a componentes de terceros de Microsoft, como los de Oracle y Teradata de Attunity y los componentes de inteligencia empresarial de SAP.

7. En la pestaña **Connections** (Conexiones), cambie a **Integration Runtimes**, si es necesario. Seleccione **Refresh** (Actualizar) para actualizar el estado. 

   ![Estado de creación, con el botón "Refresh"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use los vínculos de la columna **Actions** (Acciones) para iniciar, detener, iniciar, editar o eliminar la instancia de Integration Runtime. Utilice el último vínculo para ver el código JSON de Integration Runtime. Los botones de edición y eliminación se habilitan únicamente cuando la instancia de IR se detiene. 

   ![Vínculo de la columna "Actions"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Creación de una instancia de Integration Runtime de SSIS de Azure

1. En la interfaz de usuario de Azure Data Factory, cambie a la pestaña **Edit** (Editar), seleccione **Connections** (Conexiones) y cambie a la pestaña **Integration Runtime** para ver las instancias de Integration Runtime existentes en la factoría de datos. 
   ![Opciones para ver las instancias de Integration Runtime existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Seleccione **New** (Nuevo) para crear una instancia de Integration Runtime para la integración de SSIS en Azure. 

   ![Integration Runtime a través del menú](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. En la ventana **Integration Runtime Setup** (Instalación de Integration Runtime), seleccione **Lift-and-shift existing SSIS packages to execute in Azure** (Migrar mediante lift-and-shift los paquetes de SSIS existentes para ejecutarlos en Azure) y, después, seleccione **Siguiente**.

   ![Especificación del tipo de instancia de Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Para el resto de pasos de configuración de Integration Runtime para la integración de SSIS en Azure, consulte la sección [Aprovisionamiento de Integration Runtime de SSIS de Azure](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Implementación de paquetes SSIS
Luego, use SQL Server Data Tools o SQL Server Management Studio para implementar los paquetes de SSIS en Azure. Conéctese al servidor de bases de datos de Azure que hospeda el catálogo de SSIS (base de datos SSISDB). El nombre del servidor de bases de datos de Azure tiene el formato `<servername>.database.windows.net` (para Azure SQL Database). 

Consulte los siguientes artículos de la documentación de SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Implementación, ejecución y supervisión de un paquete SSIS en Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Conexión al catálogo de SSIS en Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Programación de la ejecución de un paquete en Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows) 

## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Aprovisionamiento de una instancia de Integration Runtime de SSIS en Azure.

Para obtener información sobre cómo copiar datos desde el entorno local a la nube, continúe con el tutorial siguiente: 

> [!div class="nextstepaction"]
> [Copia de datos en la nube](tutorial-copy-data-portal.md)
