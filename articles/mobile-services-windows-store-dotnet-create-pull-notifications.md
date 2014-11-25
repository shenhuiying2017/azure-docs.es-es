<properties linkid="develop-mobile-tutorials-create-pull-notifications-dotnet" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Definición de una API personalizada que admita notificaciones periódicas

<div class="dev-center-tutorial-selector"> 
    <a href="/es-es/develop/mobile/tutorials/create-pull-notifications-dotnet" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/create-pull-notifications-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
</div>

En este tema se muestra cómo utilizar una API personalizada para admitir notificaciones periódicas en una aplicación de la Tienda Windows. Con las notificaciones periódicas habilitadas, Windows tendrá acceso de manera periódica a su extremo de API personalizada y usará el XML devuelto, en un formato específico de icono, para actualizar el icono de la aplicación en el menú Inicio. Para obtener más información, consulte [Introducción a las notificaciones periódicas][Introducción a las notificaciones periódicas].

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o de [Introducción a los datos][Introducción a los datos]. Para hacer esto, debe completar los siguientes pasos:

1.  [Definición de la API personalizada][Definición de la API personalizada]
2.  [Actualización de la aplicación para activar las notificaciones periódicas][Actualización de la aplicación para activar las notificaciones periódicas]
3.  [Prueba de la aplicación][Prueba de la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar los tutoriales [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].

## <a name="define-custom-api"></a>Definición de la API personalizada

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![][0]

2.  Haga clic en la pestaña **API** y, a continuación, haga clic en **Create a custom API**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create a new custom API**.

3.  Cambie **Get permission** por **Everyone**, escriba *iconos* en **API name** y haga clic en el botón de comprobación.

    ![][2]

    Con esto se crea la API con acceso GET público.

4.  Haga clic en la entrada nueva de iconos en la tabla de API.

    ![][3]

5.  Haga clic en la pestaña **Script** y reemplace el código existente por el siguiente:

        exports.get = function(request, response) {
            var wns = require('wns');
            var todoItems = request.service.tables.getTable('TodoItem');
            todoItems.where({
                complete: false
            }).read({
                success: sendResponse
            });

            function sendResponse(results) {
                var tileText = {
                    text1: "My todo list"
                };
                var i = 0;
                console.log(results)
                results.forEach(function(item) {
                    tileText["text" + (i + 2)] = item.text;
                    i++;
                });
                var xml = wns.createTileSquareText01(tileText);
                response.set('content-type', 'application/xml');
                response.send(200, xml);
            }
        };

    Este código devuelve los 3 elementos superiores incompletos de la tabla TodoItem y, a continuación, los carga en un objeto JSON que se pasa a la función **wns**.**createTileSquareText01**. Esta función devuelve el siguiente XML de plantilla de icono:

        <tile>
            <visual>
                <binding template="TileSquareText01">
                    <text id="1">My todo list</text>
                    <text id="2">Task 1</text>
                    <text id="3">Task 2</text>
                    <text id="4">Task 3</text>
                </binding>
            </visual>
        </tile>

    La función **exports.get** se utiliza porque el cliente enviará una solicitud GET para tener acceso a la plantilla de icono.

    <div class="dev-callout"><b>Nota:</b>
    <p>Este script de API personalizado utiliza el <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">m&oacute;dulo wns</a> de Node.js, al que se hace referencia con la funci&oacute;n <strong>require</strong>. Este m&oacute;dulo es diferente del <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objeto wns</a> devuelto por el <a href="http://msdn.microsoft.com/es-es/library/windowsazure/jj554217.aspx">objeto push</a>, que se usa para enviar notificaciones de inserci&oacute;n desde scripts de servidor.</p>
</div>

A continuación, deberá modificar la aplicación de inicio rápido para iniciar las notificaciones periódicas que actualizan el icono dinámico solicitando la nueva API personalizada.

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación </span>Actualización de la aplicación para activar las notificaciones periódicas

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación de inicio rápido del tutorial anterior.

2.  Asegúrese de que aparezca al menos un elemento. Si no hay ningún elemento, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

3.  En Visual Studio, abra el archivo de proyecto App.xaml.cs y agregue la siguiente instrucción using:

        using Windows.UI.Notifications;

4.  Agregue el siguiente código en el controlador de eventos **OnLaunched**:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

    Este código activa las notificaciones periódicas para solicitar los datos de la plantilla icono desde la nueva API personalizada **tiles**. Seleccione un valor [PeriodicUpdateRecurrance] que se adapte mejor a la frecuencia de actualización de sus datos.

## <a name="test-app"></a>Prueba de la aplicación

1.  En Visual Studio, presione la tecla F5 para volver a ejecutar la aplicación.

    Esto activará las notificaciones periódicas.

2.  Diríjase a la pantalla de Inicio, localice el icono dinámico de la aplicación y compruebe que los datos del elemento se muestran ahora en el icono.

    ![][4]

## Pasos siguientes

Ahora que ha creado una notificación periódica, podría averiguar más información acerca de los siguientes temas de Servicios móviles:

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Las notificaciones periódicas se administran en Windows y solo se producen conforme a una programación predefinida. Las notificaciones de inserción se pueden enviar mediante el servicio móvil previa petición y pueden ser notificaciones del sistema, notificaciones de icono y notificaciones sin procesar.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información sobre la creación de API personalizadas.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

 

 



  [Introducción a las notificaciones periódicas]: http://msdn.microsoft.com/es-es/library/windows/apps/jj150587.aspx
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
  [Definición de la API personalizada]: #define-custom-api
  [Actualización de la aplicación para activar las notificaciones periódicas]: #update-app
  [Prueba de la aplicación]: #test-app
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
  [4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
