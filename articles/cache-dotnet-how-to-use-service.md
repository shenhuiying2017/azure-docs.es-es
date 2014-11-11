<properties linkid="manage-services-cache-net-how-to-cache-service" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="cache" documentationCenter="" title="How to Use Azure Managed Cache Service" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Cómo usar el Servicio de caché administrado de Azure

En esta guía se explica cómo comenzar a usar el
**Servicio de caché administrado de Azure**. Los ejemplos se escriben en código C# y usan la API .NET. Entre los escenarios tratados se incluyen **la creación y configuración de una caché**, **la configuración de clientes de caché**, **la incorporación y eliminación de
objetos en la caché, el almacenamiento del estado de sesión ASP.NET en la caché**
 y **la activación de la caché de resultados de la página ASP.NET con el uso de la caché**. Para obtener más
información acerca del uso del servicio de caché de Azure, consulte la sección [Pasos siguientes][Pasos siguientes].

> Si quiere que le guiemos a la hora de elegir la oferta de Caché de Azure que mejor se adapta a su aplicación, consulte [¿Cuál es la oferta de Caché de Azure más adecuada para mí?][¿Cuál es la oferta de Caché de Azure más adecuada para mí?].

## Tabla de contenido

-   [¿Qué es el Servicio de caché administrado de Azure?][¿Qué es el Servicio de caché administrado de Azure?]
-   [Introducción al Servicio de caché administrado][Introducción al Servicio de caché administrado]

    -   [Creación de la caché][Creación de la caché]
    -   [Configuración de la caché][Configuración de la caché]
    -   [Configuración de los clientes de caché][Configuración de los clientes de caché]
-   [Uso de cachés][Uso de cachés]

    -   [Eliminación de un objeto DataCache][Eliminación de un objeto DataCache]
    -   [Eliminación de y recuperación de un objeto de la caché][Eliminación de y recuperación de un objeto de la caché]
    -   [Eliminación de de la expiración de un objeto de la caché][Eliminación de de la expiración de un objeto de la caché]
    -   [Eliminación de del estado de la sesión de ASP.NET en la caché][Eliminación de del estado de la sesión de ASP.NET en la caché]
    -   [Eliminación de de la caché de resultados de la página ASP.NET en la caché][Eliminación de de la caché de resultados de la página ASP.NET en la caché]
-   [Pasos siguientes][Pasos siguientes]

<a name="what-is"></a>

## ¿Qué es el Servicio de caché administrado de Azure?

El Servicio de caché administrado de Azure es una solución escalable, distribuida y en memoria que permite al usuario desarrollar aplicaciones con gran capacidad de escalado y respuesta dotándolo de acceso ultrarrápido a los datos.

Servicio de caché administrado de Azure incluye las características
siguientes:

-   Proveedores de ASP.NET pregenerados para el almacenamiento en caché del estado
    de la sesión y los resultados de la página, lo que permite acelerar las aplicaciones web sin
    necesidad de modificar su código.
-   Almacenamiento en caché de cualquier objeto administrado serializable; por ejemplo: objetos CLR, filas, XML y
    datos binarios.
-   Modelo de desarrollo coherente en Azure y Windows
    Server AppFabric.

El Servicio de caché administrado ofrece acceso a una caché segura y dedicada administrada por Microsoft. Las memorias caché creadas a través del Servicio de caché administrado son accesibles desde aplicaciones de Azure que se ejecutan en Sitios web de Azure, roles web y de trabajo máquinas virtuales.

El Servicio de caché administrado está disponible en tres niveles:

-   Básico: tamaños de caché de 128 MB a 1 GB
-   Estándar: tamaños de caché de 1 GB a 10 GB
-   Premium: tamaños de caché de 5 GB a 150 GB

Estos niveles difieren en las características y el precio. Las características se tratan más adelante en esta guía; por otro lado, para obtener más información acerca de los precios consulte [Detalles de precios de caché][Detalles de precios de caché].

