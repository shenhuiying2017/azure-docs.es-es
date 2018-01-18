## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Crear una instancia en blanco en SQL Database

Se crea una instancia de Azure SQL Database con un conjunto definido de [recursos de proceso y almacenamiento](../articles/sql-database/sql-database-service-tiers.md). La base de datos se crea dentro de un [grupo de recursos de Azure](../articles/azure-resource-manager/resource-group-overview.md) y en un [servidor lógico de Azure SQL Database](../articles/sql-database/sql-database-features.md). 

Siga estos pasos para crear una instancia en blanco de SQL Database. 

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Bases de datos** en la página **Nuevo** y seleccione **Crear** en **SQL Database** en la página **Nuevo**.

   ![crear una base de datos en blanco](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Rellene el formulario de SQL Database con la siguiente información, como se muestra en la imagen anterior:   

   | Configuración       | Valor sugerido | DESCRIPCIÓN | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nombre de la base de datos** | mySampleDatabase | Para conocer los nombres de base de datos válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos). | 
   | **Suscripción** | Su suscripción  | Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   | **Grupos de recursos** | myResourceGroup | Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   | **Seleccionar origen** | Base de datos en blanco | Especifica que se debe crear una base de datos en blanco. |

4. Haga clic en **Servidor** para crear y configurar un servidor nuevo para la nueva base de datos. Rellene el **formulario de servidor nuevo** con la siguiente información: 

   | Configuración       | Valor sugerido | DESCRIPCIÓN | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nombre del servidor** | Cualquier nombre globalmente único | Para conocer cuáles son los nombres de servidor válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). | 
   | **Inicio de sesión del administrador del servidor** | Cualquier nombre válido | Para conocer los nombres de inicio de sesión válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos).|
   | **Password** | Cualquier contraseña válida | La contraseña debe tener un mínimo de 8 caracteres y debe contener caracteres de tres de las siguientes categorías: caracteres en mayúsculas, caracteres en minúsculas, números y caracteres no alfanuméricos. |
   | **Ubicación** | Cualquier ubicación válida | Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). |

   ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Haga clic en **Seleccionar**.

6. Haga clic en **Plan de tarifa** para especificar el nivel de servicio, el número de DTU y la cantidad de almacenamiento. Explore las opciones de cantidad de almacenamiento y de DTU que están a su disposición para cada nivel de servicio. 

7. Para este tutorial, seleccione el nivel de servicio **Estándar** y, a continuación, utilice el control deslizante para seleccionar **100 DTU (S3)** y **400** GB de almacenamiento.

   ![create database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Acepte los términos de la versión preliminar para usar la opción de **almacenamiento de complementos**. 

   > [!IMPORTANT]
   > \* Los tamaños de almacenamiento mayores que la cantidad de almacenamiento incluida están en su versión preliminar y pueden generar costos adicionales. Para obtener información detallada, vea [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* En el nivel Premium, más de 1 TB de almacenamiento se encuentra actualmente disponible en las siguientes regiones: este de Australia, sureste de Australia, centro de Canadá, este de Canadá, centro de Francia, centro de Alemania, este de Japón, centro de Corea, centro y sur de EE. UU., sudeste de Asia, este de EE. UU. 2, oeste de EE. UU., Virginia Gob. EE. UU. y Europa Occidental. Consulte [Limitaciones actuales P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Después de seleccionar el nivel del servidor, el número de DTU y la cantidad de almacenamiento, haga clic en **Aplicar**.  

10. Seleccione una **intercalación** para la base de datos en blanco (para este tutorial, use el valor predeterminado). Para más información sobre las intercalaciones, vea [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Intercalaciones)

11. Haga clic en **Create** (Crear) para realizar el aprovisionamiento de la base de datos. El aprovisionamiento tarda un minuto y medio aproximadamente en completarse. 

12. En la barra de herramientas, haga clic en **Notificaciones** para supervisar el proceso de implementación.
    
     ![notificación](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Crear una regla de firewall de nivel de servidor

El servicio SQL Database crea un firewall en el nivel de servidor, lo que impide que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos del servidor, a menos que se cree una regla de firewall para abrir el firewall para direcciones IP concretas. Siga estos pasos para crear una [regla de firewall de nivel de servidor de SQL Database](../articles/sql-database/sql-database-firewall-configure.md) para la dirección IP de su cliente y habilite la conectividad externa a través de dicho firewall solo para su dirección IP. 

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el departamento de TI abra el puerto 1433.
>

1. Cuando se haya finalizado la implementación, haga clic en **Bases de datos SQL** en el menú de la izquierda y, después, haga clic en **mySampleDatabase** en la página **Bases de datos SQL**. Se abre la página de información general de la base de datos, que muestra el nombre completo del servidor (por ejemplo, **mynewserver20170824.database.windows.net**) y proporciona opciones para configurarlo aún más. 

2. Copie este nombre para conectarse a su servidor y a sus bases de datos en los inicios rápidos posteriores. 

   ![nombre del servidor](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

3. Haga clic en **Establecer el firewall del servidor** en la barra de herramientas. Se abrirá la página **Configuración del firewall** del servidor de SQL Database. 

   ![regla de firewall del servidor](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Haga clic en **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP actual a la nueva regla de firewall. La regla de firewall puede abrir el puerto 1433 para una única dirección IP o un intervalo de direcciones IP.

5. Haga clic en **Save**(Guardar). Se crea una regla de firewall de nivel de servidor para el puerto 1433 de la dirección IP actual en el servidor lógico.

6. Haga clic en **Aceptar** y después cierre la página **Configuración de firewall**.

Ahora puede conectarse al servidor de SQL Database y a sus bases de datos mediante SQL Server Management Studio o cualquier otra herramienta que elija desde esta dirección IP usando la cuenta de administrador del servidor creada con anterioridad.


> [!IMPORTANT]
> De forma predeterminada, el acceso a través del firewall de SQL Database está habilitado para todos los servicios de Azure. Haga clic en **OFF** en esta página para deshabilitar todos los servicios de Azure.

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante SQL Server Management Studio.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **SQL Database** en el menú de la izquierda y haga clic en la base de datos en la página **SQL Database**. 
3. En el panel **Essentials** de la página de Azure Portal de la base de datos, busque y copie el **nombre del servidor**.

   ![información sobre la conexión](../articles/sql-database/media/sql-database-get-started-portal/server-name.png)
