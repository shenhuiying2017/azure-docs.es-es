---
title: "Migración de una BD de SQL Server a Azure SQL Database | Microsoft Docs"
description: Aprenda a migrar una base de datos SQL Server a Azure SQL Database.
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: c6d965351f6f131ee342cea672fc4fa8771f8ede
ms.lasthandoff: 04/21/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migración de su base de datos SQL Server a Azure SQL Database

En este tutorial, se migra una base de datos de SQL Server existente a Azure SQL Database mediante Microsoft Data Migration Assistant y se siguen los pasos necesarios desde la preparación para la migración y la finalización de la migración real de los datos hasta la conexión con la base de datos migrada después de completar la migración. 

> [!IMPORTANT]
> Para solucionar problemas de compatibilidad, use [SQL Server Data Tools ](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). 
>

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:

- La versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Al instalar SSMS, también se instala la versión más reciente de SQLPackage, una utilidad de línea de comandos que puede utilizarse para automatizar diversas tareas de desarrollo de bases de datos. 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Una base de datos que desee migrar. Este tutorial se usa la [base de datos OLTP AdventureWorks de SQL Server 2008R2](https://msftdbprodsamples.codeplex.com/releases/view/59211) en una instancia de SQL Server 2008R2 o versiones más recientes, pero se puede usar cualquier base de datos de su elección. 

## <a name="prepare-for-migration"></a>Preparación para la migración

Está listo para preparar la migración. Siga estos pasos para usar  **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)**  para evaluar la preparación de la base de datos para la migración a Azure SQL Database.