Esta guía contiene información general para iniciarse en el uso del Servicio de caché administrado. Para obtener información más detallada acerca de estas características, que se salen del ámbito de esta guía introductoria, consulte [Información general del Servicio de caché administrado de Azure][Información general del Servicio de caché administrado de Azure].

<a name="getting-started-cache-service"></a>

## Introducción al Servicio de caché

Comenzar a usar el Servicio de caché administrado es muy fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

-   [Creación de la caché][Creación de la caché]
-   [Configuración de la caché][Configuración de la caché]
-   [Configuración de los clientes de caché][Configuración de los clientes de caché]

<a name="create-cache"></a>

## Creación de una caché

Las instancias de caché del Servicio de caché administrado se crean usando cmdlets de PowerShell.

> Una vez que se haya creado una instancia del Servicio de caché administrado con los cmdlets de PowerShell, se puede ver y configurar en el [Portal de administración de Azure][Portal de administración de Azure].

Para crear una instancia del Servicio de caché administrado, abra una ventana de comandos de Azure PowerShell.

> Para obtener instrucciones de instalación y uso de Azure PowerShell, vea [Cómo instalar y configurar Azure PowerShell][Cómo instalar y configurar Azure PowerShell].

Invoque el cmdlet [Add-AzureAccount][Add-AzureAccount] y escriba la dirección de correo electrónico y la contraseña asociadas a la cuenta. Hay una suscripción seleccionada de manera predeterminada que se muestra tras invocar el cmdlet [Add-AzureAccount][Add-AzureAccount]. Para cambiar la suscripción, invoque el cmdlet [Select-AzureSubscription][Select-AzureSubscription].

> Si ha configurado Azure PowerShell con un certificado para la cuenta, puede pasar por alto este paso. Para obtener más información sobre la conexión de Azure PowerShell con su cuenta de Azure, consulte [Instalación y configuración de Azure PowerShell][Cómo instalar y configurar Azure PowerShell].

Hay una suscripción seleccionada de manera predeterminada que se muestra. Para cambiar la suscripción, invoque el cmdlet [Select-AzureSubscription][Select-AzureSubscription].

Invoque el cmdlet [New-AzureManagedCache][New-AzureManagedCache] y especifique el nombre, la región, la oferta de caché y el tamaño de la memoria caché.

En **Nombre**, escriba un nombre de subdominio para usar el extremo de caché. El nombre debe tener entre seis y veinte caracteres, solo puede contener números y letras minúsculas, y debe comenzar por una letra.

En **Ubicación**, seleccione una región para la memoria caché. Para optimizar el rendimiento, cree la caché en la misma región que la aplicación cliente de caché.

**Sku** y **Memoria** funcionan en conjunto para determinar el tamaño de la memoria caché. El Servicio de caché administrado está disponible en los tres niveles siguientes:

-   Básico: Tamaños de caché de 128 MB a 1 GB en incrementos de 128 MB, con una caché con nombre predeterminada.
-   Estándar: Tamaños de caché de 1 GB a 10 GB en incrementos de 1 GB, compatible con las notificaciones, con hasta diez cachés con nombre.
-   Premium: Tamaños de caché de 5 GB a 150 GB en incrementos de 5 GB, compatible con las notificaciones, con alta disponibilidad y hasta diez cachés con nombre.

Elija los valores de **Sky** y **Memoria** que se ajusten a las necesidades de su aplicación. Tenga en cuenta que algunas características de las cachés, como las notificaciones y la alta disponibilidad, solo están disponibles con determinadas ofertas de caché. Para obtener más información acerca de cómo elegir la oferta y el tamaño de caché que mejor se ajusten a su aplicación, consulte [Ofertas de caché][Ofertas de caché].

En el siguiente ejemplo, se crea una caché de la oferta Basic de 128 MB con el nombre contosocache, en la región geográfica South Central US.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Para obtener una lista completa de parámetros y valores que se pueden usar cuando se crea una caché, vea la documentación del cmdlet [New-AzureManagedCache][New-AzureManagedCache].

