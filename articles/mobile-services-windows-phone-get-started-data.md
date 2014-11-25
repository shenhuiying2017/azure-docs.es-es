<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra c&oacute;mo utilizar Servicios m&oacute;viles de Azure para aprovechar los datos en una aplicaci&oacute;n de Windows Phone 8. En este tutorial descargar&aacute; una aplicaci&oacute;n que almacena datos en memoria, crear&aacute; un nuevo servicio m&oacute;vil, integrar&aacute; el servicio m&oacute;vil en la aplicaci&oacute;n y luego iniciar&aacute; sesi&oacute;n en el Portal de administraci&oacute;n de Azure para ver los cambios que se hicieron en los datos durante la ejecuci&oacute;n de la aplicaci&oacute;n.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial est&aacute; destinado a profundizar en el uso de Azure con los Servicios m&oacute;viles para almacenar y recuperar datos en una aplicaci&oacute;n de Windows Phone 8. Para ello, en este tema se recorren muchos de los pasos que se completan autom&aacute;ticamente en el inicio r&aacute;pido de Servicios m&oacute;viles. Si esta es la primera vez que usa los Servicios m&oacute;viles, considere la posibilidad de completar antes el tutorial <a href="/es-es/develop/mobile/tutorials/get-started-wp8">Introducci&oacute;n a los Servicios m&oacute;viles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de Windows Phone 8][Descarga del proyecto de la aplicación de Windows Phone 8]
2.  [Creación del servicio móvil][Creación del servicio móvil]
3.  [Incorporación de una tabla de datos para almacenamiento][Incorporación de una tabla de datos para almacenamiento]
4.  [Actualización de la aplicación para usar Servicios móviles][Actualización de la aplicación para usar Servicios móviles]
5.  [Prueba de la aplicación en Servicios móviles][Prueba de la aplicación en Servicios móviles]

Este tutorial requiere Visual Studio 2012 Express para Windows Phone 8 y el [SDK de Windows Phone 8][SDK de Windows Phone 8] en Windows 8. Para completar este tutorial con el fin de crear una aplicación de Windows Phone 8.1, debe usar la Actualización 2 de Visual Studio 2013 o una versión posterior.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

## <a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][aplicación GetStartedWithData], un proyecto de aplicación Silverlight de Windows Phone 8. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue el proyecto de aplicación de ejemplo GetStartedWithData desde el [sitio de ejemplos de código para desarrolladores][aplicación GetStartedWithData].

    > [WACOM.NOTE] Para crear una aplicación Silverlight de create a Windows Phone 8.1, en Windows Phone 8.1 cambie el SO de destino en el proyecto de aplicación Silverlight de Windows Phone 8 descargado. Para crear una aplicación de la Tienda Windows Phone, descargue la [versión de la aplicación de Windows Phone][versión de la aplicación de Windows Phone] del proyecto de aplicación de ejemplo GetStartedWithData.

2.  En Visual Studio, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

    Observe que los objetos **TodoItem** agregados se almacenan en un elemento **ObservableCollection\<TodoItem\>** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en el cuadro de texto y, a continuación, haga clic en el botón **Save**.

    ![][0]

    Observe que el texto guardado se muestra en la lista a continuación.

## <a name="create-service"></a><span class="short-header">Creación de un servicio móvil</span>Creación de un servicio móvil en el Portal de administración

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Incorporación de una tabla nueva</span>Incorporación de una tabla nueva al servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **En línea**, busque `WindowsAzure.MobileServices`, haga clic en **Instalar** en el paquete **Servicios móviles de Azure** y luego acepte el contrato de licencia.

    ![][1]

    De este modo, se agrega la biblioteca de cliente de Servicios móviles al proyecto.

3.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

4.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][2]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

5.  En Visual Studio, abra el archivo App.xaml.cs y agregue o quite la marca de comentario en la siguiente instrucción `using`:

        using Microsoft.WindowsAzure.MobileServices;

6.  En este mismo archivo, quite la marca de comentario del código que define la variable **MobileService** y proporcione la dirección URL y la clave de aplicación del servicio móvil en el constructor **MobileServiceClient**, en ese orden.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    De esta forma, se crea una nueva instancia de **MobileServiceClient** que se usa para obtener acceso al servicio móvil.

7.  En el archivo MainPage.xaml.cs, agregue o quite la marca de comentario en las siguientes instrucciones `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  En este mismo archivo, reemplace la definición de clase **TodoItem** por el siguiente código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Comente la línea que define la colección **items** existente y luego quite la marca de comentario de las siguientes líneas:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Este código crea una colección de enlaces (**elementos**) compatible con los servicios móviles y una clase proxy para la tabla de base de datos de SQL **TodoItem** (**todoTable**).

10. En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem**.**Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

        await todoTable.InsertAsync(todoItem);

    Este código inserta un elemento nuevo en la tabla.

11. En el método **RefreshTodoItems**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

        items = await todoTable.ToCollectionAsync();

    Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos TodoItem devueltos por el servicio móvil.

12. En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

         await todoTable.UpdateAsync(item);

    Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con su servicio móvil nuevo

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en **Save**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][3]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Con esto concluye el tutorial **Introducción a los datos** para Windows Phone 8.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Windows Phone 8 para trabajar con datos en Servicios móviles. A continuación, considere la realización de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]
    
	Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]
    
	Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, también puede probar uno de los siguientes tutoriales de Windows Phone 8:

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

 
 


  [Descarga del proyecto de la aplicación de Windows Phone 8]: #download-app
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [SDK de Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [aplicación GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [versión de la aplicación de Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [Portal de administración]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-wp8
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8
