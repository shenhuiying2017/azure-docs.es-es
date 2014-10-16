<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<a name="getting-started"> </a>
Introducción a los Servicios móviles
====================================

<div class="dev-center-tutorial-selector sublanding">
	<a href="/es-es/documentation/articles/mobile-services-windows-store-get-started" title="Windows Store">Windows Store</a>
	<a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
	<a href="/es-es/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
	<a href="/es-es/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
	<a href="/es-es/documentation/articles/mobile-services-html-get-started" title="HTML" class="current">HTML</a>
	<a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
	<a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/es-es/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
	<a href="/es-es/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
</div>

<!--<div class="dev-center-tutorial-subselector">
	<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-html-get-started/" title=".NET backend">.NET backend</a> | 
	<a href="/es-es/documentation/articles/mobile-services-html-get-started/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>-->

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se indica cómo agregar un servicio back-end basado en la nube a una aplicación HTML mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. Puede ver la versión en vídeo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">3:51</span></div>
</div>

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png)

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones HTML.

<div class="dev-callout"><b>Nota:</b>

<p>Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Evaluación gratuita de Azure</a>.</p>
</div>

### Requisitos adicionales

-   Este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

    -   **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=286333).
    -   **En MacOS X**: Python, que ya debería estar instalado.
    -   **En Linux**: Python. Debe instalar la [última versión de Python](http://go.microsoft.com/fwlink/p/?LinkId=286342) (información en inglés).

    Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

-   Un servidor web compatible con HTML5.

<a name="create-new-service"> </a>
Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Creación de una aplicaciónCreación de una aplicación HTML
------------------------------------------------------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de HTML que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows** en **Choose platform** y, a continuación, expanda **Create a new Windows Store app**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear y hospedar una aplicación HTML conectada al servicio móvil.

	![](./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png)

3.  Haga clic en **Create TodoItems table** para crear una tabla donde almacenar datos de la aplicación.

4.  En **Download and run your app**, haga clic en **Download**.

	De este modo se descargan los archivos del sitio para la aplicación de *Lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido en el equipo local y anote dónde lo guardó.

5.  En la pestaña **Configure**, compruebe que `localhost` ya esté incluido en la lista **Allow requests from host names** bajo **Cross-Origin Resource Sharing (CORS)**. Si no lo está, escriba `localhost` en el campo **Host name** y, a continuación, haga clic en **Save**.

	![](./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png)

    <div class="dev-callout"><b>Nota:</b>
        <p>Si implementa la aplicación de inicio rápido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista <strong>Allow requests from host names</strong>. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx" target="_blank">Uso compartido de recursos entre orígenes</a>.</p>
    </div>

Hospedaje y ejecución de su aplicación HTML
-------------------------------------------

La etapa final de este tutorial consiste en hospedar y ejecutar la aplicación nueva en su equipo local.

1.  Busque la ubicación donde guardó los archivos comprimidos del proyecto, expanda los archivos en su equipo e inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

	<div class="dev-callout"><b>Nota:</b>

    <p>En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.</p>
	</div>

    De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

2.  Abra la URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Enter new task** y, a continuación, haga clic en **Add**.

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

	<div class="dev-callout"><b>Nota:</b>

	<p>Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo app.js.</p>
	</div>

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

<a name="next-steps"> </a>
Pasos siguientes
----------------

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   **[Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-html)**  
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   **[Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-html)**  
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   **[Referencia conceptual de Servicios móviles con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)**  
    Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML/JavaScript.


<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png