Tras invocar al cmdlet de PowerShell, la creación de la memoria caché puede tardar unos minutos. Una vez creada la memoria caché, tiene el estado `Running`, está preparada para usarla con la configuración predeterminada y se puede ver y configurar en el [Portal de administración de Azure][Portal de administración de Azure]. Para personalizar la configuración de la caché, consulte la sección siguiente, [Configuración de la caché][Configuración de la caché].

Puede supervisar el progreso de creación en la ventana de Azure PowerShell. Cuando la memoria caché está preparada para su uso, el cmdlet [New-AzureManagedCache][New-AzureManagedCache] expone la información de caché, como se muestra en el siguiente ejemplo.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>

<a name="enable-caching"></a>

## Configuración de la caché

Para configurar las opciones de la caché, debe utilizar la pestaña **Configure** correspondiente al servicio de caché del Portal de administración. Toda caché tiene una caché con nombre **predeterminada**, pero las ofertas de caché estándar y premium admiten hasta nueve cachés con nombre adicionales, que hacen un total de diez. Cada caché con nombre dispone de opciones propias que le permiten configurar la caché de una manera muy flexible.

![CachésConNombre][CachésConNombre]

Para crear una caché con nombre, escriba el nombre de la nueva caché en el cuadro **Name**, especifique las opciones que desee, haga clic en **Save** y, a continuación, haga clic en **Yes** para confirmar. Para cancelar los cambios, haga clic en **Discard**.

## Directiva de caducidad y tiempo (min)

Los parámetros **Expiry Policy** y **Time (min)** determinan cuándo caducan los elementos almacenados en caché. Existen tres tipos de directivas de caducidad: **Absolute**, **Sliding** y **Never**.

Cuando se especifica **Absolute**, el intervalo de caducidad indicado por el parámetro **Time (min)** comienza cuando se agrega un elemento a la caché. Una vez que transcurre el intervalo indicado por el parámetro **Time (min)**, el elemento caduca.

Cuando se especifica **Sliding**, el intervalo de caducidad indicado por el parámetro **Time (min)** se restablece cada vez que se obtiene acceso a un elemento de la caché. El elemento no caduca hasta que transcurre el intervalo indicado por el parámetro **Time (min)** tras el último acceso al elemento.

Cuando se especifica **Never**, el parámetro **Time (min)** debe establecerse en **0** y los elementos no caducan.

**Absolute** es la directiva de caducidad predeterminada y 10 minutos es la configuración predeterminada del parámetro **Time (min)**. La directiva de caducidad es la misma para todos los elementos de una caché con nombre, pero el valor **Time (min)** puede personalizarse para cada elemento mediante sobrecargas de **Add** y **Put** que utilizan un parámetro de tiempo de espera.

Para obtener más información acerca de las directivas de expulsión y caducidad, consulte [Caducidad y expulsión][Caducidad y expulsión].

## Notificaciones

Las notificaciones de caché permiten a las aplicaciones recibir notificaciones asincrónicas cuando se producen diferentes operaciones de caché en el clúster de caché. Las notificaciones de caché también ofrecen invalidación automática de los objetos almacenados en caché localmente. Para obtener más información, consulte [Notificaciones de Azure Cache Service (vista previa)][Notificaciones de Azure Cache Service (vista previa)].

> Las notificaciones solo están disponibles en las ofertas de caché estándar y premium, no en la oferta básica. Para obtener más información, consulte [Ofertas de caché para Azure Cache Service (Vista previa)][Ofertas de caché].

## Alta disponibilidad

Cuando está habilitada la alta disponibilidad, se realiza una copia de seguridad de todos los elementos que se agregan a la caché. Si se produce un error imprevisto en la copia principal del elemento, la copia de seguridad sigue estando disponible.

Por definición, el uso de la alta disponibilidad multiplica por dos la cantidad de memoria necesaria para cada elemento almacenado en caché. Es importante tener en cuenta esto a la hora de planificar la capacidad. Para obtener más información, consulte [Alta disponibilidad para Azure Cache Service (versión preliminar)][Alta disponibilidad para Azure Cache Service (versión preliminar)].

