<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Llamada a una API personalizada desde una aplicación HTML

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/es-es/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/es-es/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación HTML. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una inserción, actualización, eliminación u operación de lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Podrá agregar esta funcionalidad a la aplicación que creó cuando completó el tutorial de [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o de [Introducción a los datos][Introducción a los datos]. Para hacer esto, debe completar los siguientes pasos:

1.  [Definición de la API personalizada][Definición de la API personalizada]
2.  [Actualización de la aplicación para llamar a la API personalizada][Actualización de la aplicación para llamar a la API personalizada]
3.  [Prueba de la aplicación][Prueba de la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].

## <a name="define-custom-api"></a>Definición de la API personalizada

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación </span>Actualización de la aplicación para llamar a la API personalizada

1.  Mediante el editor de texto, abra el archivo index.html, busque el elemento **button** llamado `buttonRefresh` y agregue el siguiente elemento nuevo justo después de él:

        <button id="buttonCompleteAll">Complete All</button> 

    Esta acción agrega un nuevo botón a la página.

2.  En page.js y después de la función **refreshTodoItems**, agregue el siguiente código:

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    Este método controla el evento **Click** del nuevo botón. El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores.

## <a name="test-app"></a>Prueba de la aplicación

1.  Actualice el explorador.

2.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

3.  Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4.  Haga clic en el botón **Complete All**. Aparece un cuadro de diálogo de mensaje que indica el número de elementos marcados como completos y la consulta filtrada se vuelve a ejecutar, con lo que se borran todos los elementos de la lista.

## Pasos siguientes

Ahora que ha creado una API personalizada y la llamó desde su aplicación HTML, considere la posibilidad de profundizar más sobre los siguientes temas de Servicios móviles:

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
  <br/>Obtenga más información sobre la creación de API personalizadas.




  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-html-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-html-get-started-data
  [Definición de la API personalizada]: #define-custom-api
  [Actualización de la aplicación para llamar a la API personalizada]: #update-app
  [Prueba de la aplicación]: #test-app
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
