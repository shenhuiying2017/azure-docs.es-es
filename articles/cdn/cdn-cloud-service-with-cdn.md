---
title: "Integración de un servicio en la nube de Azure con la red CDN de Azure | Microsoft Docs"
description: "Aprenda a implementar un servicio en la nube que ofrece contenido de un punto de conexión de la red CDN de Azure integrado"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: f2849fe25fd0d5b3dc26598ffba7591cb7433161
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---
# <a name="intro"></a> Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure
Un servicio en la nube puede integrarse con CDN de Azure, sirviendo cualquier contenido desde la ubicación del servicio en la nube. Este enfoque le ofrece las siguientes ventajas:

* Fácil implementación y actualización de imágenes, scripts y hojas de estilo en los directorios de proyecto del servicio en la nube
* Actualización fácil de los paquetes NuGet en el servicio en la nube, como versiones de jQuery o Bootstrap
* Administración de la aplicación web y del contenido servido por CDN desde la misma interfaz de Visual Studio
* Flujo de trabajo de implementación unificado para la aplicación web y el contenido servido por CDN
* Integración de unión y minificación de ASP.NET con CDN de Azure

## <a name="what-you-will-learn"></a>Lo qué aprenderá
En este tutorial, aprenderá a:

* [Integración de un extremo de red CDN de Azure con el servicio en la nube y suministro de contenido estático en las páginas web desde CDN de Azure](#deploy)
* [Configuración de los valores de la caché para el contenido estático en el servicio en la nube](#caching)
* [Suministro de contenido de acciones de controlador a través de la red CDN de Azure](#controller)
* [Suministro de contenido unido y minificado a través de la red CDN de Azure preservando al mismo tiempo la experiencia de depuración de scripts en Visual Studio](#bundling)
* [Configuración de la reserva de los scripts y CSS cuando la red CDN de Azure está sin conexión](#fallback)

## <a name="what-you-will-build"></a>Lo que va a crear
Implementaremos un rol web de servicio en la nube mediante la plantilla predeterminada ASP.NET MVC, agregaremos código para servir contenido desde una red CDN de Azure integrada, como una imagen, los resultados de las acciones de controlador y los archivos predeterminados de JavaScript y CSS, y también escribiremos código para configurar el mecanismo de reserva de los paquetes servidos en caso de desconexión de red CDN.

## <a name="what-you-will-need"></a>Qué necesita
Este tutorial cuenta con los siguientes requisitos previos:

* Una [cuenta de Microsoft Azure activa](/account/)
* Visual Studio 2015 con el [SDK de Azure](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure:
> 
> * Puede [abrir una cuenta de Azure de manera gratuita](https://azure.microsoft.com/pricing/free-trial/) - Obtiene crédito que puede utilizar para probar los servicios de Azure de pago, e incluso una vez agotado este podrá mantener la cuenta y utilizar servicios gratuitos de Azure, como Sitios web.
> * Puede [activar las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) - Su suscripción a MSDN le proporciona crédito todos los meses que puede utilizar para servicios de Azure de pago.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>un servicio en la nube
En esta sección, implementará la plantilla de aplicación predeterminada ASP.NET MVC de Visual Studio 2015 en un rol web de servicio en la nube, y luego la integrará con un nuevo punto de conexión de red CDN. Siga las instrucciones que se describen a continuación:

1. En Visual Studio 2015, cree un nuevo servicio en la nube de Azure desde la barra de menús; para ello, debe ir a **Archivo > Nuevo > Proyecto > Nube > Servicio en la nube de Azure**. Asígnele un nombre y haga clic en **Aceptar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Seleccione **Rol web de ASP.NET** y haga clic en el botón **>**. Haga clic en Aceptar.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Seleccione **MVC** y haga clic en **Aceptar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Ahora, publique este rol web en un servicio en la nube de Azure. Haga clic con el botón derecho en el proyecto del servicio en la nube y seleccione **Publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Si aún no ha iniciado sesión en Microsoft Azure, haga clic en la lista desplegable **Agregar una cuenta...** y, después, en la opción de menú **Agregar una cuenta**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. En la página de inicio de sesión, inicie sesión con la cuenta de Microsoft que ha usado para activar su cuenta de Azure.
7. Una vez que haya iniciado la sesión, haga clic en **Siguiente**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Suponiendo que no haya creado un servicio en la nube o una cuenta de almacenamiento, Visual Studio le ayudará a crear ambos. En el cuadro de diálogo **Crear cuenta y servicio en la nube** , escriba el nombre de servicio deseado y seleccione la región que quiera. A continuación, haga clic en **Crear**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. En la página de configuración de publicación, compruebe la configuración y haga clic en **Publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > El proceso de publicación de los servicios en la nube tarda mucho tiempo. La opción Habilitar la implementación web en todos los roles puede acelerar la depuración del servicio en la nube al proporcionar actualizaciones rápidas (pero temporales) para los roles web. Para obtener más información sobre esta opción, consulte [Publicar un servicio en la nube mediante Azure Tools](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Cuando el **Registro de actividad de Microsoft Azure** muestre que el estado de publicación es **Completado**, creará un punto de conexión de la red CDN que se integra con este servicio en la nube.
   
   > [!WARNING]
   > Si, tras la publicación, el servicio en la nube implementado muestra una pantalla de error, es probable que se deba a que el servicio en la nube que está usando ha implementado un [SO invitado que no incluye .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Puede solucionar este problema mediante la [implementación de .NET 4.5.2 como tarea de inicio](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Crear un nuevo perfil de CDN
Un perfil de red de entrega de contenido es una colección de puntos de conexión de red de entrega de contenido.  Cada perfil contiene uno o más de estos puntos de conexión de CDN.  Puede que quiera usar varios perfiles para organizar sus puntos de conexión de la red CDN por dominio de Internet, aplicación web o cualquier otro criterio.

> [!TIP]
> Si ya tiene un perfil de red CDN que quiere usar para este tutorial, continúe con el paso [Crear un nuevo extremo de CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Crear un nuevo extremo de CDN
**Para crear un nuevo extremo de una red CDN para una cuenta de almacenamiento**

1. En el [Portal de administración de Azure](https://portal.azure.com), vaya a su perfil de CDN.  Puede haberlo anclado al panel en el paso anterior.  Si no lo hace, para encontrarlo, haga clic en **Examinar**, en **Perfiles de CDN** y, luego, haga clic en el perfil al que planea agregar el punto de conexión.
   
    Aparece la hoja del perfil de CDN.
   
    ![Perfil de CDN][cdn-profile-settings]
2. Haga clic en el botón **Agregar extremo** .
   
    ![Botón Agregar punto de conexión][cdn-new-endpoint-button]
   
    Aparecerá la hoja **Agregar un extremo** .
   
    ![Hoja Agregar punto de conexión][cdn-add-endpoint]
3. Escriba un **Nombre** para este punto de conexión de red de entrega de contenido.  Este nombre se usará para obtener acceso a sus recursos almacenados en caché en el dominio `<EndpointName>.azureedge.net`.
4. En la lista desplegable **Tipo de origen** , seleccione *Servicio en la nube*.  
5. En la lista desplegable **Nombre de host de origen** , seleccione su servicio en la nube.
6. Deje los valores predeterminados de **Ruta de acceso de origen**, **Encabezado de host de origen** y **Protocolo/puerto de origen**.  Debe especificar al menos un protocolo (HTTP o HTTPS).
7. Haga clic en el botón **Agregar** para crear el nuevo punto de conexión.
8. Una vez creado el punto de conexión, aparecerá en la lista de puntos de conexión del perfil. La visualización de la lista muestra la URL que se debe utilizar para tener acceso al contenido en caché, así como al dominio de origen.
   
    ![Punto de conexión de CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > El punto de conexión no estará disponible inmediatamente para su uso.  Se pueden tardar hasta 90 minutos en que el registro se propague a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 404 hasta que el contenido esté disponible a través de la red CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Probar el punto de conexión de CDN
Cuando el estado de publicación sea **Completado** abra una ventana del explorador y navegue a **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. En nuestra instalación, esta URL es:

    http://camservice.azureedge.net/Content/bootstrap.css

Esta URL corresponde a la siguiente URL de origen en el extremo de red CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Cuando navegue a **http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css** en función del explorador que haya usado, se le solicitará que descargue o abra el archivo bootstrap.css proveniente de la aplicación web publicada.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Puede acceder de forma parecida a cualquier dirección URL de acceso público en **http://*&lt;serviceName>*.cloudapp.net/** directamente desde el punto de conexión de la red CDN. Por ejemplo:

* Un archivo .js desde la ruta /Script
* Cualquier archivo de contenido desde la ruta /Content
* Cualquier controlador/acción
* Si la cadena de consulta está habilitada en el extremo de red CDN, cualquier URL con cadenas de consulta

De hecho, con la configuración anterior, es posible hospedar todo el servicio en la nube desde **http://*&lt;cdnName>*.azureedge.net/**. Si se va a **http://camservice.azureedge.net/**, el resultado de la acción se obtiene de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Esto no significa, sin embargo, que sea siempre una buena idea suministrar un servicio en la nube entero a través de la red CDN de Azure. 

Una red CDN con optimización de entrega estática no acelera necesariamente la entrega de recursos dinámicos que no estén diseñados para almacenarse en caché o se actualicen con mucha frecuencia, ya que la red CDN debe extraer una nueva versión del recurso desde el servidor de origen muy a menudo. En este escenario, puede habilitar la optimización [Aceleración de sitios dinámicos](cdn-dynamic-site-acceleration.md) (DSA) en el punto de conexión de la red CDN que usa varias técnicas para acelerar la entrega de recursos dinámicos que no se pueden almacenar en caché. 

Si tiene un sitio con una combinación de contenido estático y dinámico, puede servir el contenido estático de la red CDN con un tipo de optimización estático (por ejemplo, entrega de web general); asimismo, puede servir el contenido dinámico directamente desde el servidor de origen, o a través de un punto de conexión de red CDN con la optimización DSA activada caso por caso. Para tal fin, ya ha visto cómo acceder a archivos de contenido individuales desde el extremo de red CDN. Le mostraremos cómo servir una acción de controlador específica a través de un punto de conexión de red CDN específico en la sección Suministro de contenido de acciones de controlador a través de la red CDN de Azure.

La alternativa es determinar qué contenido servir desde CDN de Azure según el caso en el servicio en la nube. Para tal fin, ya ha visto cómo acceder a archivos de contenido individuales desde el extremo de red CDN. Le mostraremos cómo servir una acción de controlador específica a través del extremo de red CDN en [Suministro de contenido de acciones de controlador a través de la red CDN de Azure](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configuración de las opciones de caché para los archivos estáticos del servicio en la nube
Con la integración de la red CDN de Azure en el servicio en la nube, puede especificar cómo quiere que el contenido estático se almacene en la caché en el extremo de red CDN. Para ello, abra *Web.config* desde su proyecto de rol web (por ejemplo, WebRole1) y agregue un elemento `<staticContent>` a `<system.webServer>`. El XML siguiente configura la caché para que caduque en tres días.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Una vez realizado esto, todos los archivos estáticos del servicio en la nube observarán la misma regla en la caché de red CDN. Para un control más granular de la configuración de la caché, agregue un archivo *Web.config* a una carpeta y agregue ahí su configuración. Por ejemplo, agregue un archivo *Web.config* a la carpeta *\Content* y sustituya el contenido por el siguiente XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta configuración hace que todos los archivos estáticos de la carpeta *\Content* se almacenen en la caché durante 15 días.

Para más información sobre cómo configurar el elemento `<clientCache>` consulte [Caché de cliente &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

En [Suministro de contenido de acciones de controlador a través de la red CDN de Azure](#controller)también le mostraremos cómo configurar los valores de caché para los resultados de las acciones de controlador en la memoria caché de CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Suministro de contenido de acciones de controlador a través de la red CDN de Azure
Cuando integra un rol web de servicio en la nube con CDN de Azure, es relativamente fácil servir contenido de acciones de controlador a través de la red CDN de Azure. Además de atender el servicio en la nube directamente a través de la red CDN de Azure (como se ha mostrado anteriormente), [Maarten Balliauw](https://twitter.com/maartenballiauw) muestra cómo hacerlo con un divertido controlador MemeGenerator en [Reducing latency on the web with the Windows Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN) (Reducción de la latencia en la Web con la red CDN de Azure). Aquí simplemente lo vamos a reproducir.

Suponga que desea generar en su servicio en la nube memes basados en una imagen de Chuck Norris de joven (foto de [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Tiene una acción `Index` sencilla que permite a los clientes especificar los superlativos de la imagen y que luego genera el meme una vez que publican la acción. Dado que se trata de Chuck Norris, esperará que esta página se haga ampliamente popular en todo el mundo. Este es un buen ejemplo de servir contenido dinámico con CDN de Azure.

Siga los pasos anteriores para configurar esta acción de controlador:

1. En la carpeta *\Controllers*, cree un nuevo archivo .cs llamado *MemeGeneratorController.cs* y sustituya el contenido por el siguiente código. Asegúrese de sustituir la parte resaltada por su nombre de red CDN.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
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
                    return File(ms.ToArray(), "image/png");
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
2. Haga clic con el botón derecho en la acción `Index()` predeterminada y seleccione **Agregar vista**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Acepte la configuración que se indica a continuación y haga clic en **Agregar**.
   
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
5. Vuelva a publicar el servicio en la nube y navegue a **http://*&lt;nombreServicio>*.cloudapp.net/MemeGenerator/Index** en el explorador.

Cuando envía los valores de formulario a `/MemeGenerator/Index`, el método de acción `Index_Post` devuelve un vínculo al método de acción `Show` con el identificador de entrada respectivo. Cuando hace clic en el vínculo, llega al siguiente código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Si su depurador local está conectado, obtendrá la experiencia de depuración normal con una redirección local. Si se está ejecutando en el servicio en la nube, realizará la redirección a:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde a la siguiente URL de origen en el extremo de red CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Luego puede usar el atributo `OutputCacheAttribute` en el método `Generate` para especificar cómo se debería almacenar en caché el resultado de la acción, que atenderá CDN de Azure. El siguiente código especifica una caducidad de la caché de una hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Igualmente, puede servir contenido de cualquier acción de controlador del servicio en la nube a través de su CDN de Azure, con la opción de almacenamiento en caché deseada.

En la siguiente sección, le mostraremos cómo servir los scripts y CSS unidos y minificados a través de la red CDN de Azure.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integración de unión y minificación de ASP.NET con CDN de Azure
Los scripts y las hojas de estilo CSS cambian con poca frecuencia y son los principales candidatos para la caché de red CDN de Azure. Prestar servicio al rol web entero a través de la red CDN de Azure es la manera más fácil de integrar unión y minificación con CDN de Azure. Sin embargo, como es posible que no quiera hacer esto, le mostraremos cómo hacerlo conservando la experiencia deseada del desarrollador en unión y minificación de ASP.NET, como:

* Gran experiencia en el modo de depuración
* Implementación optimizada
* Actualizaciones inmediatas a clientes de actualizaciones de versión de script/CSS
* Mecanismo de reserva cuando el extremo de red CDN falla
* Menor modificación del código

En el proyecto **WebRole1** que creó en [Integración de un servicio en la nube con la Red de entrega de contenido (CDN) de Azure](#deploy), abra *App_Start\BundleConfig.cs* y examine las llamadas del método `bundles.Add()`.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La primera instrucción `bundles.Add()` agrega un paquete de scripts en el directorio virtual `~/bundles/jquery`. A continuación, abra *Views\Shared\_Layout.cshtml* para ver cómo se procesa la etiqueta del paquete de scripts. Encontrará la siguiente línea de código Razor:

    @Scripts.Render("~/bundles/jquery")

Cuando este código Razor se ejecute en el rol web de Azure, procesará una etiqueta `<script>` para el paquete de scripts similar a la siguiente:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Sin embargo, cuando se ejecute en Visual Studio mediante `F5`, procesará cada archivo de script del paquete de forma individual (en el caso anterior, solo hay un archivo de script en el paquete):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Esto permite depurar el código JavaScript de su entorno de desarrollo y, al mismo tiempo, reducir las conexiones cliente simultáneas (unión) y mejorar el rendimiento de la descarga de archivos (minificación) en producción. Es una excelente característica para preservar con la integración de red CDN de Azure. Además, como el paquete procesado ya contiene una cadena de versión generada automáticamente, querrá replicar esa funcionalidad para que cada vez que actualice su versión de jQuery a través de NuGet, se pueda actualizar en el cliente lo antes posible.

Siga estos pasos para la integración de la unión y minificación de ASP.NET con el extremo de red CDN.

1. De vuelta en *App_Start\BundleConfig.cs*, modifique los métodos `bundles.Add()` para usar un [constructor de paquetes](http://msdn.microsoft.com/library/jj646464.aspx) diferente, uno que especifique una dirección de CDN. Para ello, reemplace la definición del método `RegisterBundles` por el código siguiente:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Asegúrese de sustituir `<yourCDNName>` por el nombre de su CDN de Azure.
   
    En otras palabras, ha configurado `bundles.UseCdn = true` y agregado una dirección URL de red CDN diseñada especialmente para cada paquete. Por ejemplo, el primer constructor del código:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    es igual que:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Este constructor indica que en la unión y minificación de ASP.NET se procesen archivos de script individuales cuando se depuren localmente, pero que se use la dirección de red CDN especificada para acceder al script en cuestión. Sin embargo, observe dos características importantes con esta URL de red CDN diseñada especialmente:
   
   * El origen de esta URL de red CDN es `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que es realmente el directorio virtual del paquete de scripts en su servicio en la nube.
   * Como está usando el constructor de red CDN, la etiqueta de script de red CDN del paquete ya no contiene la cadena de versión generada automáticamente en la URL procesada. Debe generar manualmente una cadena de versión única cada vez que se modifique el paquete de scripts con el fin de forzar un error de caché en la red CDN de Azure. Al mismo tiempo, esta cadena de versión única debe permanecer constante lo que dure la implementación para aumentar los aciertos de la caché en la red CDN de Azure después de implementarse el paquete.
   * La cadena de consulta v=<W.X.Y.Z> se extrae de *Properties\AssemblyInfo.cs* en el proyecto de rol web. Puede tener un flujo de trabajo de implementación que incluya incrementar la versión de ensamblado cada vez que publica en Azure. O bien, puede modificar *Properties\AssemblyInfo.cs* en su proyecto para incrementar automáticamente la cadena de versión cada vez que compila, mediante el carácter comodín '*'. Por ejemplo:
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     Cualquier otra estrategia para optimizar la generación de una cadena única durante una implementación funcionará aquí.
2. Vuelva a publicar el servicio en la nube y acceda a la página principal.
3. Vea el código HTML de la página. Debería poder ver la URL de red CDN procesada, con una cadena de versión única cada vez que vuelve a publicar los cambios en el servicio en la nube. Por ejemplo:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. En Visual Studio, depure el servicio en la nube con `F5`.
5. Vea el código HTML de la página. Aún verá cada archivo de script procesado de forma individual para que pueda tener una experiencia de depuración coherente en Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo de reserva para URL de red CDN
Cuando el extremo de red CDN de Azure no funcione por cualquier motivo, querrá que su página web sea lo bastante inteligente como para acceder al servidor web de origen como opción de reserva para cargar JavaScript o Bootstrap. Es grave perder imágenes en su sitio web debido a la falta de disponibilidad de la red CDN, pero aún es más grave perder la funcionalidad esencial de página que proporcionan sus scripts y hojas de estilos.

La clase [Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contiene una propiedad llamada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que le permite configurar el mecanismo de reserva en caso de error de red CDN. Para usar esta propiedad, siga estos pasos:

1. En el proyecto de rol web, abra *App_Start\BundleConfig.cs*, donde agregó una dirección URL de CDN en cada [constructor de agrupaciones](http://msdn.microsoft.com/library/jj646464.aspx), y realice los siguientes cambios que se resaltan para agregar un mecanismo de reserva a las agrupaciones predeterminadas:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Cuando `CdnFallbackExpression` no tiene un valor null, el script se inserta en el código HTML para probar si el paquete está cargado correctamente y, en caso contrario, obtener acceso al paquete directamente desde el servidor web de origen. Esta propiedad se debe configurar como una expresión de JavaScript que prueba si el paquete de red CDN respectivo se ha cargado correctamente. La expresión necesaria para probar cada paquete es diferente según el contenido. En el caso de los paquetes predeterminados anteriores:
   
   * `window.jquery` se define en jquery-{version}.js
   * `$.validator` se define en jquery.validate.js
   * `window.Modernizr` se define en modernizer-{version}.js
   * `$.fn.modal` se define en bootstrap.js
     
     Habrá observado que no hemos configurado CdnFallbackExpression para el paquete `~/Cointent/css` . El motivo es que actualmente hay un [error en System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que inyecta una etiqueta `<script>` para la CSS de reserva en lugar de la etiqueta `<link>` esperada.
     
     Sin embargo, existe una buena [solución de reserva de paquetes de estilo](https://github.com/EmberConsultingGroup/StyleBundleFallback) que ofrece [Ember Consulting Group](https://github.com/EmberConsultingGroup).
2. Para usar la solución alternativa para CSS, cree un nuevo archivo .cs en la carpeta *App_Start* del proyecto de rol web llamado *StyleBundleExtensions.cs* y reemplace su contenido por el [código de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. En *App_Start\StyleFundleExtensions.cs*, cambie el nombre del espacio de nombres por el nombre del rol web (por ejemplo, **WebRole1**).
4. Vuelva a `App_Start\BundleConfig.cs` y modifique la última instrucción `bundles.Add` con el siguiente código resaltado:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Este nuevo método de extensión usa la misma idea para inyectar el script en el código HTML a fin de comprobar si hay una coincidencia en el DOM para un nombre de clase, nombre de regla o valor de regla definidos en el paquete de CSS y, en caso de no encontrarla, recurre al servidor web de origen.
5. Publicar el servicio en la nube y acceda a la página principal.
6. Vea el código HTML de la página. Debería encontrar scripts inyectados simulares a los siguientes:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Observe que el script inyectado para el paquete de CSS contiene aún el residuo errante de la propiedad `CdnFallbackExpression` en la línea:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Pero como la primera parte de la expresión || siempre devolverá true (en la línea directamente encima de esa), la función document.write() nunca se ejecutará.

## <a name="more-information"></a>Más información
* [Información general de la red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Uso de CDN de Azure](cdn-create-new-endpoint.md)
* [Unión y minificación de ASP.NET](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png