> La alta disponibilidad solo está disponible en la oferta de caché premium, no en las ofertas básica y estándar. Para obtener más información, consulte [Ofertas de caché para Azure Cache Service (Vista previa)][Ofertas de caché].

## Expulsión

Para conservar la capacidad de memoria en las cachés, es posible expulsar los elementos menos usados recientemente. Cuando el consumo de memoria supera el umbral de memoria, los objetos se desalojan de la memoria, independientemente de si han expirado o no, hasta que se libera la presión de memoria.
La expulsión se habilita de forma predeterminada. Si se deshabilita la expulsión, cuando se alcance el límite de capacidad no se expulsarán elementos de la caché, sino que las operaciones Put y Add darán error.

Para obtener más información acerca de las directivas de expulsión y caducidad, consulte [Caducidad y expulsión][Caducidad y expulsión].

Una vez configurada la caché, es posible configurar los clientes de caché para permitir el acceso a la misma.

<a name="NuGet"></a>

## Configuración de los clientes de caché

Las memorias caché creadas a través del Servicio de caché administrado son accesibles desde aplicaciones de Azure que se ejecutan en Sitios web de Azure, roles web y de trabajo máquinas virtuales. Se proporciona un paquete de NuGet que facilita la configuración de las aplicaciones cliente de caché.

Para configurar una aplicación cliente utilizando el paquete de NuGet para el servicio de caché, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y elija **Manage NuGet Packages**.

![MenúNuGetPackage][MenúNuGetPackage]

Escriba **WindowsAzure.Caching** en el cuadro de texto **Buscar en línea** y seleccione **Windows Azure Cache** de los resultados. Haga clic en **Instalar** y luego en **Acepto**.

![PaqueteDeNuGet][PaqueteDeNuGet]

El paquete de NuGet cumple varias funciones: agrega la configuración necesaria al archivo de configuración de la aplicación e incorpora las referencias de ensamblado requeridas. En el caso de proyectos de Servicios en la nube, también agrega un valor de nivel de diagnóstico de cliente de caché al archivo ServiceConfiguration.cscfg del servicio en la nube.

> En los proyectos web ASP.NET, el paquete de NuGet para el servicio de caché también agrega dos secciones con comentarios a web.config: la primera permite el almacenamiento en caché del estado de la sesión y la segunda, el almacenamiento en caché de los resultados de las páginas ASP.NET. Para obtener más información, consulte [Almacenamiento del estado de sesión ASP.NET en la caché][Eliminación de del estado de la sesión de ASP.NET en la caché] y [Almacenamiento de la caché de resultados de la página ASP.NET en la caché][Eliminación de de la caché de resultados de la página ASP.NET en la caché].

El paquete de NuGet agrega los siguientes elementos de configuración a los archivos web.config o app.config de la aplicación. Se agregan una sección **dataCacheClients** y una sección **cacheDiagnostics** al elemento **configSections**. Si no existe el elemento **configSections**, se crea uno como elemento secundario del elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

Estas nuevas secciones incluyen referencias a un elemento **dataCacheClients**, que también se agrega al elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Una vez agregada la configuración, reemplace los siguientes dos elementos de la misma.

1.  Reemplace **[Nombre del rol de caché o extremo del servicio]** por el extremo, que aparece en el panel del Portal de administración.

    ![Extremo][Extremo]

2.  Elimine los comentarios de la sección securityProperties y reemplace **[Clave de autenticación]** por la clave de autenticación, que se puede encontrar en el Portal de administración haciendo clic en **Manage Keys** del panel de caché.

    ![ClavesDeAcceso][ClavesDeAcceso]

> Estos valores deben estar correctamente configurados para que los clientes puedan obtener acceso a la caché.

