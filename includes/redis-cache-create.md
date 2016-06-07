Para crear una memoria caché, primero inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Nuevo**, **Datos y almacenamiento** y **Caché en Redis**.

>[AZURE.NOTE] Si no tiene una cuenta de Azure, puede [abrir una cuenta de Azure gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) en tan solo un par de minutos.

![New cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

>[AZURE.NOTE] Además de crear memorias caché en el Portal de Azure, también puede crearlas mediante las plantillas de ARM, PowerShell o la CLI de Azure.
>
>-	Para crear una caché mediante plantillas ARM, consulte [Creación de una caché en Redis mediante una plantilla](../articles/redis-cache/cache-redis-cache-arm-provision.md).
>-	Para crear una caché con Azure PowerShell, consulte [Administración de Caché en Redis de Azure con Azure PowerShell](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md).
>-	Para crear una caché mediante la CLI de Azure, consulte [Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)](../articles/redis-cache/cache-manage-cli.md).

En la hoja **Nueva caché en Redis**, especifique la configuración que desee para la memoria caché.

![Create cache](media/redis-cache-create/redis-cache-cache-create.png)

-	En **Nombre DNS**, especifique el nombre de la memoria caché que se va a usar para el punto de conexión de la memoria caché. El nombre de la memoria caché debe ser una cadena de entre 1 y 63 caracteres y solo puede contener números, letras y el carácter `-`. El nombre de la memoria caché no puede comenzar ni terminar por el carácter `-` y no se pueden usar varios caracteres `-` consecutivos.
-	En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la memoria caché. Si su cuenta solo dispone de una suscripción, esta se seleccionará automáticamente y no aparecerá la lista desplegable **Suscripción**.
-	En **Grupo de recursos**, seleccione o cree un grupo de recursos para su caché. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../articles/resource-group-overview.md). 
-	Use **Ubicación** para especificar la ubicación geográfica en la que se hospeda su caché. Para optimizar el rendimiento, Microsoft recomienda encarecidamente que cree la memoria caché en la misma región que la aplicación cliente de caché.
-	Use **Nivel de precios** para seleccionar el tamaño y las características de caché que desee.
-	**Redis clúster** le permite crear cachés más grandes de 53 GB y los datos de partición entre varios nodos de Redis. Para obtener más información, consulte [Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](../articles/redis-cache/cache-how-to-premium-clustering.md).
-	**Persistencia de Redis** ofrece la posibilidad de conservar la memoria caché para una cuenta de Almacenamiento de Azure. Para obtener instrucciones sobre cómo configurar la persistencia, consulte [Configuración de la persistencia para una Caché en Redis de Azure de nivel Premium](../articles/redis-cache/cache-how-to-premium-persistence.md).
-	**Red virtual** ofrece seguridad y aislamiento mejorados al restringir el acceso a la memoria caché solo a los clientes dentro de la red virtual de Azure especificada. Además, puede usar todas las características de la red virtual, como las subredes y las directivas de control de acceso, entre otras, para restringir aún más el acceso a Redis. Para más información, vea [Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium](../articles/redis-cache/cache-how-to-premium-vnet.md).

Una vez que las nuevas opciones de caché estén configuradas, haga clic en **Crear**. La creación de la caché puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. Una vez que se cree la memoria caché, esta presentará el estado **En ejecución** y estará lista para usarse con la configuración predeterminada.

![Cache created](media/redis-cache-create/redis-cache-cache-created.png)

<!---HONumber=AcomDC_0601_2016-->