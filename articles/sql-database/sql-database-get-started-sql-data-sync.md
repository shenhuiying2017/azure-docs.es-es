---
title: "Configuración de Azure SQL Data Sync (versión preliminar) | Microsoft Docs"
description: "Este tutorial muestra cómo configurar Azure SQL Data Sync (versión preliminar)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: b356bc9db9e883c2514953b516d6dd51c1807610
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="set-up-sql-data-sync-preview"></a>Configuración de SQL Data Sync (versión preliminar)
En este tutorial, obtendrá información sobre cómo configurar Azure SQL Data Sync mediante la creación de un grupo de sincronización híbrido que contiene instancias de Azure SQL Database y de SQL Server. El nuevo grupo de sincronización está configurado completamente y se sincroniza según el programa establecido.

En este tutorial se asume que tiene al menos alguna experiencia previa con SQL Database y con SQL Server. 

Para obtener información general sobre SQL Data Sync, vea [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync de Azure (versión preliminar)](sql-database-sync-data.md).

Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync, consulte los siguientes artículos:
-   [Uso de PowerShell para sincronizar varias bases de datos SQL de Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Paso 1: Creación de un grupo de sincronización

### <a name="locate-the-data-sync-settings"></a>Búsqueda de la configuración de Data Sync

1.  En el explorador, vaya a Azure Portal.

2.  En el portal, busque las bases de datos SQL desde el panel o el icono de SQL Database de la barra de herramientas.

    ![Lista de instancias de Azure SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  En la página **Bases de datos SQL**, seleccione la instancia existente de SQL Database que quiere usar como base de datos central de Data Sync. Se abre la página de la base de datos SQL.

4.  En la página de la base de datos SQL para la base de datos seleccionada, seleccione **Sincronizar con otras bases de datos**. Se abre la página de Data Sync.

    ![Opción Sincronizar con otras bases de datos](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Creación de un grupo de sincronización

1.  En la página de Data Sync, seleccione **Nuevo grupo de sincronización**. La página **Nuevo grupo de sincronización** se abre con el paso 1, con **Crear grupo de sincronización** resaltado. La página **Crear grupo de sincronización de datos** también se abre.

2.  En la página **Crear grupo de sincronización de datos**, haga lo siguiente:

    1.  En el campo **Nombre del grupo de sincronización**, escriba un nombre para el nuevo grupo de sincronización.

    2.  En la sección **Base de datos de metadatos de sincronización**, elija si desea crear una base de datos (opción recomendada) o usar una existente.

        > [!NOTE]
        > Se recomienda crear una vacía para usarla como base de datos de metadatos de sincronización. Data Sync crea tablas en esta base de datos y ejecuta una carga de trabajo frecuente. Esta base de datos se comparte automáticamente como la base de datos de metadatos de sincronización para todos los grupos de sincronización en la región seleccionada. No se puede cambiar la base de datos de metadatos de sincronización ni su nombre sin quitarla.

        Si ha elegido **Nueva base de datos**, seleccione **Crear nueva base de datos**. Se abre la página **SQL Database**. En la página **SQL Database**, asigne un nombre a la base de datos nueva y configúrela. Después seleccione **Aceptar**.

        Si ha elegido **Usar base de datos existente**, seleccione la base de datos de la lista.

    3.  En la sección **Sincronización automática**, primero seleccione **Activado** o **Desactivado**.

        Si ha elegido **Activado**, en la sección **Frecuencia de sincronización**, escriba un número y seleccione Segundos, Minutos, Horas o Días.

        ![Establecimiento de la frecuencia de sincronizaicón](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  En la sección **Resolución de conflictos**, seleccione "Prevalece la base de datos central" o "Prevalece el cliente".

        ![Especifique cómo se resuelven los conflictos](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Seleccione **Aceptar** y espere a que el nuevo grupo de sincronización se cree e implemente.

## <a name="step-2---add-sync-members"></a>Paso 2: Adición de miembros de sincronización

Después de que el nuevo grupo de sincronización se crea e implementa, el paso 2, **Agregar miembros de sincronización**, se resalta en la página **Nuevo grupo de sincronización**.

En la sección **Base de datos central**, escriba las credenciales existentes para el servidor de SQL Database en el que se encuentra la base de datos central. No escriba *nuevas* credenciales en esta sección.

![La base de datos central se ha agregado al grupo de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Adición de una instancia de Azure SQL Database

En la sección **Base de datos de miembros**, tiene la opción de agregar una instancia de Azure SQL Database al grupo de sincronización si selecciona **Agregar una base de datos de Azure**. Se abre la página **Configurar base de datos de Azure**.

En la página **Configurar base de datos de Azure**, haga lo siguiente:

1.  En el campo **Nombre del miembro de sincronización**, proporcione un nombre para el nuevo miembro de sincronización. Este nombre es distinto del nombre de la base de datos.

2.  En el campo **Suscripción**, seleccione la suscripción de Azure asociada para fines de facturación.

3.  En el campo **Azure SQL Server**, seleccione el servidor de SQL Database existente.

4.  En el campo **Azure SQL Database**, seleccione la base de datos SQL existente.

5.  En el campo **Direcciones de sincronización**, seleccione la sincronización bidireccional, a la base de datos central o desde la base de datos central.

    ![Adición de un nuevo miembro de sincronización de SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  En los campos **Nombre de usuario** y **Contraseña**, escriba las credenciales existentes para el servidor de SQL Database en el que se encuentra la base de datos miembro. No escriba *nuevas* credenciales en esta sección.

7.  Seleccione **Aceptar** y espere a que el nuevo miembro de sincronización se cree e implemente.

    ![Se ha agregado el nuevo miembro de sincronización de SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Adición de una base de datos local de SQL Server

En la sección **Base de datos de miembros**, tiene la opción de agregar una instancia local de SQL Server al grupo de sincronización; para ello, seleccione **Agregar una base de datos local**. Se abre la página **Configurar localmente**.

En la página **Configurar localmente**, haga lo siguiente:

1.  Seleccione **Elegir Sync Agent Gateway**. Se abre la página **Seleccionar agente de sincronización**.

    ![Selección de Sync Agent Gateway](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  En la página **Elegir Sync Agent Gateway**, seleccione si quiere usar un agente existente o crear uno.

    Si ha elegido **Agentes existentes**, seleccione el agente existente en la lista.

    Si ha elegido **Crear un agente nuevo**, haga lo siguiente:

    1.  Descargue el software del Agente de sincronización cliente del vínculo facilitado e instálelo en el equipo en que se encuentra SQL Server.
 
        > [!IMPORTANT]
        > Tendrá que abrir el puerto TCP 1433 saliente en el firewall para permitir que el agente cliente se comunique con el servidor.


    2.  Escriba un nombre para el agente.

    3.  Seleccione **Crear y generar clave**.

    4.  Copie la clave del agente en el Portapapeles.
        
        ![Creación de un agente de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Seleccione **Aceptar** para cerrar la página **Seleccionar agente de sincronización**.

    6.  En el equipo de SQL Server, localice y ejecute la aplicación del Agente de sincronización cliente.

        ![La aplicación del agente cliente de Data Sync](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  En la aplicación del agente de sincronización, seleccione **Enviar clave del agente**. Se abre el cuadro de diálogo **Configuración de base de datos de metadatos de sincronización**.

    8.  En el cuadro de diálogo **Configuración de base de datos de metadatos de sincronización**, pegue la clave del agente copiada de Azure Portal, Proporcione también las credenciales existentes para el servidor de Azure SQL Database en el que se encuentra la base de datos de metadatos. (Si ha creado una base de datos de metadatos, esta base de datos está en el mismo servidor que la base de datos central). Seleccione **Aceptar** y espere a que la configuración finalice.

        ![Definición de las credenciales del servidor y de la clave del agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Si recibe un error de firewall en este punto, tendrá que crear una regla de firewall en Azure para permitir el tráfico entrante desde el equipo de SQL Server. Puede crear manualmente la regla en el portal, pero quizá le resulte más fácil crearla en SQL Server Management Studio (SSMS). En SSMS, intente conectarse a la base de datos central en Azure. Escriba su nombre como \<hub_database_name\>.database.windows.net. Para configurar la regla de firewall de Azure, siga los pasos del cuadro de diálogo. A continuación, vuelva a la aplicación de Agente de sincronización cliente.

    9.  En la aplicación del Agente de sincronización cliente, haga clic en **Registrar** para registrar una instancia de SQL Server Database en el agente. Se abre el cuadro de diálogo **Configuración de SQL Server**.

        ![Adición y configuración de una instancia de SQL Server Database](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. En el cuadro de diálogo **Configuración de SQL Server**, elija si desea conectarse mediante autenticación de SQL Server o autenticación de Windows. Si elige la autenticación de SQL Server, escriba las credenciales existentes. Proporcione el nombre de SQL Server y el nombre de la base de datos que desea sincronizar. Seleccione **Probar conexión** para probar la configuración. Después, seleccione **Guardar**. La base de datos registrada aparece en la lista.

        ![La base de datos de SQL Server ya está registrada](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Ahora puede cerrar la aplicación del Agente de sincronización cliente.

    12. En el portal, en la página **Configurar localmente**, elija **Seleccionar base de datos**. Se abre la página **Seleccionar base de datos**.

    13. En la página **Seleccionar base de datos**, en el campo **Nombre del miembro de sincronización**, proporcione un nombre para el nuevo miembro de sincronización. Este nombre es distinto del nombre de la base de datos. Seleccione la base de datos de la lista. En el campo **Direcciones de sincronización**, seleccione la sincronización bidireccional, a la base de datos central o desde la base de datos central.

        ![Seleccione la base de datos local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Seleccione **Aceptar** para cerrar la página **Seleccionar base de datos**. Después, seleccione **Aceptar** para cerrar la página **Configurar localmente** y espere a que el nuevo miembro de sincronización se cree e implemente. Por último, haga clic en **Aceptar** para cerrar la página **Seleccionar miembros de sincronización**.

        ![Base de datos local agregada al grupo de sincronización](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Para conectarse a SQL Data Sync y al agente local, agregue el nombre de usuario al rol `DataSync_Executor`. Data Sync crea este rol en la instancia de SQL Server.

## <a name="step-3---configure-sync-group"></a>Paso 3: Configuración del grupo de sincronización

Una vez que se han creado e implementado los nuevos miembros del grupo de sincronización, el paso 3, **Configuración del grupo de sincronización**, se resalta en la página **Nuevo grupo de sincronización**.

1.  En la página **Tablas**, seleccione una base de datos en la lista de miembros del grupo de sincronización y, después, seleccione **Actualizar esquema**.

2.  En la lista de tablas disponibles, seleccione las tablas que desea sincronizar.

    ![Selección de tablas para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  De forma predeterminada, se seleccionan todas las columnas de la tabla. Si no desea sincronizar todas las columnas, deshabilite la casilla de las columnas que no desea sincronizar. Asegúrese de dejar seleccionada la columna de clave principal.

    ![Selección de campos para sincronizar](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Por último, seleccione **Guardar**.

## <a name="faq-about-setup-and-configuration"></a>Preguntas frecuentes sobre configuración

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>¿Con qué frecuencia puede sincronizar mis datos Data Sync? 
La frecuencia mínima es cada cinco minutos.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>¿SQL Data Sync crea y aprovisiona tablas completamente?

Si las tablas del esquema de sincronización no se han creado en la base de datos de destino, SQL Data Sync (versión preliminar) las crea con las columnas que haya seleccionado. Sin embargo, este comportamiento no genera un esquema de total fidelidad por las razones siguientes:

-   Solo las columnas seleccionadas se crean en la tabla de destino. Si algunas columnas de las tablas de origen no forman parte del grupo de sincronización, estas columnas no se aprovisionan en las tablas de destino.

-   Los índices se crean únicamente para las columnas seleccionadas. Si el índice de la tabla de origen tiene columnas que no forman parte del grupo de sincronización, estos índices no se aprovisionan en las tablas de destino.

-   Los índices en las columnas de tipo XML no se aprovisionan.

-   Las restricciones CHECK no se aprovisionan.

-   Los desencadenadores existentes en las tablas de origen no se aprovisionan.

-   No se crean vistas ni procedimientos almacenados en la base de datos de destino.

Debido a estas limitaciones, se recomienda lo siguiente:
-   Para entornos de producción, aprovisione el esquema de total fidelidad personalmente.
-   Para probar el servicio, la característica de aprovisionamiento automático de SQL Data Sync (versión preliminar) funciona correctamente.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>¿Por qué veo tablas que no he creado?  
Data Sync crea tablas laterales en su base de datos para hacer un seguimiento de los cambios. No las elimine, ya que Data Sync dejaría de funcionar.

### <a name="is-my-data-convergent-after-a-sync"></a>¿Son los datos homogéneos después de una sincronización?

No necesariamente. En un grupo de sincronización con un concentrador y tres radios (A, B y C), se sincroniza el concentrador con A, con B y con C. Si se realiza un cambio en la base de datos A *después* de que se haya sincronizado el concentrador con A, ese cambio no se aplica en las bases de datos B o C hasta la siguiente tarea de sincronización.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>¿Cómo se realizan cambios de esquema en un grupo de sincronización?

Tendrá que realizar manualmente los cambios de esquema.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>¿Cómo se exporta e importa una base de datos con la sincronización de datos?
Después de exportar una base de datos como un archivo `.bacpac` e importar dicho archivo para crear una base de datos, debe realizar los dos pasos siguientes para usar Data Sync en la nueva base de datos:
1.  Limpie los objetos de sincronización de datos y las tablas auxiliares en la **nueva base de datos** mediante [este script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Este script elimina todos los objetos de sincronización de datos necesarios de la base de datos.
2.  Vuelva a crear el grupo de sincronización con la nueva base de datos. Si ya no necesita el grupo de sincronización antiguo, elimínelo.

## <a name="faq-about-the-client-agent"></a>Preguntas frecuentes sobre el agente cliente

### <a name="why-do-i-need-a-client-agent"></a>¿Por qué se necesita un agente cliente?

El servicio SQL Data Sync (versión preliminar) se comunica con bases de datos de SQL Server a través del agente cliente. Esta función de seguridad evita la comunicación directa con las bases de datos detrás de un firewall. Cuando el servicio SQL Data Sync (versión preliminar) se comunica con el agente, lo hace mediante conexiones cifradas y un token o *clave del agente* únicos. Las bases de datos de SQL Server autentican el agente mediante la cadena de conexión y la clave del agente. Este diseño proporciona un alto nivel de seguridad para los datos.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>¿Cuántas instancias de la interfaz de usuario del agente local pueden ejecutarse?

Solo se puede ejecutar una instancia de la interfaz de usuario.

### <a name="how-can-i-change-my-service-account"></a>¿Cómo puedo cambiar mi cuenta de servicio?

Después de instalar un agente cliente, la única manera de cambiar la cuenta de servicio es desinstalarlo e instalar un nuevo agente cliente con la nueva cuenta de servicio.

### <a name="how-do-i-change-my-agent-key"></a>¿Cómo puedo cambiar la clave del agente?

Un agente solo puede usar una clave del agente una vez. No se puede reutilizar si se quita el agente y se instala uno nuevo, ni tampoco puede ser utilizada por varios agentes. Si necesita crear una nueva clave para un agente existente, debe asegurarse de que la misma clave esté registrada con el agente cliente y el servicio SQL Data Sync (versión preliminar).

### <a name="how-do-i-retire-a-client-agent"></a>¿Cómo se puede retirar un agente cliente?

Para invalidar o retirar un agente inmediatamente, vuelva a generar su clave en el portal pero no la envíe en la interfaz de usuario del agente. Al regenerar una clave, se invalida la clave anterior, independientemente de si el agente correspondiente está conectado o desconectado.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>¿Cómo muevo un agente cliente a otro equipo?

Si desea ejecutar el agente local desde otro equipo distinto al equipo en el que está actualmente, haga lo siguiente:

1. Instale el agente en el equipo deseado.

2. Inicie sesión en el portal de SQL Data Sync (versión preliminar) y vuelva a generar una clave del agente para el nuevo agente.

3. Use la interfaz de usuario del nuevo agente para enviar la nueva clave del agente.

4. Espere mientras el agente cliente descarga la lista de bases de datos locales que se registraron anteriormente.

5. Proporcione las credenciales de todas las bases de datos que se muestren como inaccesibles. Estas bases de datos deben ser accesibles desde el nuevo equipo en el que está instalado el agente.

## <a name="next-steps"></a>Pasos siguientes
¡Enhorabuena! Ha creado un grupo de sincronización que incluye una instancia de SQL Database y un servidor de SQL Database.

Para más información sobre SQL Data Sync, consulte:

-   [Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync](sql-database-sync-data.md)
-   [Procedimientos recomendados para SQL Data Sync de Azure](sql-database-best-practices-data-sync.md)
-   [Supervisión de Azure SQL Data Sync con Log Analytics de OMS](sql-database-sync-monitor-oms.md)
-   [Solución de problemas de SQL Data Sync de Azure](sql-database-troubleshoot-data-sync.md)

-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync:
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