En el caso de proyectos de Servicios en la nube, el paquete de NuGet también agrega un valor **ClientDiagnosticLevel** a **ConfigurationSettings** del rol de cliente de caché del archivo ServiceConfiguration.cscfg. En el siguiente ejemplo se presenta la sección **WebRole1** de un archivo ServiceConfiguration.cscfg con un valor de **ClientDiagnosticLevel** 1, que es el valor de **ClientDiagnosticLevel** predeterminado.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> El nivel de diagnóstico de cliente configura el nivel de la información de diagnóstico de almacenamiento en caché recopilada para los clientes de caché. Para obtener más información, consulte [Diagnóstico y solución de problemas de aplicaciones de Servicio Caché (vista previa)][Diagnóstico y solución de problemas de aplicaciones de Servicio Caché (vista previa)]

El paquete de NuGet también agrega referencias a los siguientes ensamblados:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Si el proyecto es un proyecto web, también se agrega la siguiente referencia de ensamblado:

-   Microsoft.Web.DistributedCache.dll.

> Estos ensamblados están situados en la carpeta C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[sdk version]\\ref\\Caching\\.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>

## Trabajo con cachés

En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

-   [Eliminación de un objeto DataCache][Eliminación de un objeto DataCache]
-   [Eliminación de y recuperación de un objeto de la caché][Eliminación de y recuperación de un objeto de la caché]
-   [Eliminación de de la expiración de un objeto de la caché][Eliminación de de la expiración de un objeto de la caché]
-   [Eliminación de del estado de la sesión de ASP.NET en la caché][Eliminación de del estado de la sesión de ASP.NET en la caché]
-   [Eliminación de de la caché de resultados de la página ASP.NET en la caché][Eliminación de de la caché de resultados de la página ASP.NET en la caché]

<a name="create-cache-object"></a>

## Deshabilitación o habilitación un objeto DataCache

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente en la parte superior de cualquier archivo desde el que desee utilizar
Caché de Azure:

    using Microsoft.ApplicationServer.Caching;

> Si Visual Studio no reconoce los tipos de la instrucción de uso
> incluso tras la instalación del paquete de NuGet para el servicio de caché, que agrega las referencias necesarias, asegúrese de que el perfil de destino del
> proyecto sea .NET Framework 4 o superior y seleccione un perfil que no especifique **Client Profile**. Para obtener instrucciones acerca de la configuración de los clientes de caché, consulte [Configuración de los clientes de caché][Configuración de los clientes de caché].

Un objeto **DataCache** se puede crear de dos formas. La primera consiste sencillamente en crear un objeto **DataCache**, para lo que es necesario reemplazar el nombre de la caché deseada.

    DataCache cache = new DataCache("default");

Tras crear una instancia del objeto **DataCache**, puede usarlo para interactuar con la caché, tal y como se describe en las siguientes secciones.

La segunda forma se basa en crear un nuevo objeto **DataCacheFactory** en la aplicación con la utilización del constructor predeterminado. Esto da lugar a que el cliente de caché use los ajustes del archivo de configuración. Recurra al método **GetDefaultCache** de la nueva instancia **DataCacheFactory** que devuelve un objeto **DataCache**, o bien recurra al método **GetCache** y reemplace el nombre de la caché. Estos métodos devuelven un objeto **DataCache** que se puede usar para obtener acceso a la caché de manera programada.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

<a name="add-object"></a>

## Deshabilitación o habilitación y recuperación de un objeto de la caché

Para incorporar un elemento a la memoria caché, se puede usar el método **Add** o **Put**
. El método **Add** incorpora el objeto especificado a la caché, cuya clave se corresponde con el
valor del parámetro clave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Si un objeto con la misma clave ya se encuentra en la caché, se producirá una excepción
**DataCacheException** con el mensaje siguiente:

> ErrorCode:SubStatus: An attempt is being made to create an object with
> a Key that already exists in the cache. Caching will only accept
> unique Key values for objects.

Para recuperar un objeto con una clave específica, se puede usar el método **Get**. Si el objeto existe, este se
devuelve, y si no existe, se devuelve null.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

El método **Put** agrega el objeto con la clave especificada a la caché si no existe, o
bien reemplaza el objeto en caso de que exista.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## Deshabilitación o habilitación de la expiración de un objeto de la caché

