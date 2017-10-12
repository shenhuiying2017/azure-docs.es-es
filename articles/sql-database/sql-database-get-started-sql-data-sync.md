---
title: "Introducción a SQL Data Sync de Azure (versión preliminar) | Microsoft Docs"
description: "Este tutorial le ayuda a comenzar con Azure SQL Data Sync (versión preliminar)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.openlocfilehash: 1943e87ffd991d099ab655af5a7d16d7f1608d22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Introducción a SQL Data Sync de Azure (vista previa)
En este tutorial, obtendrá información sobre cómo configurar Azure SQL Data Sync mediante la creación de un grupo de sincronización híbrido que contiene instancias de Azure SQL Database y de SQL Server. El nuevo grupo de sincronización está configurado completamente y se sincroniza según el programa establecido.

En este tutorial se asume que tiene al menos alguna experiencia previa con SQL Database y con SQL Server. 

Para obtener información general sobre SQL Data Sync, vea [Sincronización de datos](sql-database-sync-data.md).

Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync, consulte los siguientes artículos:
-   [Uso de PowerShell para sincronizar varias bases de datos SQL de Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Uso de PowerShell para sincronizar una base de datos SQL de Azure y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> La documentación técnica completa para Azure SQL Data Sync, que anteriormente se encontraba en MSDN, está disponible como documento .pdf. Descárguela [aquí](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true).

## <a name="step-1---create-sync-group"></a>Paso 1: Creación de un grupo de sincronización

### <a name="locate-the-data-sync-settings"></a>Búsqueda de la configuración de Data Sync

1.  En el explorador, vaya a Azure Portal.

2.  En el portal, busque las bases de datos SQL desde el panel o el icono de SQL Database de la barra de herramientas.

    ![Lista de instancias de Azure SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  En la hoja **SQL Database**, seleccione la instancia existente de SQL Database que desea utilizar como la base de datos central de Data Sync. Se abre la hoja de SQL Database.

4.  En la hoja de SQL Database para la base de datos seleccionada, seleccione **Sincronizar con otras bases de datos**. Se abre la hoja de Data Sync.

    ![Opción Sincronizar con otras bases de datos](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Creación de un grupo de sincronización

1.  En la hoja de Data Sync, seleccione **Nuevo grupo de sincronización**. La hoja **Nuevo grupo de sincronización** se abre con el paso 1, con **Crear grupo de sincronización** resaltado. La hoja **Crear grupo de sincronización de datos** también se abre.

2.  En la hoja **Crear grupo de sincronización de datos**, haga lo siguiente:

    1.  En el campo **Nombre del grupo de sincronización**, escriba un nombre para el nuevo grupo de sincronización.

    2.  En la sección **Base de datos de metadatos de sincronización**, elija si desea crear una base de datos (opción recomendada) o usar una existente.

        > [!NOTE]
        > Se recomienda crear una vacía para usarla como base de datos de metadatos de sincronización. Data Sync crea tablas en esta base de datos y ejecuta una carga de trabajo frecuente. Esta base de datos se comparte automáticamente como la base de datos de metadatos de sincronización para todos los grupos de sincronización en la región seleccionada. No se puede cambiar la base de datos de metadatos de sincronización ni su nombre sin quitarla.

        Si ha elegido **Nueva base de datos**, seleccione **Crear nueva base de datos**. Se abre la hoja de **SQL Database**. En la hoja **SQL Database**, asigne un nombre a la base de datos nueva y configúrela. Después seleccione **Aceptar**.

        Si ha elegido **Usar base de datos existente**, seleccione la base de datos de la lista.

    3.  En la sección **Sincronización automática**, primero seleccione **Activado** o **Desactivado**.

        Si ha elegido **Activado**, en la sección **Frecuencia de sincronización**, escriba un número y seleccione Segundos, Minutos, Horas o Días.

        ![Establecimiento de la frecuencia de sincronizaicón](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  En la sección **Resolución de conflictos**, seleccione "Prevalece la base de datos central" o "Prevalece el cliente".

        ![Especifique cómo se resuelven los conflictos](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Seleccione **Aceptar** y espere a que el nuevo grupo de sincronización se cree e implemente.

## <a name="step-2---add-sync-members"></a>Paso 2: Adición de miembros de sincronización

Después de que el nuevo grupo de sincronización se crea e implementa, el paso 2, **Adición de miembros de sincronización**, se resalta en la hoja **Nuevo grupo de sincronización**.

En la sección **Base de datos central**, escriba las credenciales existentes para el servidor de SQL Database en el que se encuentra la base de datos central. No escriba *nuevas* credenciales en esta sección.

![La base de datos central se ha agregado al grupo de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>Adición de una instancia de Azure SQL Database

En la sección **Base de datos de miembros**, tiene la opción de agregar una instancia de Azure SQL Database al grupo de sincronización si selecciona **Agregar una base de datos de Azure**. La hoja **Configurar base de datos de Azure** se abre.

En la hoja **Configurar base de datos de Azure**, haga lo siguiente:

1.  En el campo **Nombre del miembro de sincronización**, proporcione un nombre para el nuevo miembro de sincronización. Este nombre es distinto del nombre de la base de datos.

2.  En el campo **Suscripción**, seleccione la suscripción de Azure asociada para fines de facturación.

3.  En el campo **Azure SQL Server**, seleccione el servidor de SQL Database existente.

4.  En el campo **Azure SQL Database**, seleccione la base de datos SQL existente.

5.  En el campo **Direcciones de sincronización**, seleccione la sincronización bidireccional, a la base de datos central o desde la base de datos central.

    ![Adición de un nuevo miembro de sincronización de SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  En los campos **Nombre de usuario** y **Contraseña**, escriba las credenciales existentes para el servidor de SQL Database en el que se encuentra la base de datos miembro. No escriba *nuevas* credenciales en esta sección.

7.  Seleccione **Aceptar** y espere a que el nuevo miembro de sincronización se cree e implemente.

    ![Se ha agregado el nuevo miembro de sincronización de SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>Adición de una base de datos local de SQL Server

En la sección **Base de datos de miembros**, tiene la opción de agregar una instancia local de SQL Server al grupo de sincronización; para ello, seleccione **Agregar una base de datos local**. La hoja **Configurar localmente** se abre.

En la hoja **Configurar localmente**, haga lo siguiente:

1.  Seleccione **Elegir Sync Agent Gateway**. La hoja **Seleccionar agente de sincronización** se abre.

    ![Selección de Sync Agent Gateway](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  En la hoja **Elegir Sync Agent Gateway**, elija si desea usar un agente existente o crear uno.

    Si ha elegido **Agentes existentes**, seleccione el agente existente en la lista.

    Si ha elegido **Crear un agente nuevo**, haga lo siguiente:

    1.  Descargue el software del Agente de sincronización cliente del vínculo facilitado e instálelo en el equipo en que se encuentra SQL Server.
 
        > [!IMPORTANT]
        > Tendrá que abrir el puerto TCP 1433 saliente en el firewall para permitir que el agente cliente se comunique con el servidor.


    2.  Escriba un nombre para el agente.

    3.  Seleccione **Crear y generar clave**.

    4.  Copie la clave del agente en el Portapapeles.
        
        ![Creación de un agente de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Seleccione **Aceptar** para cerrar la hoja **Seleccionar agente de sincronización**.

    6.  En el equipo de SQL Server, localice y ejecute la aplicación del Agente de sincronización cliente.

        ![La aplicación del agente cliente de Data Sync](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  En la aplicación del agente de sincronización, seleccione **Enviar clave del agente**. Se abre el cuadro de diálogo **Configuración de base de datos de metadatos de sincronización**.

    8.  En el cuadro de diálogo **Configuración de base de datos de metadatos de sincronización**, pegue la clave del agente copiada de Azure Portal, Proporcione también las credenciales existentes para el servidor de Azure SQL Database en el que se encuentra la base de datos de metadatos. (Si ha creado una base de datos de metadatos, esta base de datos está en el mismo servidor que la base de datos central). Seleccione **Aceptar** y espere a que la configuración finalice.

        ![Definición de las credenciales del servidor y de la clave del agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Si recibe un error de firewall en este punto, tendrá que crear una regla de firewall en Azure para permitir el tráfico entrante desde el equipo de SQL Server. Puede crear manualmente la regla en el portal, pero quizá le resulte más fácil crearla en SQL Server Management Studio (SSMS). En SSMS, intente conectarse a la base de datos central en Azure. Escriba su nombre como \<hub_database_name\>.database.windows.net. Siga los pasos del cuadro de diálogo para configurar la regla de firewall de Azure. A continuación, vuelva a la aplicación de Agente de sincronización cliente.

    9.  En la aplicación del Agente de sincronización cliente, haga clic en **Registrar** para registrar una instancia de SQL Server Database en el agente. Se abre el cuadro de diálogo **Configuración de SQL Server**.

        ![Adición y configuración de una instancia de SQL Server Database](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. En el cuadro de diálogo **Configuración de SQL Server**, elija si desea conectarse mediante autenticación de SQL Server o autenticación de Windows. Si elige la autenticación de SQL Server, escriba las credenciales existentes. Proporcione el nombre de SQL Server y el nombre de la base de datos que desea sincronizar. Seleccione **Probar conexión** para probar la configuración. Después, seleccione **Guardar**. La base de datos registrada aparece en la lista.

        ![La base de datos de SQL Server ya está registrada](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Ahora puede cerrar la aplicación del Agente de sincronización cliente.

    12. En el portal, en la hoja **Configurar localmente**, seleccione **Seleccionar una base de datos**. Se abre la hoja de **Seleccionar una base de datos**.

    13. En la hoja **Seleccionar una base de datos**, en el campo **Nombre del miembro de sincronización**, proporcione un nombre para el nuevo miembro de sincronización. Este nombre es distinto del nombre de la base de datos. Seleccione la base de datos de la lista. En el campo **Direcciones de sincronización**, seleccione la sincronización bidireccional, a la base de datos central o desde la base de datos central.

        ![Seleccione la base de datos local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Seleccione **Aceptar** para cerrar la hoja **Seleccionar una base de datos**. Después, seleccione **Aceptar** para cerrar la hoja **Configurar localmente** y espere a que el nuevo miembro de sincronización se cree e implemente. Por último, haga clic en **Aceptar** para cerrar la hoja **Seleccionar miembros de sincronización**.

        ![Base de datos local agregada al grupo de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Para conectarse a SQL Data Sync y al agente local, agregue el nombre de usuario al rol `DataSync_Executor`. Data Sync crea este rol en la instancia de SQL Server.

## <a name="step-3---configure-sync-group"></a>Paso 3: Configuración del grupo de sincronización

Después de que los nuevos miembros del grupo de sincronización se crean e implementan, el paso 3, **Configuración del grupo de sincronización**, se resalta en la hoja **Nuevo grupo de sincronización**.

1.  En la hoja **Tablas**, seleccione una base de datos en la lista de miembros del grupo de sincronización y, después, seleccione **Actualizar esquema**.

2.  En la lista de tablas disponibles, seleccione las tablas que desea sincronizar.

    ![Selección de tablas para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  De forma predeterminada, se seleccionan todas las columnas de la tabla. Si no desea sincronizar todas las columnas, deshabilite la casilla de las columnas que no desea sincronizar. Asegúrese de dejar seleccionada la columna de clave principal.

    ![Selección de campos para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Por último, seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
¡Enhorabuena! Ha creado un grupo de sincronización que incluye una instancia de SQL Database y una base de datos de SQL Server.

Para obtener más información sobre SQL Database y SQL Data Sync, vea:

-   [Descarga de la documentación técnica completa de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
