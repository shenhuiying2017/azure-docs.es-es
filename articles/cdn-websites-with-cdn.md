<properties urlDisplayName="Integrate an Azure Website with Azure CDN" pageTitle="Integración de un sitio web de Azure con la red CDN de Azure" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, websites" description="Un tutorial que le enseña cómo implementar un sitio web que ofrece contenido de un extremo de CDN de Azure integrado" metaCanonical="" services="cdn,web-sites" documentationCenter=".NET" title="Integrate an Azure Website with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# Integración de un sitio web de Azure con la red CDN de Azure #

Sitios web Azure puede integrarse con [CDN de Azure](http://azure.microsoft.com/es-es/services/cdn/), agregándolo a las capacidades de escalado globales inherentes en Sitios web Azure sirviendo el contenido del sitio web globalmente desde los nodos de servidor cercan de sus clientes ([aquí](http://msdn.microsoft.com/es-es/library/azure/gg680302.aspx) se puede encontrar una lista actualizada de todas las ubicaciones de nodo actuales). Esta integración aumenta considerablemente el rendimiento de su sitio web de Azure y mejora considerablemente la experiencia del usuario de dicho sitio en todo el mundo. 

La integración de Sitios web Azure con la red CDN de Azure le ofrece las siguientes ventajas:

- Integración de la implementación de contenido (imágenes, scripts y hojas de estilo) como parte del proceso de [implementación continua](http://azure.microsoft.com/es-es/documentation/articles/web-sites-publish-source-control/) de su sitio web Azure
- Actualización sencilla de los paquetes de NuGet en el sitio web de Azure, como versiones de jQuery o Bootstrap 
- Administración de la aplicación web y del contenido servido por CDN desde la misma interfaz de Visual Studio
- Integración de unión y minificación de ASP.NET con CDN de Azure

## Qué aprenderá ##

En este tutorial, aprenderá a:

-	[Integración de un extremo de la red CDN de Azure con el sitio web de Azure y suministro de contenido estático en las páginas web desde la red CDN de Azure](#deploy)
-	[Configuración de los valores de la caché para el contenido estático en el sitio web de Azure](#caching)
-	[Suministro de contenido de acciones de controlador a través de CDN de Azure](#controller)
-	[Suministro de contenido unido y minificado a través de CDN de Azure preservando al mismo tiempo la experiencia de depuración de scripts en Visual Studio](#bundling)
-	[Configuración de la reserva de los scripts y CSS cuando la red CDN de Azure está sin conexión](#fallback) 

## Lo que va a crear ##

Implementaremos un sitio web de Azure mediante la plantilla predeterminada ASP.NET MVC en Visual Studio, agregaremos código para servir contenido desde una red CDN de Azure integrada, como una imagen, los resultados de las acciones de controlador y los archivos predeterminados de JavaScript y CSS, y también escribiremos código para configurar el mecanismo de reserva de los paquetes servidos en caso de desconexión de CDN.

## Qué necesita ##

Este tutorial cuenta con los siguientes requisitos previos:

-	Una cuenta de [Microsoft Azure activa](http://azure.microsoft.com/es-es/account/)
-	Visual Studio 2013 con el [SDK de Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para completar este tutorial, deberá tener una cuenta de Azure:</h5>
  <ul>
    <li>Puede <a href="http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A261C142F">abrir una cuenta de Azure de manera gratuita</a> - Obtiene crédito que puede utilizar para probar los servicios de Azure de pago, e incluso una vez agotado este podrá mantener la cuenta y utilizar servicios gratuitos de Azure, como Sitios web.</li>
    <li>Puede <a href="http://azure.microsoft.com/es-es/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">activar las ventajas de suscriptor de MSDN</a> - Su suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.</li>
  <ul>
</div>

<a name="deploy"></a>
## Implementación de un sitio web de Azure con un extremo de red CDN integrado ##

En esta sección, implementará la plantilla de aplicación predeterminada ASP.NET MVC de Visual Studio 2013 en un sitio web de Azure y luego la integrará con un nuevo extremo de CDN. Siga las instrucciones que se describen a continuación:

1. En Visual Studio 2013, cree una nueva aplicación web ASP.NET desde la barra de menús seleccionando **Archivo > Nuevo > Proyecto > Web > Aplicación web ASP.NET**. Asigne un nombre y haga clic en **Aceptar**.

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. Seleccione **MVC** y haga clic en **Administrar suscripciones**.

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. Haga clic en **Iniciar sesión**.

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. En la página de inicio de sesión, inicie sesión con la cuenta de Microsoft que ha usado para activar su cuenta de Azure.
7. A continuación, haga clic en **Cerrar**. A continuación, haga clic en **Aceptar** para continuar.

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Suponiendo que no ha creado un sitio web de Azure, Visual Studio pueden ayudarle a crearlo. En el cuadro de diálogo **Configurar sitio de web de Microsoft Azure**, asegúrese de que el nombre del sitio es único. A continuación, haga clic en **Aceptar**.

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. Una vez creada la aplicación ASP.NET, publíquela en Azure en el panel de actividad de publicación web haciendo clic en **Publicar `<nombre de aplicación>` este sitio ahora**. Haga clic en **Publicar** para completar el proceso.

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	Verá su sitio web de Azure publicado en el explorador cuando la publicación se complete. 

1. Para crear un extremo de la red CDN, inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/). 
2. Haga clic en **Nuevo** > **Servicios de aplicaciones** > **CDN** > **Creación rápida**. Seleccione **http://*<nombreDelSitio>*.azurewebsites.net/** y haga clic en **Crear**.

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	>[WACOM.NOTE] Cuando haya creado el extremo de CDN, el portal de Azure mostrará su URL y el dominio de origen con el que está integrado. Sin embargo, puede que pase un tiempo hasta que la configuración del extremo de CDN se propague a las ubicaciones de todos los nodos de CDN. 

3. De vuelta en el portal de Azure, en la pestaña **CDN**, haga clic en el nombre del extremo de red CDN que acaba de crear.

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. Haga clic en **Habilitar cadena de consulta** para habilitar las cadenas de consulta en la caché de CDN. Una vez realizada la habilitación, el mismo vínculo al que se accede con diferentes cadenas de consulta se almacenará en caché como entradas independientes.

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[WACOM.NOTE] Aunque habilitar la cadena de consulta no es necesario en esta sección del tutorial, querrá hacer esto lo antes posible por comodidad dado que cualquier cambio aquí realizado va a tardar en propagarse a todos los nodos de CDN, y no deseará que el contenido no habilitado para la cadena de consulta colapse la caché de CDN (la actualización del contenido de CDN se tratará más adelante).

2. Ahora, haga clic en la dirección del extremo de la red CDN. Si el extremo está listo, debería ver el sitio web mostrado. Si obtiene un error **HTTP 404**, el extremo de la red CDN no está listo. Puede que necesite esperar hasta una hora para que la configuración de la red CDN se propague a todos los nodos perimetrales. 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. A continuación, intente obtener acceso al archivo **~/Content/bootstrap.css** en el proyecto ASP.NET. En la ventana del explorador, navegue a **http://*<nombreDeRedCDN>*.vo.msecnd.net/Content/bootstrap.css**. En nuestra instalación, esta URL es:

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	Esta URL corresponde a la siguiente URL de origen en el extremo de CDN:

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	Cuando vaya a **http://*<<nombre de cdn>*.vo.msecnd.net/Content/bootstrap.css**, se le pedirá que descargue el archivo bootstrap.css que procedía de su sitio web de Azure. 

	![](media/cdn-websites-with-cdn/12-file-access.png)

De igual forma, puede acceder a cualquier URL de acceso público en **http://*<nombre de servicio>*.cloudapp.net/**, directamente desde el extremo de CDN. Por ejemplo:

-	Un archivo .js desde la ruta /Script
-	Cualquier archivo de contenido desde la ruta /Content
-	Cualquier controlador/acción 
-	Si la cadena de consulta está habilitada en el extremo de CDN, cualquier URL con cadenas de consulta
-	El sitio de web de Azure completo, si todo el contenido es público

Tenga en cuenta que puede que no siempre sea una buena idea (o por lo general una buena idea) servir un sitio Web de Azure completo a través de la red CDN de Azure. Estos son algunos de los puntos a tener en cuenta:

-	Este enfoque requiere que el sitio entero sea público, dado que CDN de Azure no puede servir contenido privado.
-	Si por algún motivo el extremo de CDN se desconecta, ya sea por mantenimiento programado o debido a un error del usuario, el sitio web entero se desconecta a menos que los clientes se puedan redirigir a la URL de origen **http://*<nombreDelSitio>*.azurewebsites.net/**. 
-	Incluso con la configuración personalizada del control de la memoria caché (consulte [Configuración de las opciones de caché para los archivos estáticos del sitio web de Azure](#caching)), un extremo de CDN no mejora el rendimiento del contenido extremadamente dinámico. Si ha intentado cargar la página principal desde el extremo de CDN como se ha mostrado anteriormente, tenga en cuenta que al menos tardará cinco segundos en cargarse la página principal predeterminada la primera vez, pese a ser una página bastante simple. Imagine entonces cómo sería la experiencia del cliente si esta página incluyera contenido dinámico que se debe actualizar cada minuto. Servir contenido dinámico desde un extremo de CDN requiere una corta caducidad de la caché, lo que se traduce en errores frecuentes de la caché en el extremo de CDN. Esto afecta al rendimiento del sitio web de Azure y frustra el propósito de una red CDN.

La alternativa es determinar qué contenido servir desde CDN de Azure según el caso en el sitio web de Azure. Para tal fin, ya ha visto cómo acceder a archivos de contenido individuales desde el extremo de CDN. Le mostraremos cómo servir una acción de controlador específica a través del extremo de CDN en [Suministro de contenido de acciones de controlador a través de CDN de Azure](#controller).

<a name="caching"></a>
## Configuración de las opciones de almacenamiento en caché para los archivos estáticos del sitio web de Azure ##

Con la integración de la red CDN de Azure en el sitio web de Azure, puede especificar cómo quiere que el contenido estático se almacene en la caché en el extremo de CDN. Para ello, abra el archivo *Web.config* desde su proyecto ASP.NET (por ejemplo, **cdnwebapp**) y agregue un elemento `<staticContent>` a `<system.webServer>`. El XML siguiente configura la caché para que caduque en tres días.  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

Una vez realizado esto, todos los archivos estáticos del sitio web de Azure observarán la misma regla en la caché de CDN. Para un control más granular de la configuración de la caché, agregue un archivo *Web.config* a una carpeta y agregue ahí su configuración. Por ejemplo, agregue un archivo *Web.config* a la carpeta *\Content* y sustituya el contenido por el siguiente XML:

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

Esta configuración hace que todos los archivos estáticos de la carpeta *\Content* se almacenen en la caché durante 15 días.

Para obtener más información sobre cómo configurar el elemento `<clientCache>`, vea [Caché de cliente <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

En [Suministro de contenido de acciones de controlador a través de CDN de Azure](#controller), le mostraremos cómo puede configurar los valores de la caché para los resultados de las acciones de controlador en la caché de CDN.

<a name="controller"></a>
## Suministro de contenido de acciones de controlador a través de CDN de Azure ##

Al integrar un sitio web de Azure con la red CDN de Azure, es relativamente fácil servir el contenido desde las acciones de controlador a través de la red CDN de Azure. Nuevamente, si decide suministrar todo el sitio web de Azure a través de la red CDN, no será necesario hacer esto en absoluto puesto que todas las acciones de controlador ya están accesibles a través de la red CDN. Pero por las razones que ya indiqué en [Implementación de un sitio web de Azure con un extremo de red CDN integrado](#deploy), puede no decidir adoptar esta opción y optar por seleccionar la acción de controlador que desea servir desde la red CDN de Azure. [Maarten Balliauw](https://twitter.com/maartenballiauw) le muestra cómo hacerlo con un divertid controlador MemeGenerator en [Reducción de la latencia en Web con la red CDN de Microsoft Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Aquí simplemente lo vamos a reproducir.

Suponga que desea generar en su sitio web memes basados en una imagen de Chuck Norris de joven (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Tiene una acción `Index` sencilla que permite a los clientes especificar los superlativos de la imagen, que luego genera el meme una vez que publican la acción. Dado que se trata de Chuck Norris, esperará que esta página se haga ampliamente popular en todo el mundo. Este es un buen ejemplo de servir contenido dinámico con CDN de Azure. 

Siga los pasos anteriores para configurar esta acción de controlador:

1. En la carpeta *\Controllers*, cree un nuevo archivo .cs llamado *MemeGeneratorController.cs* y sustituya el contenido por el siguiente código. Asegúrese de sustituir la parte resaltada por su ruta de acceso de archivo y nombre de red CDN.
	<pre class="prettyprint">
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Net;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace cdnwebapp.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap))
	            {
	                SizeF size = new SizeF();
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. Haga clic con el botón secundario en la acción `Index()` predeterminada y seleccione **Agregar vista**.

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acepte la configuración que se indica a continuación y haga clic en **Agregar**.

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Abra el nuevo archivo *Views\MemeGenerator\Index.cshtml* y sustituya el contenido por el siguiente HTML simple para enviar los superlativos:

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. Publique de nuevo el sitio web de Azure y vaya a **http://*<nombre de servicio>*.cloudapp.net/MemeGenerator/Index** en el explorador. 

Al enviar los valores del formulario para `/MemeGenerator/Index`, el método de acción `Index_Post` devuelve un vínculo al método de acción `Show` con el identificador de entrada correspondiente. Cuando hace clic en el vínculo, llega al siguiente código:  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

Si su depurador local está conectado, obtendrá la experiencia de depuración normal con una redirección local. Si se está ejecutando en el sitio web de Azure, realizará la redirección a:

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde a la siguiente URL de origen en el extremo de CDN:

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Después de aplicar la regla de reescritura de URL anterior, el archivo real que se almacena en caché en el extremo de CDN es:

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Luego puede usar el atributo `OutputCacheAttribute` en el método `Generate` para especificar cómo se debería almacenar en caché el resultado de la acción, que atenderá CDN de Azure. El siguiente código especifica una caducidad de la caché de una hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Igualmente, puede servir contenido de cualquier acción de controlador del sitio web de Azure a través de su red CDN de Azure, con la opción de almacenamiento en caché deseada.

En la siguiente sección, le mostraremos cómo servir los scripts y CSS unidos y minificados a través de CDN de Azure. 

<a name="bundling"></a>
## Integración de unión y minificación de ASP.NET con CDN de Azure ##

Los scripts y las hojas de estilo CSS cambian con poca frecuencia y son los principales candidatos para la caché de CDN de Azure. Prestar servicio al sitio web entero a través de la red CDN de Azure es la manera más fácil de integrar unión y minificación con CDN de Azure. Sin embargo, como es posible elegir en contra de este enfoque por las razones descritas en [Integración de un extremo de la red CDN de Azure con el sitio web de Azure y suministro de contenido estático en las páginas web desde la red CDN de Azure](#deploy), le mostraré cómo hacerlo conservando la experiencia de desarrollador deseada de unión y minificación ASP.NET, como:

-	Gran experiencia en el modo de depuración
-	Implementación optimizada
-	Actualizaciones inmediatas a clientes de actualizaciones de versión de script/CSS
-	Mecanismo de reserva cuando el extremo de CDN falla
-	Menor modificación del código

En el proyecto ASP.NET que creó en [	Integración de un extremo de la red CDN de Azure con el sitio web de Azure y suministro de contenido estático en las páginas web desde la red CDN de Azure](#deploy), abra *App_Start\BundleConfig.cs* y examine las llamadas de método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

La primera declaración `bundles.Add()` agrega un paquete de script al directorio virtual `~/bundles/jquery`. A continuación, abra *Views\Shared\_Layout.cshtml* para ver cómo se procesa la etiqueta del paquete de scripts. Encontrará la siguiente línea de código Razor:

    @Scripts.Render("~/bundles/jquery")

Cuando este código Razor se ejecute en el sitio web de Azure, procesará una etiqueta `<script>` para el paquete de scripts similar a la siguiente: 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Sin embargo, cuando se ejecute en Visual Studio mediante `F5`, procesará cada archivo de script del paquete de forma individual (en el caso anterior, solo hay un archivo de script en el paquete):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Esto permite depurar el código JavaScript de su entorno de desarrollo y, al mismo tiempo, reducir las conexiones cliente simultáneas (unión) y mejorar el rendimiento de la descarga de archivos (minificación) en producción. Es una excelente característica para preservar con la integración de CDN de Azure. Además, como el paquete procesado ya contiene una cadena de versión generada automáticamente, querrá replicar esa funcionalidad para que cada vez que actualice su versión de jQuery a través de NuGet, se pueda actualizar en el cliente lo antes posible.

Siga estos pasos para la integración de la unión y minificación de ASP.NET con el extremo de CDN.

1. De vuelta en *App_Start\BundleConfig.cs*, modifique los métodos `bundles.Add()` para usar un [constructor de paquetes](http://msdn.microsoft.com/es-es/library/jj646464.aspx) diferente, uno que especifique una dirección de CDN. Para ello, sustituya la definición de método `RegisterBundles` por el código siguiente:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	Asegúrese de sustituir`<yourCDNName>` por el nombre de su CDN de Azure.

	En otras palabras, ha configurado `bundles.UseCdn = true` y agregado una URL de CDN diseñada especialmente a cada paquete. Por ejemplo, el primer constructor del código:

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	es igual que: 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	Este constructor indica que en la unión y minificación de ASP.NET se procesen archivos de script individuales cuando se depuren localmente, pero que se use la dirección de CDN especificada para acceder al script en cuestión. Sin embargo, observe dos características importantes con esta URL de CDN diseñada especialmente:
	
	-	El origen de esta dirección URL de CDN es `http://<nombreDeSuSitio>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>`, que es en realidad el directorio virtual del paquete de scripts en su aplicación web.
	-	Como está usando el constructor de CDN, la etiqueta de script de CDN del paquete ya no contiene la cadena de versión generada automáticamente en la URL procesada. Debe generar manualmente una cadena de versión única cada vez que se modifique el paquete de scripts con el fin de forzar un error de caché en la red CDN de Azure. Al mismo tiempo, esta cadena de versión única debe permanecer constante lo que dure la implementación para aumentar los aciertos de la caché en la red CDN de Azure después de implementarse el paquete.
	-	La cadena de consulta v=<W.X.Y.Z> se extrae de *Properties\AssemblyInfo.cs* en el proyecto ASP.NET. Puede tener un flujo de trabajo de implementación que incluya incrementar la versión de ensamblado cada vez que publica en Azure. O bien, puede modificar *Properties\AssemblyInfo.cs* en su proyecto para incrementar automáticamente la cadena de versión cada vez que compila, mediante el carácter comodín '*'. Por ejemplo:
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		Cualquier otra estrategia para optimizar la generación de una cadena única durante una implementación funcionará aquí.

3. Vuelva a publicar la aplicación ASP.NET y obtenga acceso a la página principal.
 
4. Vea el código HTML de la página. Debería poder ver la URL de CDN procesada, con una cadena de versión única cada vez que vuelve a publicar los cambios en el sitio web de Azure. Por ejemplo:  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;>&lt;/script>

	...

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. En Visual Studio, depure la aplicación ASP.NET en Visual Studio escribiendo `F5`., 

6. Vea el código HTML de la página. Aún verá cada archivo de script procesado de forma individual para que pueda tener una experiencia de depuración coherente en Visual Studio.  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## Mecanismo de reserva para URL de CDN ##

Cuando el extremo de CDN de Azure no funcione por cualquier motivo, querrá que su página web sea lo bastante inteligente como para acceder al servidor web de origen como opción de reserva para cargar JavaScript o Bootstrap. Es grave perder imágenes en su sitio web debido a la falta de disponibilidad de la red CDN, pero aún es más grave perder la funcionalidad esencial de página que proporcionan sus scripts y hojas de estilos.

La clase [Bundle](http://msdn.microsoft.com/es-es/library/system.web.optimization.bundle.aspx) contiene una propiedad llamada [CdnFallbackExpression](http://msdn.microsoft.com/es-es/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que le permite configurar el mecanismo de reserva en caso de error de CDN. Para usar esta propiedad, siga estos pasos:

1. En el proyecto ASP.NET, abra *App_Start\BundleConfig.cs*, donde ha agregado una URL de CDN a cada [constructor de paquetes](http://msdn.microsoft.com/es-es/library/jj646464.aspx), y realice los siguientes cambios que se resaltan para agregar el mecanismo de reserva a los paquetes predeterminados:  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	Cuando `CdnFallbackExpression` no tiene un valor null, el script se inyecta en el código HTML para probar si el paquete está cargado correctamente y, si no, acceder al paquete directamente desde el servidor web de origen. Esta propiedad se debe configurar como una expresión de JavaScript que prueba si el paquete de CDN respectivo se ha cargado correctamente. La expresión necesaria para probar cada paquete es diferente según el contenido. En el caso de los paquetes predeterminados anteriores:
	
	-	`window.jquery` se define en jquery-{version}.js
	-	`$.validator` se define en jquery.validate.js
	-	`window.Modernizr` se define en modernizer-{version}.js
	-	`$.fn.modal` se define en bootstrap.js
	
	Habrá observado que no hemos configurado CdnFallbackExpression para el paquete `~/Cointent/css`. El motivo es que actualmente hay un [error en System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que inyecta una etiqueta `<script>` para la CSS de reserva en lugar de la etiqueta `<link>` esperada.
	
	Sin embargo, existe una buena solución de [reserva de paquetes de estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) que ofrece [Ember Consulting Group](https://github.com/EmberConsultingGroup). 

2. Para usar la solución alternativa para CSS, cree un nuevo archivo .cs en la carpeta *App_Start* del proyecto ASP.NET llamada *StyleBundleExtensions.cs*, y sustituya su contenido por el [código de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs). 

4. En *App_Start\StyleFundleExtensions.cs*, cambie el nombre del espacio de nombres al espacio de nombres de la aplicación ASP.NET (por ejemplo **cdnwebapp**). 

3. Vuelva a `App_Start\BundleConfig.cs` y modifique la última instrucción `bundles.Add` con el siguiente código que se resalta:  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	Este nuevo método de extensión usa la misma idea para inyectar el script en el código HTML a fin de comprobar si hay una coincidencia en el DOM para un nombre de clase, nombre de regla o valor de regla definidos en el paquete de CSS y, en caso de no encontrarla, recurre al servidor web de origen.

4. Vuelva a publicar en el sitio Web de Azure y obtenga acceso a la página principal. 
5. Vea el código HTML de la página. Debería encontrar scripts inyectados simulares a los siguientes:    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script>(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	Observe que el script inyectado para el paquete de CSS contiene aún el residuo errante de la propiedad `CdnFallbackExpression` en la línea:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	Pero como la primera parte de la expresión || siempre devolverá true (en la línea directamente encima de esa), la función document.write() nunca se ejecutará.

6. Para probar si el script de reserva funciona, vuelva al panel del extremo de la red CDN y haga clic en **Deshabilitar extremo**.

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Actualice la ventana del explorador para el sitio web de Azure. Ahora debería ver que todos los scripts y hojas de estilo están correctamente cargados.

# Más información #
- [Información general de la red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Entrega de contenido desde la red CDN de Azure en su aplicación web](http://azure.microsoft.com/es-es/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](http://azure.microsoft.com/es-es/documentation/articles/cdn-cloud-service-with-cdn/)
- [Unión y minificación de ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Uso de la red CDN en Azure](http://azure.microsoft.com/es-es/documentation/articles/cdn-how-to-use/)