De forma predeterminada, los elementos de la caché expiran 10 minutos después de colocarlos en la caché. Este valor se puede configurar en **Time (min)** en la pestaña Configure para la caché en el Portal de administración.

![CachésConNombre][CachésConNombre]

Se ofrecen tres opciones en **Expiration Policy**: **Never**, **Absolute** y **Sliding**. Estas opciones configuran cómo se usa **Time (min)** para determinar la expiración. El valor de **Expiration Type** predeterminado es **Absolute**, que significa que el temporizador de cuenta regresiva de la expiración de un elemento comienza cuando el elemento se coloca en la caché. Cuando ha transcurrido la cantidad de tiempo especificada para un elemento, este expira. Si se especifica **Sliding**, la cuenta regresiva para la expiración de un elemento se restablece cada vez que se obtiene acceso al elemento en la caché, y el elemento no expirará hasta que haya transcurrido la cantidad de tiempo especificada desde el último acceso. Si se especifica **Never**, la opción **Time (min)** debe establecerse en **0**, y los elementos no expirarán y serán válidos mientras estén en la caché.

Si se desea un intervalo de tiempo de expiración más largo o más corto que el que se ha configurado en las propiedades de la caché, se puede definir una duración específica cuando se incorpora o actualiza un elemento en la caché mediante la utilización de la
sobrecarga de **Add** y **Put** que consideran un parámetro **TimeSpan**. En el
siguiente ejemplo, la cadena **value** se incorpora a la caché, con la clave de
**item** y con un tiempo de expiración de 30 minutos.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Para ver el intervalo de tiempo de expiración restante de un elemento de la caché, se puede usar el método
**GetCacheItem** para recuperar un objeto **DataCacheItem**
 que contiene información acerca del elemento de la caché, incluido el
intervalo de tiempo de expiración restante.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## Deshabilitación o habilitación del estado de la sesión de ASP.NET en la caché

El proveedor del estado de sesión de la caché de Azure es un mecanismo de almacenamiento
fuera de proceso para las aplicaciones ASP.NET. Este proveedor le
permite almacenar el estado de sesión en una caché de Azure en lugar
de en la memoria o en una base de datos de SQL Server. Para usar el proveedor del estado de
sesión del almacenamiento en caché, debe configurar primero la caché y, a continuación, la aplicación ASP.NET para el almacenamiento en caché con la utilización del paquete NuGet para el almacenamiento en caché, tal y como se describe en [Introducción al Servicio de caché administrado][Introducción al Servicio de caché administrado]. Cuando el paquete de NuGet para caché está instalado, incorpora una sección comentada en web.config que contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor del estado de sesión para la caché de Azure.

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Si el archivo web.config no contiene esta sección comentada después de instalar el paquete de NuGet para la caché, asegúrese de que el último administrador del paquete de NuGet se ha instalado desde la página de [instalación de NuGet Package Manager][instalación de NuGet Package Manager] (en inglés) y, a continuación, desinstale el paquete y vuelva a instalarlo.

Para habilitar el proveedor del estado de sesión para la caché de Azure, quite el comentario de la sección especificada. La caché predeterminada se especifica en el fragmento proporcionado. Para usar una caché diferente, especifique la que desee en el atributo **cacheName**.

Para obtener más información acerca del uso del proveedor del estado de sesión del Servicio de
caché administrado, consulte [Proveedor del estado de sesión para la caché de Azure][Proveedor del estado de sesión para la caché de Azure].

<a name="store-page"></a>

## Deshabilitación o habilitación de la caché de resultados de la página ASP.NET en la caché

