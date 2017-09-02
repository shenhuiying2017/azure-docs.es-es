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
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migración de su base de datos SQL Server a Azure SQL Database

El movimiento de una base de datos de SQL Server a Azure SQL Database es un proceso que consta de tres partes: primero se prepara la base de datos, después se exporta y finalmente se importa. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Preparar una base de datos en un servidor SQL Server para migrarla a Azure SQL Database mediante [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA)
> * Exportar la base de datos a un archivo BACPAC
> * Importar el archivo BACPAC en Azure SQL Database

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

- Se ha instalado la versión más reciente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Al instalar SSMS, también se instala la versión más reciente de SQLPackage, una utilidad de línea de comandos que puede utilizarse para automatizar diversas tareas de desarrollo de bases de datos. 
- Se ha instalado [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Se ha identificado y obtenido acceso a una base de datos para migrar. Este tutorial se usa la [base de datos OLTP AdventureWorks de SQL Server 2008R2](https://msftdbprodsamples.codeplex.com/releases/view/59211) en una instancia de SQL Server 2008R2 o versiones más recientes, pero se puede usar cualquier base de datos de su elección. Para solucionar problemas de compatibilidad, use [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

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

8. Consulte los resultados. Los resultados de **paridad de características de SQL Server** que se muestran son los predeterminados para la revisión. En concreto, revise la información sobre las características no compatibles y parcialmente compatibles, y la información proporcionada acerca de las acciones recomendadas. 

     ![evaluación de la nueva migración de datos paridad](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Revise los **Problemas de compatibilidad** haciendo clic en esa opción en la parte superior izquierda. En concreto, revise la información acerca de bloqueos de la migración, los cambios de comportamiento y características en desuso para cada nivel de compatibilidad. Para la base de datos AdventureWorks2008R2, revise los cambios en la búsqueda de texto completo desde SQL Server 2008 y los cambios realizados en SERVERPROPERTY('LCID') desde SQL Server 2000. Para obtener información sobre estos cambios, se proporcionan vínculos para obtener más información. Muchas opciones de búsqueda y la configuración de búsqueda de texto completo han cambiado. 

   > [!IMPORTANT] 
   > Después de migrar la base de datos a Azure SQL Database, puede decidir utilizar la base de datos en su nivel de compatibilidad actual (nivel 100 para la base de datos AdventureWorks2008R2) o en un nivel superior. Para más información acerca de las implicaciones y las opciones para la utilización de una base de datos en un nivel de compatibilidad específico, consulte [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) [Nivel de compatibilidad de ALTER DATABASE (Transact-SQL)]. Vea también [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obtener información sobre los valores de nivel de base de datos adicionales relacionados con los niveles de compatibilidad.
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

Una vez completada la ejecución, se almacena el archivo BACPAC generado en el directorio donde se encuentra el archivo ejecutable sqlpackage. En este ejemplo, C:\Program Files (x86) \Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Al inicia sesión desde el equipo desde el que se ejecuta la utilidad de línea de comandos SQLPackage, se facilita la creación de la regla de firewall del paso 5.

## <a name="create-a-sql-server-logical-server"></a>Crear un servidor lógico de SQL Server

Un [servidor lógico de SQL Server](sql-database-features.md) actúa como el punto central de administración de varias bases de datos. Siga estos pasos para crear un servidor lógico de SQL Server de tal forma que contenga la base de datos OLTP Adventure Works de SQL Server. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Escriba **servidor sql** en la ventana de búsqueda de la página **Nuevo** y seleccione **Base de datos SQL (servidor lógico)** en la lista filtrada.

    ![seleccionar servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. En la página **Todo**, haga clic en **Base de datos de SQL (servidor lógico)** y, a continuación, haga clic en **Crear** en la página **SQL Server (servidor lógico)**.

    ![crear un servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Rellene el formulario de SQL Server (servidor lógico) con la siguiente información, como se muestra en la imagen anterior:     

   | Configuración       | Valor sugerido | Descripción | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nombre del servidor** | Escriba un nombre único global. | Para conocer cuáles son los nombres de servidor válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). | 
   | **Inicio de sesión del administrador del servidor** | Escriba cualquier nombre válido. | Para conocer los nombres de inicio de sesión válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos). |
   | **Password** | Escriba cualquier contraseña válida. | La contraseña debe tener un mínimo de 8 caracteres y debe contener caracteres de tres de las siguientes categorías: caracteres en mayúsculas, caracteres en minúsculas, números y caracteres no alfanuméricos. |
   | **Suscripción** | Selección de una suscripción | Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   | **Grupos de recursos** | Elija un grupo de recursos existente o cree uno nuevo, como **myResourceGroup** |  Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   | **Ubicación** | Escriba una ubicación válida para el nuevo servidor | Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). |

   ![formulario de creación de servidor lógico completado](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Haga clic en **Crear** para aprovisionar el servidor lógico. El aprovisionamiento tarda unos minutos. 

> [!IMPORTANT]
> Recuerde el nombre del servidor, el nombre de inicio de sesión del administrador del servidor y la contraseña. Necesitará estos valores más adelante en el tutorial.
>

## <a name="create-a-server-level-firewall-rule"></a>Crear una regla de firewall de nivel de servidor

El servicio SQL Database crea un [firewall en el nivel de servidor](sql-database-firewall-configure.md), lo que impide que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos del servidor, a menos que se cree una regla de firewall para abrir el firewall para direcciones IP concretas. Siga estos pasos para crear una regla de firewall de nivel de servidor de SQL Database para la dirección IP del equipo desde el que se ejecuta la utilidad de línea de comandos SQLPackage. Esto permite que SQLPackage se conecte con el servidor lógico de base de datos de SQL Server a través del firewall de Azure SQL Database. 

1. Haga clic en **Todos los recursos** en el menú de la izquierda y haga clic en el nuevo servidor en la página **Todos los recursos**. Se abrirá la página de información general del servidor, en la que se proporcionan opciones para continuar la configuración.

     ![información general del servidor lógico](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. En la página de información general, haga clic en **Firewall** en el menú de la izquierda bajo **Configuración**. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

3. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP del equipo que está usando actualmente y, luego, haga clic en **Guardar**. Se creará una regla de firewall de nivel de servidor para esta dirección IP.

     ![establecer regla de firewall del servidor](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Haga clic en **Aceptar**.

Ahora puede conectarse todas las bases de datos de este servidor mediante SQL Server Management Studio o cualquier otra herramienta que elija desde esta dirección IP usando la cuenta de administrador del servidor creada con anterioridad.

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el departamento de TI abra el puerto 1433.
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>Importar un archivo BACPAC a Azure SQL Database 

Las versiones más recientes de la utilidad de línea de comandos SQLPackage proporcionan compatibilidad para crear una base de datos de Azure SQL Database en un determinado [nivel de servicio y nivel rendimiento](sql-database-service-tiers.md). Para obtener un rendimiento óptimo durante la importación, seleccione un nivel de alto rendimiento y servicio y, luego, redúzcalo verticalmente después de la importación si el nivel de rendimiento y de nivel de servicio es mayor de lo que necesita inmediatamente.

Siga estos pasos para usar la utilidad de línea de comandos SQLPackage para importar la base de datos AdventureWorks2008R2 a Azure SQL Database. Aunque puede usar SQL Server Management Studio para esta tarea, SQLPackage es el método preferido para la mayoría de entornos de producción para obtener la máxima flexibilidad y un rendimiento óptimo. Vea [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migración desde SQL Server a Azure SQL Database mediante archivos BACPAC).

- Ejecute el siguiente comando de SQLPackage en la línea de comandos para importar la base de datos **AdventureWorks2008R2** desde el almacenamiento local al servidor lógico de SQL Server que creó anteriormente a nueva base de datos, un nivel de servicio **Premium** y un objetivo de servicio **P6**. Reemplace los valores entre corchetes angulares con los valores adecuados para el servidor lógico de SQL Server y especifique un nombre para la nueva base de datos (remplace también los corchetes angulares). También puede elegir cambiar los valores de la edición de la base de datos y el objetivo de servicio según corresponda a su entorno. Para este tutorial, la base de datos migrada se denomina **myMigratedDatabase**.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![importación de sqlpackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Un servidor lógico de SQL Server escucha en el puerto 1433. Si intenta conectarse a un servidor lógico de SQL Server desde dentro de un firewall corporativo, este puerto debe estar abierto en el firewall corporativo para poder conectarse correctamente.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Conexión con SQL Server Management Studio (SSMS)

Use SQL Server Management Studio para establecer una conexión con el servidor de Azure SQL Database y la nueva base de datos migrada, denominada **myMigratedDatabase** en este tutorial. Si está ejecutando SQL Server Management Studio en un equipo diferente del que ejecutó SQLPackage, cree una regla de firewall para este equipo usando los pasos del procedimiento anterior.

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

Puede modificar el nivel de servicio, el nivel de rendimiento y el nivel de compatibilidad con SQL Server Management Studio. Durante la fase de importación, se recomienda importar a una base de datos de nivel superior de rendimiento para mejorar el rendimiento, pero reducir verticalmente una vez finalizada la importación para ahorrar dinero hasta que esté listo para usar de forma activa la base de datos importada. Al cambiar el nivel de compatibilidad se puede obtener un mejor rendimiento y tener acceso a las funcionalidades más recientes del servicio Azure SQL Database. Cuando se migra una base de datos anterior, el nivel de compatibilidad de base de datos se mantiene en el nivel más bajo admitido que es compatible con la base de datos que se va a importar. Para más información, vea [Improved query performance with compatibility Level 130 in Azure SQL Database](sql-database-compatibility-level-query-performance-130.md) (Rendimiento mejorado de consultas con el nivel de compatibilidad 130 en Azure SQL Database).

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
En este tutorial ha preparado, exportado e importado una base de datos. Ha aprendido a:

> [!div class="checklist"]
> * Preparar una base de datos en un servidor SQL Server para migrarla a Azure SQL Database
> * Exportar la base de datos a un archivo BACPAC
> * Importar el archivo BACPAC en Azure SQL Database

En el siguiente tutorial aprenderá a proteger una base de datos.

> [!div class="nextstepaction"]
> [Protección de Azure SQL Database](sql-database-security-tutorial.md).



