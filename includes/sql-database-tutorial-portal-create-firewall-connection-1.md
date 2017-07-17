<a id="log-in-to-the-azure-portal" class="xliff"></a>

## Iniciar sesión en el portal de Azure

Inicie sesión en [Azure Portal](https://portal.azure.com/).

<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

## Creación de una base de datos SQL mediante Azure Portal

Se crea una base de datos SQL de Azure con un conjunto definido de [recursos de proceso y almacenamiento](../articles/sql-database/sql-database-service-tiers.md). La base de datos se crea dentro de un [grupo de recursos de Azure](../articles/azure-resource-manager/resource-group-overview.md) y en un [servidor lógico de Azure SQL Database](../articles/sql-database/sql-database-features.md). 

Siga estos pasos para crear una instancia en blanco de SQL Database. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **SQL Database**. 

   ![crear una base de datos en blanco](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Rellene el formulario de SQL Database con la siguiente información, como se muestra en la imagen anterior:   

   | Configuración | Valor sugerido | Descripción |
   | --------| --------------- | ----------- | 
   | **Nombre de la base de datos** | mySampleDatabase | Para conocer los nombres de base de datos válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos). | 
   | **Suscripción** | Su suscripción  | Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   | **Grupos de recursos** | myResourceGroup | Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   | **Seleccionar origen** | Base de datos en blanco | Especifica que se debe crear una base de datos en blanco. |
   ||||

4. Haga clic en **Servidor** para crear y configurar un servidor nuevo para la nueva base de datos. Rellene el **formulario de servidor nuevo** con la siguiente información: 

   | Configuración | Valor sugerido | Descripción |
   | --------| --------------- | ----------- | 
   | **Nombre del servidor** | Cualquier nombre globalmente único. | Para conocer cuáles son los nombres de servidor válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). | 
   | **Inicio de sesión del administrador del servidor** | Cualquier nombre válido. | Para conocer los nombres de inicio de sesión válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos).|
   | **Password** | Cualquier contraseña válida. | La contraseña debe tener un mínimo de ocho caracteres y debe contener caracteres de tres de las siguientes categorías: caracteres en mayúsculas, caracteres en minúsculas, números y caracteres no alfanuméricos. |
   | **Ubicación** | Cualquier ubicación válida. | Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). |
   ||||

   ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Haga clic en **Seleccionar**.

6. Haga clic en **Plan de tarifa** para especificar el tanto el nivel de rendimiento como el nivel de servicio de la nueva base de datos. Para este tutorial, seleccione **20 DTU** y **250** GB de almacenamiento.

   ![create database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Haga clic en **Apply**.  

8. Seleccione un **intercalación** para la base de datos en blanco (para este tutorial, utilice el valor predeterminado). Para más información acerca de las intercalaciones, consulte [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Intercalaciones)

9. Haga clic en **Create** (Crear) para realizar el aprovisionamiento de la base de datos. El aprovisionamiento tarda un minuto y medio aproximadamente en completarse. 

10. En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.

   ![notificación](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

## Creación de una regla de firewall de nivel a servidor mediante Azure Portal

El servicio SQL Database crea un firewall en el nivel de servidor. Inicialmente, el firewall impide que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos del servidor. Se permiten las conexiones después de que se cree una regla de firewall para abrir direcciones IP concretas. Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](../articles/sql-database/sql-database-firewall-configure.md) para la dirección IP de su cliente y para habilitar la conectividad externa a través de dicho firewall solo para su dirección IP. 


> [!NOTE]
> Azure SQL Database se comunica a través del puerto 1433. Solo puede conectarse a SQL Database después de que el firewall de la red permita el tráfico saliente a través del puerto 1433.


1. Cuando se haya finalizado la implementación, haga clic en **Bases de datos SQL** en el menú de la izquierda y, después, haga clic en **mySampleDatabase** en la página **Bases de datos SQL**. Se abre la página de información general de la base de datos, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170313.database.windows.net**) y proporciona opciones para configurarlo aún más. Copie dicho nombre, ya que lo tendrá que usar más adelante,

   > [!IMPORTANT]
   > lo necesitará para conectarse a su servidor y a sus bases de datos en los inicios rápidos posteriores.
   > 

   ![nombre del servidor](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. Haga clic en **Establecer el firewall del servidor** en la barra de herramientas, como se muestra en la imagen anterior. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

   ![regla de firewall del servidor](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP actual a la nueva regla de firewall. La regla de firewall puede abrir el puerto 1433 para una única dirección IP o un intervalo de direcciones IP.

4. Haga clic en **Guardar**. Se crea una regla de firewall de nivel de servidor para el puerto 1433 de la dirección IP actual en el servidor lógico.

   ![establecer regla de firewall del servidor](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Haga clic en **Aceptar** y después cierre la página **Configuración de firewall**.

Ahora puede conectarse al servidor de Azure SQL Database y sus bases de datos mediante una herramienta como SQL Server Management Studio (SSMS). La conexión se realiza desde esta dirección IP y usa la cuenta de administrador de servidor que se creó anteriormente.


> [!IMPORTANT]
> De forma predeterminada, el acceso a través del firewall de SQL Database está habilitado para todos los servicios de Azure. Haga clic en **OFF** en esta página para deshabilitar todos los servicios de Azure.


<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

## Obtención de los valores de la cadena de conexión de cadena mediante Azure Portal

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante SQL Server Management Studio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 

3. En el panel **Essentials** de la página de Azure Portal de la base de datos, busque y copie el **nombre del servidor**.

   ![información sobre la conexión](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
