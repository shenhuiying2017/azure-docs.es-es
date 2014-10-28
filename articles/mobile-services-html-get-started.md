<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se indica c&oacute;mo agregar un servicio back-end basado en la nube a una aplicaci&oacute;n HTML mediante los Servicios m&oacute;viles de Azure. Con este tutorial crear&aacute; tanto un servicio m&oacute;vil nuevo como una aplicaci&oacute;n simple de <em>Lista de pendientes</em> que almacena datos de la aplicaci&oacute;n en el servicio m&oacute;vil nuevo. Puede ver la versi&oacute;n en v&iacute;deo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">3:51:00</span></div>

</div>

La siguiente captura de pantalla muestra la aplicación final:

![][]

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones HTML.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

### Requisitos adicionales

-   Este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

    -   **En Windows**: IIS Express. El [Instalador de plataforma web de Microsoft][] se encarga de instalar IIS Express.
    -   **En MacOS X**: Python, que ya debería estar instalado.
    -   **En Linux**: Python. Debe instalar la [última versión de Python][] (información en inglés).

    Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

-   Un servidor web compatible con HTML5.

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Creación de una aplicación nueva</span>Creación de una aplicación HTML nueva

</h2>
Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de HTML que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows** en **Choose platform** y, a continuación, expanda **Create a new Windows Store app**.

    ![][1]

    Con esto se muestran los tres sencillos pasos requeridos para crear y hospedar una aplicación HTML conectada al servicio móvil.

    ![][2]

3.  Haga clic en **Create TodoItems table** para crear una tabla donde almacenar datos de la aplicación.

4.  En **Download and run your app**, haga clic en **Download**.

    De este modo se descargan los archivos del sitio web para la aplicación *Lista de pendientes* de muestra conectada al servicio móvil. Guarde el archivo comprimido en el equipo local y anote dónde lo guardó.

5.  En la pestaña **Configurar**, compruebe que `localhost` ya esté incluido en la lista **Permitir solicitudes de nombres de host** bajo **Cross-Origin Resource Sharing (CORS)**. Si no lo está, escriba `localhost` en el campo **Nombre de host** y, a continuación, haga clic en **Guardar**.

    ![][3]

    <div class="dev-callout"><b>Nota:</b>
    <p>Si implementa la aplicaci&oacute;n de inicio r&aacute;pido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista <strong>Permitir solicitudes de nombres de host</strong>. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx" target="_blank">Uso compartido de recursos entre or&iacute;genes</a>.</p>
</div>

## Hospedaje y ejecución de su aplicación HTML

La etapa final de este tutorial consiste en hospedar y ejecutar la aplicación nueva en su equipo local.

1.  Busque la ubicación donde guardó los archivos comprimidos del proyecto, expanda los archivos en su equipo e inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    <div class="dev-callout"><b>Nota:</b>
    <p>En un equipo con Windows, escriba &quot;R&quot; cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podr&iacute;a advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador contin&uacute;e con la carga del script.</p>
</div>

    De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

2.  Abra la URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Enter new task** y, a continuación, haga clic en **Add**.

    ![][4]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>Puede revisar el c&oacute;digo de acceso al servicio m&oacute;vil para consultar e insertar datos; este se encuentra en el archivo app.js.</p> 
</div>

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![][5]

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![][6]

## <a name="next-steps"> </a>Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   **[Introducción a los datos][]**
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   **[Llamada a una API personalizada desde una aplicación HTML][]**
    
	Conéctese a su aplicación HTML mediante una API personalizada hospedada en Servicios móviles.

-   **[Introducción a la autenticación][]**
    
	Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   **[Referencia conceptual de Servicios móviles con HTML/JavaScript][]**
    
	Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML/JavaScript.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Ver el tutorial (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=287040
  []: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Instalador de plataforma web de Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [última versión de Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Uso compartido de recursos entre orígenes]: http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-html
  [Llamada a una API personalizada desde una aplicación HTML]: /es-es/documentation/articles/mobile-services-html-call-custom-api
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html
  [Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client
