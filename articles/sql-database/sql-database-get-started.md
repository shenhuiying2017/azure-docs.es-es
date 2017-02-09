---
title: "Azure Portal: introducción a Azure SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos con Azure Portal. También aprenderá a consultar las bases de datos con SQL Server Management Studio."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 8eb4b280104cc4f05b9ed25985d0c336f15b6d0f


---
# <a name="sql-database-tutorial-get-started-with-azure-sql-database-servers-databases-and-firewall-rules-using-the-azure-portal-and-sql-server-management-studio"></a>Tutorial de SQL Database: introducción a los servidores, las bases de datos y las reglas de firewall de Azure SQL Database mediante Azure Portal y SQL Server Management Studio

En este tutorial de introducción, aprenderá a usar Azure Portal para:

* Crear un nuevo grupo de recursos de Azure
* Crear un servidor lógico de Azure SQL
* Ver las propiedades de servidor lógico de Azure SQL
* Crear una regla de firewall de nivel de servidor
* Creación de la base de datos de ejemplo Adventure Works LT como una base de datos única
* Ver las propiedades de la base de datos de ejemplo Adventure Works LT en Azure

En este tutorial, también se usa la versión más reciente de SQL Server Management Studio para:

* Conectar con el servidor lógico y su base de datos maestra
* Consultar la base de datos maestra
* Conectar con la base de datos de ejemplo
* Consultar la base de datos de ejemplo

Cuando termine este tutorial, tendrá una base de datos de ejemplo y una base de datos vacía en ejecución en un grupo de recursos de Azure y se conectan a un servidor lógico. También tendrá una regla de firewall de nivel de servidor configurada para que la entidad de seguridad de nivel de servidor pueda iniciar sesión en el servidor desde una dirección IP especificada (o intervalo de direcciones IP). 

**Tiempo estimado**: este tutorial le llevará aproximadamente 30 minutos (si ya cumple los requisitos previos).

## <a name="prerequisites"></a>Requisitos previos

* Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Debe poder conectarse a Azure Portal mediante una cuenta que sea miembro del rol de colaborador o propietario de la suscripción. Para más información sobre el acceso basado en roles (RBAC), consulte [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

> [!TIP]
> Puede realizar las mismas tareas del tutorial de introducción con [C#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).
>

### <a name="sign-in-by-using-your-existing-account"></a>Inicio de sesión con una cuenta existente
Con una [suscripción existente](https://account.windowsazure.com/Home/Index), siga estos pasos para conectarse al portal de Azure.

1. Abra el explorador que prefiera y conéctese al [Portal de Azure](https://portal.azure.com/).
2. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
3. En la página **Iniciar sesión** , proporcione las credenciales de la suscripción.
   
   ![Iniciar sesión](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Creación un nuevo servidor SQL lógico en Azure Portal

1. Haga clic en **Nuevo**, escriba **sql server** y haga clic en **ENTRAR**.

    ![servidor sql lógico](./media/sql-database-get-started/logical-sql-server.png)
2. Haga clic en **SQL server (servidor lógico)**.
   
    ![crear-servidor sql lógico](./media/sql-database-get-started/create-logical-sql-server.png)
3. Haga clic en **Crear** para abrir la nueva hoja SQL Server (servidor lógico).

    ![nuevo-servidor sql lógico](./media/sql-database-get-started/new-logical-sql-server.png)
3. En el cuadro de texto Nombre de servidor, proporcione un nombre válido para el nuevo servidor lógico. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![nuevo nombre de servidor](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > El nombre completo del nuevo servidor será <nombreDeSuServidor >.database.windows.net.
    >
    
4. En el cuadro de texto de inicio de sesión del administrador del servidor, proporcione un nombre de usuario para el inicio de sesión de autenticación de SQL para este servidor. Este inicio de sesión se conoce como el inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![inicio de sesión de administrador de SQL](./media/sql-database-get-started/sql-admin-login.png)
5. En los cuadros de texto **Contraseña** y **Confirmar contraseña**, escriba una contraseña para la cuenta de inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado una contraseña válida.
    
    ![contraseña del administrador de SQL](./media/sql-database-get-started/sql-admin-password.png)
6. Seleccione una suscripción en la que tenga permiso para crear objetos.

    ![suscripción](./media/sql-database-get-started/subscription.png)
7. En el cuadro de texto Grupo de recursos, seleccione **Crear nuevo** y, en el cuadro de texto del grupo de recursos, proporcione un nombre válido para el nuevo grupo de recursos (también puede usar un grupo de recursos existente si ya ha creado uno para sí mismo). Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![nuevo grupo de recursos](./media/sql-database-get-started/new-resource-group.png)

8. En el cuadro de texto **Ubicación**, seleccione un centro de datos adecuado para su ubicación; por ejemplo, "Este de Australia".
    
    ![ubicación del servidor](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > La casilla de verificación **Permitir que los servicios de Azure accedan al servidor** no se puede cambiar en esta hoja. Puede cambiar esta configuración en la hoja del firewall del servidor. Para más información, consulte [Get started with security](sql-database-get-started-security.md) (Introducción a la seguridad).
    >
    
9. Haga clic en **Crear**.

    ![botón Crear](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-sql-server-properties-in-the-azure-portal"></a>Visualización de las propiedades de SQL Server lógicas en Azure Portal

1. En Azure Portal, haga clic en **Más servicios**.

    ![más servicios](./media/sql-database-get-started/more-services.png)
2. En el cuadro de texto Filtrar, escriba **SQL** y, después, haga clic en la estrella de los servidores de SQL Server para especificarlos como favoritos en Azure. 

    ![establecer favorito](./media/sql-database-get-started/favorite.png)
3. En la hoja predeterminada, haga clic en **Servidores SQL Server** para abrir la lista de servidores SQL Server en su suscripción de Azure. 

    ![nuevo servidor sql](./media/sql-database-get-started/new-sql-server.png)

4. Haga clic en el nuevo servidor SQL para ver sus propiedades en Azure Portal. Los tutoriales siguientes le ayudarán a comprender las opciones disponibles en esta hoja.

    ![hoja sql server](./media/sql-database-get-started/sql-server-blade.png)
5. En Configuración, haga clic en **Propiedades** para ver varias propiedades del servidor SQL lógico.

    ![propiedades de sql server](./media/sql-database-get-started/sql-server-properties.png)
6. Copie el nombre completo del servidor en el Portapapeles para usarlo un poco más adelante en este tutorial.

    ![nombre completo de sql server](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creación de una regla de firewall de nivel a servidor en Azure Portal

1. En la hoja SQL Server, en Configuración, haga clic en **Firewall** para abrir la hoja Firewall para el servidor SQL Server.

    ![firewall de sql server](./media/sql-database-get-started/sql-server-firewall.png)

2. Revise la dirección IP del cliente que se muestra y compruebe que se trata de la dirección IP en Internet en el explorador que desee (pregunte "¿Cuál es mi dirección IP?”). En ocasiones no coinciden por distintas razones.

    ![su dirección IP](./media/sql-database-get-started/your-ip-address.png)

3. Si las direcciones IP coinciden, haga clic en **Agregar IP de cliente**, en la barra de herramientas.

    ![agregar IP de cliente](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Puede abrir el firewall de SQL Database en el servidor para una sola dirección IP o para un intervalo de direcciones. Abrir el firewall permite a los administradores y usuarios de SQL iniciar sesión en cualquier base de datos del servidor para el que tengan unas credenciales válidas.
    >

4. Haga clic en **Guardar** en la barra de herramientas para guardar esta regla de firewall de nivel de servidor y, después, haga clic en **Aceptar**.

    ![agregar IP de cliente](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Conexión con SQL Server mediante SQL Server Management Studio (SSMS)

1. Si aún no lo ha hecho descargue e instale la versión más reciente de SSMS en [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para estar siempre actualizado, la versión más reciente de SSMS le avisará cuando haya una nueva versión disponible para su descarga.

2. Después de la instalación, escriba **Microsoft SQL Server Management Studio** en el cuadro de búsqueda de Windows y haga clic en **Entrar** para abrir SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. En el cuadro de diálogo Conectar con el servidor, escriba la información necesaria para conectarse a su servidor SQL Server mediante la autenticación de SQL Server.

    ![conectar con el servidor](./media/sql-database-get-started/connect-to-server.png)
4. Haga clic en **Conectar**.

    ![conectado al servidor](./media/sql-database-get-started/connected-to-server.png)
5. En el Explorador de objetos, expanda **Bases de datos**, expanda **Bases de datos de sistema** y expanda **Maestra** para ver los objetos de la base de datos maestra.

    ![base de datos maestra](./media/sql-database-get-started/master-database.png)
6. Haga clic con el botón derecho en **maestra** y luego haga clic en **Nueva consulta**.

    ![base de datos maestra de consulta](./media/sql-database-get-started/query-master-database.png)

8. Escriba la siguiente consulta en la ventana de consulta:

   ```select * from sys.objects```

9.  En la barra de herramientas, haga clic en **Ejecutar** para devolver una lista de todos los objetos del sistema en la base de datos maestra.

    ![objetos de sistema de la base de datos maestra de consulta](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Para explorar la seguridad de SQL, consulte la [introducción a la seguridad de SQL](sql-database-get-started-security.md).
    >

## <a name="create-new-database-in-the-azure-portal-using-adventure-works-lt-sample"></a>Creación de una nueva base de datos en Azure Portal mediante el ejemplo Adventure Works LT

1. En Azure Portal, haga clic en **Bases de datos SQL**, en la hoja predeterminada.

    ![bases de datos sql](./media/sql-database-get-started/new-sql-database.png)
2. En la hoja Bases de datos SQL, haga clic en **Agregar**.

    ![agregar base de datos sql](./media/sql-database-get-started/add-sql-database.png)
3. En la hoja SQL Database, revise la información que se completa automáticamente.

    ![hoja sql database](./media/sql-database-get-started/sql-database-blade.png)
4. Proporcione un nombre de base de datos válido.

    ![nombre de la base de datos sql](./media/sql-database-get-started/sql-database-name.png)
5. En Seleccionar origen, haga clic en **Muestra** y, en Seleccionar muestra, haga clic en **AdventureWorksLT [V12]**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
6. En Servidor, indique el nombre de usuario y la contraseña de inicio de sesión del administrador del servidor.

    ![credenciales de servidor](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Al agregar una base de datos a un servidor, puede agregarse como una base de datos única (el valor predeterminado) o a un grupo elástico. Para más información sobre los grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).
    >

7. En Plan de tarifa, cambie el plan de tarifa a **Básico** (puede aumentar el plan de tarifa más adelante si lo desea pero, con fines de aprendizaje, se recomienda usar el plan de tarifa más bajo).

    ![Plan de tarifa](./media/sql-database-get-started/pricing-tier.png)
8. Haga clic en **Crear**.

    ![botón Crear](./media/sql-database-get-started/create.png)

## <a name="view-database-properties-in-the-azure-portal"></a>Visualización de las propiedades de la base de datos SQL en Azure Portal

1. En la hoja Bases de datos SQL, haga clic en la nueva base de datos para ver sus propiedades en Azure Portal. Los tutoriales siguientes le ayudarán a comprender las opciones disponibles en esta hoja. 

    ![hoja nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-blade.png)
2. Haga clic en **Propiedades** para ver información adicional acerca de la base de datos.

    ![propiedades de la nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-properties.png)

3. Haga clic en **Mostrar las cadenas de conexión de la base de datos**.

    ![cadenas de conexión de la nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Haga clic en **Introducción** y, después, haga clic en el nombre del servidor en el panel Essentials.
    
    ![panel essentials de la nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. En el panel Essentials del servidor, consulte la base de datos recién agregada.

    ![nueva base de datos de ejemplo en el panel essentials](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="connect-and-query-sample-database-using-sql-server-management-studio"></a>Conexión y consulta de una base de datos de ejemplo con SQL Server Management Studio

1. Cambie a SQL Server Management Studio y, en el Explorador de objetos, haga clic en **Bases de datos** y, después, haga clic en **Actualizar** en la barra de herramientas para ver la base de datos de ejemplo.

    ![nueva base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-ssms.png)
2. En el Explorador de objetos, expanda la nueva base de datos para ver sus objetos.

    ![objetos de nueva base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Haga clic con el botón derecho en la base de datos de ejemplo y, después, haga clic en **Nueva consulta**.

    ![nueva consulta de base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Escriba la siguiente consulta en la ventana de consulta:

   ```select * from sys.objects```
   
9.  En la barra de herramientas, haga clic en **Ejecutar** para devolver una lista de todos los objetos del sistema en la base de datos de ejemplo.

    ![objetos del sistema de consulta a la nueva base de datos de ejemplo con ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-new-blank-database-using-sql-server-management-studio"></a>Creación de una nueva base de datos vacía mediante SQL Server Management Studio

1. En el Explorador de objetos, haga clic con el botón derecho en **Bases de datos** y, después, haga clic en **Nueva base de datos**.

    ![nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > También puede hacer que SSMS cree un script de creación de base de datos para crear una nueva base de datos mediante Transact-SQL.
    >

2. En el cuadro de diálogo Nueva base de datos, proporcione un nombre de base de datos en el cuadro de texto Nombre de base de datos. 

    ![nombre de la nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. En el cuadro de diálogo Nueva base de datos, haga clic en **Opciones** y, después, cambie la edición a **Básica**.

    ![opciones de la nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Revise las demás opciones de este cuadro de diálogo que pueda modificar para una base de datos SQL de Azure. Para más información sobre estas opciones, consulte [Creación de una base de datos](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Haga clic en **Aceptar** para crear la base de datos vacía.
5. Cuando haya finalizado, actualice el nodo Base de datos en el Explorador de objetos para ver la base de datos vacía recién creada. 

    ![nueva base de datos en blanco en el explorador de objetos](./media/sql-database-get-started/new-blank-database-object-explorer.png)

> [!TIP]
> Elimine las bases de datos que no use para ahorrar dinero mientras aprende. Las bases de datos de la edición Básica se pueden restaurar en un plazo de siete días. Sin embargo, no elimine un servidor. Si lo hace, no podrá recuperar el servidor ni las bases de datos eliminadas.
>


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial, hay otros tutoriales que quizás desee explorar para afianzar lo que ha aprendido en este tutorial. 

* Si desea empezar a explorar la seguridad de Azure SQL Database, consulte el artículo de [introducción a la seguridad](sql-database-get-started-security.md).
* Si conoce Excel, consulte [Conexión a una base de datos SQL con Excel](sql-database-connect-excel.md).
* Si está listo para comenzar a codificar, elija el lenguaje de programación en [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).
* Si desea mover las bases de datos de SQL Server locales a Azure, consulte [Migrar una base de datos a SQL Database](sql-database-cloud-migrate.md).
* Si desea cargar datos en una tabla nueva desde un archivo CSV con la herramienta de línea de comandos BCP, consulte cómo [cargar datos en SQL Database desde un archivo CSV con BCP](sql-database-load-from-csv-with-bcp.md).
* Si desea empezar a crear tablas y otros objetos, consulte el tema "Crear una tabla" en [Creación de una tabla](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionales

- Para obtener una descripción general técnica, consulte [¿Qué es SQL Database?](sql-database-technical-overview.md)
- Para obtener información de precios, consulte [Precio de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Jan17_HO1-->


