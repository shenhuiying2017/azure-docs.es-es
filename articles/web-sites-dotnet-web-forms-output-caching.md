<properties urlDisplayName="details" pageTitle="Detalles del centro de vídeo" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="sdanie" />



# Uso de almacenamiento en caché de resultados de formularios web ASP.NET con Sitios web Azure

	 almacenamiento en caché de resultados de formularios Web Forms ASP.NET. El almacenamiento en caché de resultados de una página es una optimización que devuelve directamente una página de representación en caché durante un tiempo determinado. Resulta útil en situaciones en las que se obtiene acceso a la página de forma más frecuente de lo suele cambiar. Es importante tener en cuenta que el almacenamiento en caché de resultados de una página no es compatible con aplicaciones ASP.NET MVC.

El Servicio de caché (vista previa) proporciona un servicio de almacenamiento en caché distribuido que es externo al sitio web. Esto permite a todas las instancias del sitio web obtener acceso a la misma representación en caché de una página.

Los pasos básicos para utilizar el servicio de caché (vista previa) para el almacenamiento en caché de resultados de una página incluyen:

* [Creación de la caché](#createcache)
* [Configuración del proyecto ASP.NET para utilizar el servicio de caché de Azure.](#configureproject)
* [Modificación del archivo web.config](#configurewebconfig)
* [Uso del almacenamiento en caché de resultados para devolver temporalmente versiones en caché de una página](#useoutputcaching)

<h2><a id="createcache"></a>Creación de la memoria caché</h2>
Las instancias de caché del Servicio de caché administrado se crean usando cmdlets de PowerShell. 

>Una vez que se haya creado una instancia del Servicio de caché administrado con los cmdlets de PowerShell, se puede ver y configurar en el [Portal de administración de Azure][].

Para crear una instancia del Servicio de caché administrado, abra una ventana de comandos de Azure PowerShell.

>Para obtener instrucciones de instalación y uso de Azure PowerShell, vea [Instalación y configuración de Azure PowerShell][].

Invoque el cmdlet [Add-AzureAccount][] y escriba la dirección de correo electrónico y la contraseña asociadas a la cuenta. Hay una suscripción seleccionada de manera predeterminada que se muestra tras invocar el cmdlet [Add-AzureAccount][]. Para cambiar la suscripción, invoque el cmdlet [Select-AzureSubscription][].

>Si ha configurado Azure PowerShell con un certificado para la cuenta, puede pasar por alto este paso. Para obtener más información sobre la conexión de Azure PowerShell con su cuenta de Azure, consulte [Instalación y configuración de Azure PowerShell][].

Hay una suscripción seleccionada de manera predeterminada que se muestra. Para cambiar la suscripción, invoque el cmdlet [Select-AzureSubscription][].

Invoque el cmdlet [New-AzureManagedCache][] y especifique el nombre, la región, la oferta de caché y el tamaño de la memoria caché.

En **Nombre**, escriba un nombre de subdominio para usar el extremo de caché. El nombre debe tener entre seis y veinte caracteres, solo puede contener números y letras minúsculas, y debe comenzar por una letra.

En **Ubicación**, seleccione una región para la memoria caché. Para optimizar el rendimiento, cree la caché en la misma región que la aplicación cliente de caché.

**Sku** y **Memoria** funcionan en conjunto para determinar el tamaño de la memoria caché. El Servicio de caché administrado está disponible en los tres niveles siguientes:

-	Básico: Tamaños de caché de 128 MB a 1 GB en incrementos de 128 MB, con una caché con nombre predeterminada.
-	Estándar: Tamaños de caché de 1 GB a 10 GB en incrementos de 1 GB, compatible con las notificaciones, con hasta diez cachés con nombre.
-	Premium: Tamaños de caché de 5 GB a 150 GB en incrementos de 5 GB, compatible con las notificaciones, con alta disponibilidad y hasta diez cachés con nombre.

Elija los valores de **Sku** y **Memoria** que se ajusten a las necesidades de su aplicación. Tenga en cuenta que algunas características de las cachés, como las notificaciones y la alta disponibilidad, solo están disponibles con determinadas ofertas de caché. Para obtener más información acerca de cómo elegir la oferta y el tamaño de caché que mejor se ajusten a su aplicación, consulte [Ofertas de caché][].

 En el siguiente ejemplo, se crea una caché de la oferta Basic de 128 MB con el nombre contosocache, en la región geográfica Centro-Sur de EE. UU.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>Para obtener una lista completa de parámetros y valores que se pueden usar cuando se crea una caché, vea la documentación del cmdlet [New-AzureManagedCache][].

Tras invocar al cmdlet de PowerShell, la creación de la memoria caché puede tardar unos minutos. Una vez creada la memoria caché, su estado es `En ejecución` y está preparada para usarse con la configuración predeterminada. Además, se puede ver y configurar en el [Portal de administración de Azure][].

Puede supervisar el progreso de creación en la ventana de Azure PowerShell. Cuando la memoria caché está preparada para su uso, el cmdlet [New-AzureManagedCache][] expone la información de caché, como se muestra en el siguiente ejemplo.

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





Las siguientes secciones utilizarán la configuración de la pestaña **Panel** para configurar el almacenamiento en caché para un proyecto ASP.NET.

<h2><a id="configureproject"></a>Configuración del proyecto ASP.NET</h2>
1. Primero, asegúrese de que ha [instalado el][] **SDK de Azure para .NET** más reciente.

2. En Visual Studio, haga clic con el botón secundario en el proyecto ASP.NET en el **Explorador de soluciones** y, a continuación, seleccione **Administrar paquetes NuGet**. Si utiliza WebMatrix, en lugar de realizar la acción anterior, haga clic en el botón **NuGet** en la barra de herramientas.

3. Escriba **WindowsAzure.Caching** en el cuadro de edición **Buscar en línea**.

	![NuGetDialog][NuGetDialog]

4. Seleccione el paquete **Almacenamiento en caché de Azure** y, a continuación, haga clic en el botón **Instalar**.

<h2><a id="configurewebconfig"></a>Modificación del archivo web.config</h2>
Además de hacer referencias de ensamblado para la caché, el paquete NuGet agrega entradas auxiliares en el archivo web.config. Para utilizar la caché para el almacenamiento en caché de resultados de una página ASP.NET, se deben en realizar muchas modificaciones en el archivo web.config.

1. Abra el archivo **web.config** para el proyecto ASP.NET.

2. Si dispone de elementos **caching** y **outputCache** existentes, conviértalos en comentario (o quítelos).

3. Luego, quite la marca de comentario del elemento **caching** que agregó el paquete NuGet para el almacenamiento en caché de Azure. El resultado final debería ser similar a la siguiente captura de pantalla.

	![OutputConfig][OutputConfig]

4. A continuación, busque la sección **dataCacheClients**. Quite la marca de comentario del elemento secundario **securityProperties**.

	![CacheConfig][CacheConfig]

5. En el elemento **autoDiscover**, defina el atributo **identifier** en la dirección URL del extremo de la caché. Para buscar la URL del extremo, vaya a las propiedades de la caché en el Portal de administración de Azure. En la pestaña **Panel**, copie el valor **ENDPOINT URL** en la sección **quick glance**.

	![EndpointURL][EndpointURL]

6. En el elemento **messageSecurity**, defina el atributo **authorizationInfo** en la clave de acceso de la caché. Para encontrar la clave de acceso, seleccione la caché en el Portal de administración de Azure. A continuación, haga clic en el icono **Manage Keys** en la barra inferior. Haga clic en el botón de copia junto al cuadro de texto **CLAVE DE ACCESO PRIMARIA**.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Uso de almacenamiento en caché de resultados</h2>
El paso final es configurar páginas en la aplicación de formularios Web Forms ASP.NET para usar el almacenamiento en caché de resultados. Esto se puede hacer agregando el atributo **OutputCache** al principio del origen .ASPX. Por ejemplo:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

En el ejemplo anterior se almacena en caché la página durante 45 segundos. Puesto que **VaryByParam** se establece en "*", esta salida de página en caché no cambia incluso si se pasan diferentes parámetros de consulta. Sin embargo, en el siguiente ejemplo se almacena en caché una versión distinta de la página para cada valor del parámetro UserId:

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[installed the latest]: http://www.windowsazure.com/es-es/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Cmdlets de memoria caché administrada de Azure]: http://go.microsoft.com/fwlink/?LinkID=398555
[Instalación y configuración de Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/es-es/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/es-es/library/dn495203.aspx
[Portal de administración de Azure]: https://manage.windowsazure.com/
  
  
  
