<properties 
	pageTitle="Transformación y extensión del sitio" 
	description="Por determinar:" 
	authors="cephalin" 
	writer="cephalin" 
	editor="mollybos" 
	manager="wpickett" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Transformación y extensión del sitio

Puede transformar el archivo [ApplicationHost.config](http://msdn.microsoft.com/library/dd465326.aspx) en los Sitios web Azure mediante las declaraciones de [XML Document Transformation](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) (XDT). También puede usar las declaraciones XDT para agregar extensiones de sitios privados para habilitar acciones de administración del sitio personalizado. Este artículo incluye un sitio del administrador PHP de ejemplo que habilita la administración de la configuración de PHP a través de una interfaz web.


<!-- MINI TOC -->

* [Transformación de la configuración del sitio en ApplicationHost.config](#transform)
* [Extensión del sitio](#extend)
	* [Información general de las extensiones de sitios privados](#overview)
	* [Ejemplo de extensión del sitio: administrador PHP](#SiteSample)
		* [Aplicación web del administrador de PHP](#PHPwebapp)
		* [El archivo applicationHost.xdt](#XDT)
	* [Implementación de la extensión del sitio](#deploy)

<h2><a id="transform"></a>Transformación de la configuración del sitio en ApplicationHost.config</h2>
La plataforma de Sitios web Azure proporciona la flexibilidad y control para la configuración del sitio. Aunque el archivo de configuración ApplicationHost.config de IIS estándar no está disponible para la edición directa en Sitios Web Azure, la plataforma es compatible con un modelo de transformación ApplicationHost.config declarativo basado en XML Document Transformation (XDT).

Para sacar provecho de esta funcionalidad de transformación, cree un archivo ApplicationHost.xdt con contenido XDT y colóquelo en la raíz del sitio. A continuación, en la página **Configure** del Portal de Azure, establezca la configuración de la aplicación  `WEBSITE_PRIVATE_EXTENSIONS` en 1 (es posible que tenga que reiniciar el sitio). 

El siguiente ejemplo de applicationHost.xdt muestra cómo agregar una nueva variable de entorno personalizada en un sitio que use PHP 5.4.

	<?xml version="1.0"?> 
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform"> 
  		<system.webServer> 
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />	
         				</environmentVariables>
      				</application>
    			</fastCgi> 
  		</system.webServer> 
	</configuration> 

 
Hay un archivo de registro con información y estado de trasformación disponible en la raíz del FTP en LogFiles\Transform.

Para obtener ejemplos adicionales, consulte [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Nota**<br />
los elementos de la lista de módulos en  `system.webServer` no pueden quitarse o reordenarse, aunque sí es posible agregar elementos a la lista. 


<h2><a id="extend"></a>Extensión del sitio</h2>
<h3><a id="overview"></a>Información general de las extensiones de sitios privados</h3>
Sitios web Azure es compatible con extensiones del sitio como punto de extensibilidad para las acciones administrativas del sitio. De hecho, algunas características de la plataforma de Sitios web Azure se implementan como extensiones preinstaladas del sitio. Cuando las extensiones preinstaladas de la plataforma no se puedan modificar, puede crear y configurar extensiones privadas para sus propios sitios. Esta funcionalidad también se basa en declaraciones XDT. Los pasos clave para la creación de una extensión del sitio privado son los siguientes:

1. **Contenido** de la extensión del sitio: cree una aplicación web compatible con los Sitios web Azure
2. **Declaración** de la extensión del sitio: cree un archivo ApplicationHost.xdt
3. **Implementación** de la extensión del sitio: coloque el contenido en la carpeta SiteExtensions en `root`
4.  **Habilitación** de la extensión del sitio: establezca la configuración de la aplicación `WEBSITE_PRIVATE_EXTENSIONS` en 1

Los vínculos internos para la aplicación web deben apuntar a una ruta relacionada con la ruta de la aplicación especificada en el archivo ApplicationHost.xdt. Cualquier cambio en el archivo ApplicationHost.xdt requiere reciclar el sitio. 

**Nota**: la información adicional para estos elementos clave se encuentra disponible en [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) Se incluye un ejemplo detallado para mostrar los pasos para la creación y habilitación de una extensión de sitio privado. El código fuente del ejemplo de administrador de PHP que se muestra a continuación puede descargarse en [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

<h3><a id="SiteSample"></a>Ejemplo de extensión del sitio: Administrador PHP</h3>

El administrador PHP es una extensión del sitio que permite a los administradores del sitio ver y configurar fácilmente su configuración PHP con una interfaz web en lugar de tener que modificar archivos .ini PHP directamente. Los archivos de configuración comunes para PHP incluyen el archivo php.ini ubicado en Archivos de programa y el archivo .user.ini ubicado en la carpeta raíz del sitio. Puesto que el archivo php.ini no es directamente editable en la plataforma de Sitios web Azure, la extensión del administrador PHP usa el archivo .user.ini para aplicar los cambios de configuración.

<h4><a id="PHPwebapp"></a>Aplicación web del administrador PHP</h4>
	
A continuación se muestra la página principal del sitio web del administrador PHP:

![TransformSitePHPUI][TransformSitePHPUI]

Como puede ver, una extensión del sitio es como una aplicación web normal, pero con un archivo ApplicationHost.xdt adicional situado en la carpeta raíz del sitio (puede encontrar más información sobre el archivo ApplicationHost.xdt disponible en la siguiente sección de este artículo).

La extensión del administrador PHP se creó mediante la plantilla de la aplicación ASP.NET MVC 4 Web Application de Visual Studio. La siguiente vista del Explorador de soluciones muestra la estructura de la extensión del sitio del administrador PHP.

![TransformSiteSolEx][TransformSiteSolEx]

La única lógica especial necesaria para la E/S del archivo es indicar dónde se encuentra el directorio wwwroot del sitio. Puesto que se muestra el siguiente ejemplo de código, la variable de entorno "HOME" indica la ruta raíz del sitio y la ruta wwwroot puede construirse anexando "site\wwwroot":


	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	} 


Una vez que disponga de la ruta de acceso al directorio, puede usar las operaciones de E/S del archivo normal para leer y escribir en los archivos.

Un punto de advertencia en relación con las extensiones del sitio hace referencia a la administración de los vínculos internos.  Si dispone de vínculos en los archivos HTML que proporcionen rutas absolutas a vínculos internos en el sitio, debe asegurarse de que esos vínculos se anexen al nombre de extensión como ruta del sitio. Esto es necesario porque la ruta del sitio para la extensión es ahora "/`[nombre-de-su-extensión]`/" en lugar de solo "/", por lo que los vínculos internos deben actualizarse como corresponda. Por ejemplo, suponga que el código incluye un vínculo a lo siguiente: 

`"<a href="/Home/Settings">Configuración de PHP</a>"`

Cuando el vínculo forma parte de una extensión del sitio, el vínculo debe tener la siguiente forma:

`"<a href="/[your-site-name]/Home/Settings">Configuración</a>"` 

Puede satisfacer este requisito usando solo rutas relativas en el sitio web o, en caso de los sitios web ASP.NET, usando el método `@Html.ActionLink` que crea los vínculos apropiados para usted.

<h4><a id="XDT"></a>Archivo applicationHost.xdt</h4>

El código de la extensión del sitio está en %HOME%\SiteExtensions\[nombre-de-su-extensión]. Llamaremos a esto raíz de extensión.  

Para registrar la extensión del sitio con el archivo applicationHost.config, tendrá que situar un archivo denominado ApplicationHost.xdt en la raíz de extensión. El contenido del archivo ApplicationHost.xdt debe ser el siguiente:

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

El nombre que seleccione como nombre de extensión debe tener el mismo nombre que la carpeta raíz de extensión.

Esto provoca la suma de una nueva ruta de aplicación a la lista de sitios  `system.applicationHost` en el sitio SCM. El sitio SCM es un punto final de administración que especifica credenciales de acceso. Dispone de la dirección URL  `https://[your-site-name].scm.azurewebsites.net`.  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>Implementación de la extensión del sitio</h3>

Para instalar la extensión del sitio, puede usar el FTP para copiar todos los archivos de la aplicación web en la carpeta `\SiteExtensions\[nombre-de-su-extensión]` del sitio en el que desee instalar la extensión.  Asegúrese de copiar el archivo ApplicationHost.xdt en la ubicación también.

A continuación, en el Portal de Sitios web Azure, diríjase a la pestaña **Configure** para el sitio web que tenga su extensión. En la sección **configuración de aplicaciones**, agregue la clave `WEBSITE_PRIVATE_EXTENSIONS` y proporciónele un valor de `1`.

![TransformSiteappSettings][TransformSiteappSettings]

Finalmente, en el Portal de Azure, reinicie el sitio web para habilitar su extensión.

![TransformSiteRestart][TransformSiteRestart]

Debe poder ver la extensión del sitio en:


`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]` 

Tenga en cuenta que el aspecto de la dirección URL es parecido al de la dirección URL de su sitio, con la diferencia de que usa HTTPS y contiene ".scm". 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png







<!--HONumber=42-->