1. Abra **Data Migration Assistant**. Puede ejecutar DMA en cualquier equipo con conexión a la instancia de SQL Server que contiene la base de datos que va a migrar, no es necesario instalarla en el equipo que hospeda la instancia de SQL Server.

     ![abrir data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. En el menú de la izquierda, haga clic en **+ Nuevo** para crear un proyecto de **Evaluación**. Rellene el formulario con un **Nombre del proyecto** (todos los demás valores deben dejarse en sus valores predeterminados) y haga clic en **Crear**. Abra la página **Opciones**.

     ![nuevo proyecto de data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. En la página **Opciones**, haga clic en **Siguiente**. Se abre la página **Seleccione orígenes**.

     ![opciones de nueva migración de datos](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. En la página **Seleccione orígenes**, escriba el nombre de instancia de SQL Server que contiene el servidor que se va a migrar. Cambie los demás valores de esta página si es necesario. Haga clic en **Conectar**.

     ![seleccione orígenes de nueva migración de datos](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. En la sección **Agregar orígenes** de la página **Seleccione orígenes**, seleccione las casillas de las bases de datos de las que se va a probar la compatibilidad. Haga clic en **Agregar**.

     ![seleccione orígenes de nueva migración de datos](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. Haga clic en **Iniciar evaluación**.

     ![inicio de la evaluación de la nueva migración de datos](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. Cuando se completa la evaluación, compruebe primero para ver si la base de datos es lo suficientemente compatible como para migrar. Busque la marca de verificación en un círculo verde.

     ![resultado de evaluación de la nueva migración de datos compatible](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. Revise los resultados, comenzando con la **paridad de características de SQL Server**. En concreto, revise la información sobre las características no compatibles y parcialmente compatibles, y la información proporcionada acerca de las acciones recomendadas. 

     ![evaluación de la nueva migración de datos paridad](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Haga clic en **Problemas de compatibilidad**. En concreto, revise la información acerca de bloqueos de la migración, los cambios de comportamiento y características en desuso para cada nivel de compatibilidad. Para la base de datos AdventureWorks2008R2, revise los cambios en la búsqueda de texto completo desde SQL Server 2008 y los cambios realizados en SERVERPROPERTY('LCID') desde SQL Server 2000. Para obtener información sobre estos cambios, se proporcionan vínculos para obtener más información. Muchas opciones de búsqueda y la configuración de búsqueda de texto completo han cambiado. 

   > [!IMPORTANT] 
   > Después de migrar la base de datos a Azure SQL Database, puede decidir utilizar la base de datos en su nivel de compatibilidad actual (nivel 100 para la base de datos AdventureWorks2008R2) o en un nivel superior. Para obtener más información sobre las implicaciones y las opciones para la utilización de una base de datos en un nivel de compatibilidad específico, consulte [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Nivel de compatibilidad de ALTER DATABASE). Vea también [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obtener información sobre los valores de nivel de base de datos adicionales relacionados con los niveles de compatibilidad.
   >

10. Si lo desea, haga clic en **Exportar informe** para guardar el informe como un archivo JSON.
11. Cierre Data Migration Assistant.

## <a name="export-to-bacpac-file"></a>Exportación a un archivo BACPAC 

Un archivo BACPAC es un archivo ZIP con una extensión de BACPAC que contiene los metadatos y los datos de una base de datos de SQL Server. Un archivo BACPAC se puede almacenar en Azure Blob Storage o en el almacenamiento local para su archivado o migración: como de SQL Server a Azure SQL Database. Para que una exportación sea transaccionalmente coherente, deberá asegurarse de que no se produce ninguna actividad escritura durante la exportación.

Siga estos pasos para usar la utilidad de línea de comandos SQLPackage para exportar la base de datos AdventureWorks2008R2 al almacenamiento local.

1. Abra un símbolo del sistema de Windows y cambie el directorio a una carpeta en la que tenga la versión **130** de SQLPackage: **C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin**.

2. Ejecute el siguiente comando de SQLPackage en la línea de comandos para exportar la base de datos **AdventureWorks2008R2** de **localhost** a **AdventureWorks2008R2.bacpac**. Modifique cualquiera de estos valores según sea apropiado para su entorno.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![exportación de sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

Una vez completada la ejecución, se almacena el archivo BCPAC generado en el directorio donde se encuentra el archivo ejecutable sqlpackage. En este ejemplo, C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Al inicia sesión desde el equipo desde el que se ejecuta la utilidad de línea de comandos SQLPackage, se facilita la creación de la regla de firewall del paso 5.

## <a name="create-a-sql-database-logical-server"></a>Creación de un servidor lógico de SQL Database

Un [servidor lógico de Azure SQL Database](sql-database-features.md) actúa como el punto central de administración de varias bases de datos. Siga estos pasos para crear un servidor lógico de SQL Database de tal forma que contenga la base de datos OLTP AdventureWorks de SQL Server. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Escriba **servidor** en la ventana de búsqueda de la página **Nuevo** y seleccione **SQL Server (servidor lógico)** en la lista filtrada.

    ![seleccionar servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. En la página **Todo**, haga clic en **Base de datos de SQL (servidor lógico)** y, a continuación, haga clic en **Crear** en la página **SQL Server (servidor lógico)**.

    ![crear un servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Rellene el formulario de SQL Server (servidor lógico) con la siguiente información, como se muestra en la imagen anterior:     

   - Nombre del servidor: especifique un nombre de servidor único global.
   - Inicio de sesión del administrador del servidor: proporcione un nombre para la cuenta de inicio de sesión del administrador del servidor.
   - Contraseña: especifique la contraseña que desee.
   - Grupo de recursos: seleccione **Crear nuevo** y especifique **myResourceGroup**.
   - Ubicación: seleccione una ubicación de centro de datos

    ![formulario de creación de servidor lógico completado](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Haga clic en **Crear** para aprovisionar el servidor lógico. El aprovisionamiento tarda unos minutos. 

## <a name="create-a-server-level-firewall-rule"></a>Crear una regla de firewall de nivel de servidor

El servicio SQL Database crea un [firewall en el nivel de servidor](sql-database-firewall-configure.md), lo que impide que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos del servidor, a menos que se cree una regla de firewall para abrir el firewall para direcciones IP concretas. Siga estos pasos para crear una regla de firewall de nivel de servidor de SQL Database para la dirección IP del equipo desde el que se ejecuta la utilidad de línea de comandos SQLPackage. Esto permite que SQLPackage se conecte con el servidor lógico de SQL Database a través del firewall Azure SQL Database. 

1. Haga clic en **Todos los recursos** en el menú de la izquierda y haga clic en el nuevo servidor en la página **Todos los recursos**. Se abre la página de información general del servidor, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170403.database.windows.net**) y proporciona opciones para otras configuraciones.

     ![información general del servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. En la página de información general, haga clic en **Firewall** en el menú de la izquierda bajo **Configuración**. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

3. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP del equipo que está usando actualmente y, luego, haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para esta dirección IP.

     ![establecer regla de firewall del servidor](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Haga clic en **Aceptar**.

Ahora puede conectarse todas las bases de datos de este servidor mediante SQL Server Management Studio o cualquier otra herramienta que elija desde esta dirección IP usando la cuenta de administrador del servidor creada con anterioridad.

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor de Azure SQL Database, a menos que el departamento de TI abra el puerto 1433.
>

## <a name="import-bacpac-file-to-azure-sql-database"></a>Importación de un archivo BACPAC a Azure SQL Database 

Las versiones más recientes de la utilidad de línea de comandos SQLPackage proporcionan compatibilidad para crear una base de datos de Azure SQL Database en un determinado [nivel de servicio y nivel rendimiento](sql-database-service-tiers.md). Para obtener un rendimiento óptimo durante la importación, seleccione un nivel de alto rendimiento y servicio y, luego, redúzcalo verticalmente después de la importación si el nivel de rendimiento y de nivel de servicio es mayor de lo que necesita inmediatamente.

Siga estos pasos para usar la utilidad de línea de comandos SQLPackage para importar la base de datos AdventureWorks2008R2 a Azure SQL Database.

- Ejecute el siguiente comando de SQLPackage en la línea de comandos para importar la base de datos **AdventureWorks2008R2** desde el almacenamiento local al servidor lógico de Azure SQL Database que creó anteriormente con el nombre de base de datos **myMigratedDatabase**, un nivel de servicio **Premium** y un objetivo de servicio **P6**. Modifique cualquiera de estos tres valores según sea apropiado para su entorno.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![importación de sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Un servidor lógico de Azure SQL Database escucha en el puerto 1433. Si intenta conectarse a un servidor lógico de Azure SQL Database desde dentro de un firewall corporativo, este puerto debe estar abierto en el firewall corporativo para poder conectarse correctamente.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Conexión con SQL Server Management Studio (SSMS)

Use SQL Server Management Studio para establecer una conexión con un servidor de Azure SQL Database y la base de datos migrada. Si está ejecutando SQL Server Management Studio en un equipo diferente del que ejecutó SQLPackage, cree una regla de firewall para este equipo usando los pasos del procedimiento anterior.

1. Abra SQL Server Management Studio.

2. En el cuadro de diálogo **Conectar con el servidor**, especifique la siguiente información:
   - **Tipo de servidor**: especifique el motor de base de datos
   - **Nombre del servidor**: escriba el nombre completo del servidor, como **mynewserver20170403.database.windows.net**
   - **Autenticación**: especifique la autenticación de SQL Server
   - **Inicio de sesión**: escriba la cuenta de administrador del servidor
   - **Contraseña**: escriba la contraseña de la cuenta de administrador del servidor
 
    ![conexión con ssms](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. Haga clic en **Conectar**. Se abre la ventana del Explorador de objetos. 

4. En el Explorador de objetos, expanda **Bases de datos** y, después, expanda **myMigratedDatabase** para ver los objetos de la base de datos de ejemplo.

## <a name="change-database-properties"></a>Modificación de las propiedades de la base de datos

Puede modificar el nivel de servicio, el nivel de rendimiento y el nivel de compatibilidad con SQL Server Management Studio.

1. En el Explorador de objetos, haga clic con el botón derecho en **myMigratedDatabase** y, luego, en **Nueva consulta**. Se abre una ventana de consulta conectada a la base de datos.

2. Ejecute el siguiente comando para establecer el nivel de servicio en **estándar** y el nivel de rendimiento en **S1**.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![cambio de niveles de servicio](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. Ejecute el comando siguiente para cambiar el nivel de compatibilidad de la base de datos a **130**.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![cambio del nivel de compatibilidad](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>Pasos siguientes 

- Para obtener información general de la migración, vea [Migración de bases de datos](sql-database-cloud-migrate.md).
- Para obtener una presentación de las diferencias de Transact-SQL, vea [Resolución de diferencias de Transact-SQL durante la migración a SQL Database](sql-database-transact-sql-information.md).
- Para conectarse y consultar con Visual Studio Code, vea [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectarse y consultar con .NET, vea [Conexión y consultas con .NET](sql-database-connect-query-dotnet.md).
- Para conectarse y consultar con PHP, vea [Conexión y consultas con PHP](sql-database-connect-query-php.md).
- Para conectarse y consultar con Node.js, vea [Conexión y consultas con Node.js](sql-database-connect-query-nodejs.md).
- Para conectarse y consultar con Java, vea [Conexión y consultas con Java](sql-database-connect-query-java.md).
- Para conectarse y consultar con Python, vea [Conexión y consultas con Python](sql-database-connect-query-python.md).
- Para conectarse y consultar con Ruby, vea [Conexión y consultas con Ruby](sql-database-connect-query-ruby.md).