El proveedor de la caché de resultados para la caché de Azure es un mecanismo de almacenamiento fuera de proceso para los datos de la caché de resultados. Estos datos corresponden específicamente a respuestas
HTTP completas (almacenamiento en caché de los resultados de la página). El proveedor se conecta al nuevo punto de extensibilidad del
proveedor de la caché de resultados introducido en ASP.NET 4. Para
usar el proveedor de la caché de resultados, primero configure el clúster de caché y, a continuación, configure la aplicación ASP.NET para el almacenamiento en caché con el uso del paquete de NuGet para el almacenamiento en caché, según se describe en [Introducción al Servicio de caché administrado][Introducción al Servicio de caché administrado]. Cuando el paquete de NuGet para el almacenamiento en caché está instalado, incorpora la siguiente sección comentada en web.config que contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor de la caché de resultados para el almacenamiento en caché de Azure.

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Si el archivo web.config no contiene esta sección comentada después de instalar el paquete de NuGet para la caché, asegúrese de que el último administrador del paquete de NuGet se ha instalado desde la página de [instalación de NuGet Package Manager][instalación de NuGet Package Manager] (en inglés) y, a continuación, desinstale el paquete y vuelva a instalarlo.

Para habilitar el proveedor de la caché de resultados para la caché de Azure, quite el comentario de la sección especificada. La caché predeterminada se especifica en el fragmento proporcionado. Para usar una caché diferente, especifique la que desee en el atributo **cacheName**.

Incorpore una directiva **OutputCache** a cada página cuyos resultados desea almacenar en caché.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

En este ejemplo, los datos de la página almacenados en la caché permanecerán ahí durante 60 segundos y se almacenará en la caché una versión diferente de la página para cada combinación de parámetros. Para obtener más información acerca de las opciones disponibles, consulte [directiva OutputCache][directiva OutputCache].

Para obtener más información acerca del uso del proveedor de la caché de resultados para la caché de Azure, consulte [Proveedor de la caché de resultados para la caché de Azure][Proveedor de la caché de resultados para la caché de Azure].

<a name="next-steps"></a>

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del Servicio de caché administrado,
utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento en caché más complejas.

-   Consulte la referencia de MSDN: [Servicio de caché administrado][Información general del Servicio de caché administrado de Azure]
-   Obtenga información acerca de cómo migrar al Servicio de caché administrado: [Migrar al Servicio de caché administrado][Migrar al Servicio de caché administrado]
-   Consulte los ejemplos: [Ejemplos del Servicio de caché administrado][Ejemplos del Servicio de caché administrado]

<!-- INTRA-TOPIC LINKS -->
<!-- IMAGES -->
<!-- LINKS -->

  [Pasos siguientes]: #next-steps
  [Introducción al Servicio de caché administrado]: #getting-started-cache-service
  [Creación de la caché]: #create-cache
  [Configuración de la caché]: #enable-caching
  [Configuración de los clientes de caché]: #NuGet
  [Uso de cachés]: #working-with-caches
  [Eliminación de un objeto DataCache]: #create-cache-object
  [Eliminación de y recuperación de un objeto de la caché]: #add-object
  [Eliminación de de la expiración de un objeto de la caché]: #specify-expiration
  [Eliminación de del estado de la sesión de ASP.NET en la caché]: #store-session
  [Eliminación de de la caché de resultados de la página ASP.NET en la caché]: #store-page
  [Detalles de precios de caché]: http://www.windowsazure.com/es-es/pricing/details/cache/
  [Información general del Servicio de caché administrado de Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Cómo instalar y configurar Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/es-es/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/es-es/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [Ofertas de caché]: http://go.microsoft.com/fwlink/?LinkId=317277
  [CachésConNombre]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  [Caducidad y expulsión]: http://go.microsoft.com/fwlink/?LinkId=317278
  [MenúNuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png
  [PaqueteDeNuGet]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png
  [Extremo]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png
  [ClavesDeAcceso]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png
  [instalación de NuGet Package Manager]: http://go.microsoft.com/fwlink/?LinkId=240311
  [Proveedor del estado de sesión para la caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
  [directiva OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
  [Proveedor de la caché de resultados para la caché de Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
  [Migrar al Servicio de caché administrado]: http://go.microsoft.com/fwlink/?LinkId=317347
  [Ejemplos del Servicio de caché administrado]: http://go.microsoft.com/fwlink/?LinkId=320840
