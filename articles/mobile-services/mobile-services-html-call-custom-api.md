<properties 
	pageTitle="Llamada a una API personalizada desde un cliente HTML - Servicios móviles" 
	description="Obtenga información acerca de cómo definir una API personalizada y, a continuación, llamarla desde una aplicación de HTML que utiliza Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="bureado"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Llamada a una API personalizada desde una aplicación HTML

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación HTML. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente].

## <a name="define-custom-api"></a>Definición de la API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>Actualización de la aplicación para llamar a la API personalizada

1. Mediante el editor de texto, abra el archivo index.html, busque el elemento **button** llamado `buttonRefresh` y agregue el siguiente elemento nuevo justo después de él: 

		<button id="buttonCompleteAll">Complete All</button> 

	Esta acción agrega un nuevo botón a la página.

2. En page.js y después de la función **refreshTodoItems**, agregue el siguiente código:

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

	Este método controla el evento **Click** del botón nuevo. El método **invokeApi** se llama en el cliente, el cual envía una solicitud de POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores.

## <a name="test-app"></a>Prueba de la aplicación

1. Actualice el explorador.

2. En la aplicación, escriba algo de texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

3. Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4. Haga clic en el botón **Completar todo**. Aparece un cuadro de diálogo de mensaje que indica el número de elementos marcados como completos y la consulta filtrada se vuelve a ejecutar, con lo que se borran todos los elementos de la lista.

## Pasos siguientes

En este tema se ha explicado cómo usar la función **invokeApi** para llamar a una API personalizada bastante sencilla desde su aplicación de HTML/JavaScript. Para obtener más información acerca de la función **invokeApi**, consulte la publicación [API personalizada en Servicios móviles de Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

También, considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Referencia del script del servidor de Servicios móviles] <br/>Obtenga más información sobre la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de código fuente] <br/>Obtenga información sobre cómo usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introducción a los Servicios móviles]: mobile-services-html-get-started.md
[Incorporación de Servicios móviles a una aplicación existente]: mobile-services-html-get-started-data.md
[Almacenamiento de scripts de servidor en control de código fuente]: mobile-services-store-scripts-source-control.md

<!---HONumber=July15_HO1-->