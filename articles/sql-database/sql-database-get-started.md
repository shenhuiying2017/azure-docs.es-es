---
title: "Inicio rápido: su primera base de datos de Azure SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos en Azure Portal. También aprenderá a usar SQL Server Management Studio con Azure SQL Database."
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
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3
ms.lasthandoff: 02/18/2017


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Creación, conexión y consulta de su primera instancia de Azure SQL Database en Azure Portal y uso de SSMS

En este tutorial, aprenderá a crear, conectarse y consultar instancias de Azure SQL Database y a usar SQL Server Management Studio. Después de realizar este tutorial:

* Habrá creado un grupo de recursos que contiene un servidor lógico, una regla de firewall de nivel de servidor y dos bases de datos.
* Sabrá cómo ver las propiedades de servidor y de base de datos en Azure Portal y cómo usar SQL Server Management Studio.
* Sabrá cómo consultar una base de datos en Azure Portal y cómo usar SQL Server Management Studio.

**Tiempo estimado**: este tutorial dura aproximadamente 30 minutos (si ya cumple los requisitos previos).

> [!TIP]
> También puede aprender a crear, conectarse y consultar una instancia de Azure SQL Database con [PowerShell](sql-database-get-started-powershell.md) o [C#](sql-database-get-started-csharp.md).
>

> [!NOTE]
> Este tutorial le servirá para obtener información sobre el contenido de estos temas: [Introducción al servidor de SQL Database](sql-database-server-overview.md), [Introducción a SQL Database](sql-database-overview.md) e [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md). Para conocer una introducción sobre el servicio SQL Database, consulte [¿Qué es SQL Database?](sql-database-technical-overview.md)
>  

## <a name="prerequisites"></a>Requisitos previos

* **Una cuenta de Azure**. Puede [abrir una cuenta gratuita de Azure](https://azure.microsoft.com/free/) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Permisos de creación de Azure**. Debe poder conectarse a Azure Portal con una cuenta que sea miembro del rol de colaborador o propietario de la suscripción. Para más información sobre el acceso basado en roles (RBAC), consulte [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

* **SQL Server Management Studio**. Puede descargar e instalar la versión de SQL Server Management Studio (SSMS) más reciente de [Descarga de SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Utilice siempre la versión más reciente de SSMS al conectarse a Azure SQL Database, ya que se lanzan funcionalidades nuevas constantemente.

### <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Los pasos de este procedimiento muestran cómo conectarse a Azure Portal con la [cuenta de Azure](https://account.windowsazure.com/Home/Index).

1. Abra el explorador que prefiera y conéctese al [Portal de Azure](https://portal.azure.com/).
2. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
3. En la página **Iniciar sesión** , proporcione las credenciales de la suscripción.
   
   ![Iniciar sesión](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Crear un servidor lógico nuevo

Los pasos de este procedimiento le enseñan cómo crear un servidor lógico en Azure Portal en la región de su elección. Un servidor lógico es el objeto en el que se crean sus bases de datos SQL y el objeto en el que puede crear reglas de firewall para permitir que los usuarios se conecten a través del firewall de Azure SQL Database. 

1. Haga clic en **Nuevo**, escriba **sql server** y haga clic en **ENTRAR**.

    ![servidor sql lógico](./media/sql-database-get-started/logical-sql-server.png)
2. Haga clic en **SQL server (servidor lógico)**.
   
    ![crear-servidor sql lógico](./media/sql-database-get-started/create-logical-sql-server.png)
3. Haga clic en **Crear** para abrir solo la nueva hoja SQL Server (servidor lógico).

    ![nuevo-servidor sql lógico](./media/sql-database-get-started/new-logical-sql-server.png)
3. En el cuadro de texto **Nombre de servidor**, proporcione un nombre válido para el nuevo servidor lógico. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![nuevo nombre de servidor](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > El nombre completo del nuevo servidor debe ser único y tener el formato **<nombre_deSu_servidor>.database.windows.net**. Use este nombre completo de servidor más adelante en este tutorial para conectarse a su servidor y a las bases de datos.
    >
    
4. En el cuadro de texto de **inicio de sesión del administrador del servidor**, proporcione un nombre de usuario para el inicio de sesión de autenticación de SQL para este servidor. Este inicio de sesión se conoce como el inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado un nombre válido.
    
    ![inicio de sesión de administrador de SQL](./media/sql-database-get-started/sql-admin-login.png)
5. En los cuadros de texto **Contraseña** y **Confirmar contraseña**, escriba una contraseña para la cuenta de inicio de sesión de la entidad de seguridad del servidor. Una marca de verificación verde indica que ha proporcionado una contraseña válida.
    
    ![contraseña del administrador de SQL](./media/sql-database-get-started/sql-admin-password.png)
6. En el cuadro desplegable **Suscripción**, seleccione una suscripción en la que tenga permiso para crear objetos.

    ![suscripción](./media/sql-database-get-started/subscription.png)
7. En el cuadro de texto **Grupo de recursos**, seleccione **Crear nuevo** y, a continuación, proporcione un nombre válido para el nuevo grupo de recursos. Una marca de verificación verde indica que ha proporcionado un nombre válido.

    ![nuevo grupo de recursos](./media/sql-database-get-started/new-resource-group.png)

8. En el cuadro de texto **Ubicación**, seleccione el centro de datos en el que desea crear el servidor lógico.
    
    ![ubicación del servidor](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > La casilla de verificación **Permitir que los servicios de Azure accedan al servidor** no se puede cambiar en esta hoja. Puede cambiar esta configuración en la hoja del firewall del servidor. Para más información, consulte [Get started with security](sql-database-control-access-sql-authentication-get-started.md) (Introducción a la seguridad).
    >
    
9. Seleccione la casilla **Anclar al panel**.

10. Haga clic en **Crear** para implementar este script en Azure para crear el servidor lógico.

    ![botón Crear](./media/sql-database-get-started/create.png)

11. Una vez creado el servidor, revise las propiedades del servidor que se muestran de forma predeterminada. 

    ![hoja sql server](./media/sql-database-get-started/sql-server-blade.png)
12. Haga clic en **Propiedades** para ver las propiedades adicionales del servidor SQL lógico.

    ![propiedades de sql server](./media/sql-database-get-started/sql-server-properties.png)
13. Copie el nombre completo del servidor en el Portapapeles para usarlo un poco más adelante en este tutorial.

    ![nombre completo de sql server](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Crear una regla de firewall de nivel de servidor

Los pasos descritos en este procedimiento le muestran cómo crear una regla de firewall de nivel de servidor en Azure Portal. De forma predeterminada, un firewall de Azure SQL Database impide la conectividad externa a su servidor lógico y a sus bases de datos. Para poder conectarse a su servidor, debe crear una regla de firewall para la dirección IP del equipo desde el que se conecta mediante el procedimiento siguiente. Para más información, consulte [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md).

1. En la hoja SQL Server, haga clic en **Firewall** para abrir la hoja correspondiente del servidor. Tenga en cuenta que se mostrará la dirección IP del equipo cliente.

    ![firewall de sql server](./media/sql-database-get-started/sql-server-firewall.png)

2. Haga clic en **Agregar IP de cliente** en la barra de herramientas para crear una regla de firewall para la dirección IP actual.

    ![agregar IP de cliente](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Puede crear una regla de firewall para una única dirección IP o todo un intervalo de direcciones. Abrir el firewall permite a los administradores y usuarios de SQL iniciar sesión en cualquier base de datos del servidor para el que tengan unas credenciales válidas.
    >

4. Haga clic en **Guardar** en la barra de herramientas para guardar esta regla de firewall de nivel de servidor y, después, haga clic en **Aceptar** para cerrar el cuadro de diálogo Correcto.

    ![Correcto](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Conectarse al servidor con SSMS

Los pasos de este procedimiento le explican cómo conectarse con el servidor lógico de SQL con SQL Server Management Studio. SSMS es la principal herramienta que usa DBA para administrar bases de datos y servidores de SQL Server.

1. Abra SQL Server Management Studio. Para ello, escriba **Microsoft SQL Server Management Studio** en el cuadro de búsqueda de Windows y haga clic en **Entrar** para abrir SSMS.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. En el cuadro de diálogo **Conectar con el servidor**, escriba el nombre completo del servidor del procedimiento anterior, seleccione Autenticación de SQL Server y, a continuación, proporcione los datos de inicio de sesión y contraseña que especificó durante el aprovisionamiento del servidor.

    ![conectar con el servidor](./media/sql-database-get-started/connect-to-server.png)
4. Haga clic en **Conectar** para iniciar la conexión y abra el Explorador de objetos en SSMS.

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
    > Para consultar una introducción a la seguridad de SQL, consulte [Introducción a la autenticación de SQL](sql-database-control-access-sql-authentication-get-started.md)
    >

## <a name="create-a-database-with-sample-data"></a>Crear una base de datos con datos de ejemplo

Los pasos descritos en este procedimiento le explican cómo crear una base de datos con datos de ejemplo en la instancia de Azure Portal asociada con el servidor lógico que creó anteriormente. 

1. En Azure Portal, haga clic en **Bases de datos SQL**, en la hoja predeterminada.

    ![bases de datos sql](./media/sql-database-get-started/new-sql-database.png)
2. En la hoja Bases de datos SQL, haga clic en **Agregar**. 

    ![agregar base de datos sql](./media/sql-database-get-started/add-sql-database.png)

    ![hoja sql database](./media/sql-database-get-started/sql-database-blade.png)
3. En el cuadro de texto **Nombre de la base de datos**, proporcione un nombre valido de base de datos.

    ![nombre de la base de datos sql](./media/sql-database-get-started/sql-database-name.png)
4. En **Seleccionar origen**, seleccione **Ejemplo (AdventureWorksLT)**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
5. En **Servidor**, compruebe que esté seleccionado su servidor. Observe también que al agregar una base de datos a un servidor, puede agregarse como una base de datos única (este es el valor predeterminado) o agregarse a un grupo elástico. Para más información sobre los grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).

6. En **Plan de tarifa**, cambie el plan de tarifa a **Básico** y haga clic en **Seleccionar**. Puede aumentar el plan de tarifa más adelante si lo desea pero, con fines de aprendizaje, se recomienda usar el plan de tarifa más bajo.

    ![Plan de tarifa](./media/sql-database-get-started/pricing-tier.png)
7. Seleccione la casilla **Anclar al panel** y luego haga clic en **Crear**.

    ![botón Crear](./media/sql-database-get-started/create.png)

8. Una vez creada la base de datos, vea sus propiedades en Azure Portal. Los tutoriales siguientes le ayudarán a comprender las opciones disponibles en esta hoja. 

    ![hoja nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Consultar la base de datos en Azure Portal

Los pasos de este procedimiento le explican cómo consultar la base de datos en Azure Portal. 

1. En la hoja Bases de datos SQL, haga clic en **Herramientas** en la barra de herramientas.

    ![herramientas](./media/sql-database-get-started/tools.png)
2. En la hoja Herramientas, haga clic en **Editor de consultas (versión preliminar)**.

    ![editor de consultas](./media/sql-database-get-started/query-editor.png)
3. Haga clic en la casilla para confirmar que el editor de consultas es una característica en versión preliminar y después haga clic en **Aceptar**.
4. En la hoja **Editor de consultas**, haga clic en **Iniciar sesión**.

    ![hoja editor de consultas](./media/sql-database-get-started/query-editor-blade.png)
5. Revise el tipo de autorización y el inicio de sesión, e indique la contraseña para este inicio de sesión. 

    ![inicio de sesión del editor de consultas](./media/sql-database-get-started/query-editor-login.png)
6. Haga clic en **Aceptar** para intentar iniciar sesión.
7. Una vez autenticado, en la ventana de consulta, escriba la siguiente consulta y haga clic en **Ejecutar**.

   ```select * from sys.objects```

    ![consulta del editor de consultas](./media/sql-database-get-started/query-editor-query.png)

8. Revise los resultados de la consulta en el panel **Resultados**.

    ![resultados del editor de consultas](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>Consulta de la base de datos con SSMS

Los pasos de este procedimiento le explican cómo conectarse a la base de datos con SQL Server Management Studio y, después, consultar los datos de ejemplo para ver los objetos de la base de datos.

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

## <a name="create-a-blank-database-with-ssms"></a>Crear una base de datos vacía con SSMS

Los pasos de este procedimiento le explican cómo crear una nueva base de datos con SQL Server Management Studio.

1. En el Explorador de objetos, haga clic con el botón derecho en **Bases de datos** y, después, haga clic en **Nueva base de datos**.

    ![nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

2. En el cuadro de diálogo **Nueva base de datos**, proporcione un nombre de base de datos en el cuadro de texto Nombre de base de datos. 

    ![nombre de la nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. En el cuadro de diálogo Nueva base de datos, haga clic en **Opciones** y, después, cambie la edición a **Básica**.

    ![opciones de la nueva base de datos vacía con ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Revise las demás opciones de este cuadro de diálogo que pueda modificar para una base de datos SQL de Azure. Para más información sobre estas opciones, consulte [Creación de una base de datos](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Haga clic en **Aceptar** para crear la base de datos vacía.
5. Cuando haya finalizado, actualice el nodo Base de datos en el Explorador de objetos para ver la base de datos vacía recién creada. 

    ![nueva base de datos en blanco en el explorador de objetos](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Solución de problemas de conectividad

Cuando la conexión a Azure SQL Database no se logra establecer, se reciben mensajes de error. Los problemas de conexión pueden deberse a la reconfiguración de Azure SQL Database, las configuraciones de firewall, el tiempo de espera de la conexión o a una información de inicio de sesión incorrecta. Para obtener una herramienta de solución de problemas de conectividad, consulte [Solucionar problemas de conectividad con Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database).

## <a name="delete-a-single-database-in-the-azure-portal"></a>Eliminación de una base de datos única mediante Azure Portal

Los pasos de este procedimiento le explican cómo eliminar una base de datos única con Azure Portal.

1. En la hoja Bases de datos SQL del portal de Azure, haga clic en la base de datos que desea eliminar. 
2.  Para la base de datos SQL, haga clic en **eliminar**.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Haga clic en **Sí** para confirmar que desea eliminar la base de datos de forma permanente.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Durante el período de retención de la base de datos, puede restaurarla desde las copias de seguridad automáticas iniciadas por el servicio (siempre y cuando no elimine el propio servidor). Las bases de datos de la edición Básica se pueden restaurar en un plazo de siete días. En todas las demás ediciones, puede restaurarlas en un plazo de 35 días. Si elimina el servidor, no podrá recuperar el servidor ni las bases de datos eliminadas. Para más información acerca de las copias de seguridad de la base de datos, consulte [Más información sobre las copias de seguridad de SQL Database](sql-database-automated-backups.md) y, para obtener información sobre cómo restaurar una base de datos a partir de copias de seguridad, consulte el artículo sobre la [recuperación de bases de datos](sql-database-recovery-using-backups.md). Para un artículo de procedimientos sobre cómo restaurar una base de datos eliminada, consulte [Restauración de una instancia de Azure SQL Database eliminada con Azure Portal](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial, hay otros tutoriales que quizás desee explorar para afianzar lo que ha aprendido en este tutorial. 

- Para una introducción al tutorial de autenticación de SQL Server, consulte [Autenticación y autorización de SQL](sql-database-control-access-sql-authentication-get-started.md)
- Para una introducción al tutorial de autenticación de Azure Active Directory, consulte [Autenticación y autorización de AAD](sql-database-control-access-aad-authentication-get-started.md)
* Si desea consultar la base de datos de ejemplo en Azure Portal, consulte [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/) (Versión preliminar pública: experiencia de consultas interactiva para instancias de SQL Database).
* Si conoce Excel, consulte [Conexión a una base de datos SQL con Excel](sql-database-connect-excel.md).
* Si está listo para comenzar a codificar, elija el lenguaje de programación en [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).
* Si desea mover las bases de datos de SQL Server locales a Azure, consulte [Migrar una base de datos a SQL Database](sql-database-cloud-migrate.md).
* Si desea cargar datos en una tabla nueva desde un archivo CSV con la herramienta de línea de comandos BCP, consulte cómo [cargar datos en SQL Database desde un archivo CSV con BCP](sql-database-load-from-csv-with-bcp.md).
* Si desea empezar a crear tablas y otros objetos, consulte el tema "Crear una tabla" en [Creación de una tabla](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Recursos adicionales

- Para obtener una descripción general técnica, consulte [¿Qué es SQL Database?](sql-database-technical-overview.md)
- Para obtener información de precios, consulte [Precio de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).


