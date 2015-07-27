<properties 
	pageTitle="Llamada a una API personalizada desde un cliente de Windows Phone - Servicios móviles" 
	description="Obtenga información acerca de cómo definir una API personalizada y, a continuación, llamarla desde una aplicación de Windows Phone que utiliza Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# Llamada a una API personalizada desde el cliente

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de Windows Phone. Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

La API personalizada que se creó en este tema le ofrece la posibilidad de enviar una sola consulta de POST que establece la marca completada en `true` para todos los elementos todo en la tabla. Sin esta API personalizada, el cliente tendría que enviar consultas individuales para actualizar la marca para cada elemento todo en la tabla.

Esta funcionalidad se agregará a la aplicación que creó cuando completó el tutorial [Incorporación de Servicios móviles a una aplicación existente](mobile-services-windows-phone-get-started-data.md). Este tutorial se basa en el ejemplo GetStartedWithData, una simple aplicación TodoList. Antes de iniciar este tutorial, primero debe completar el tutorial [Incorporación de servicios móviles a una aplicación existente](mobile-services-windows-phone-get-started-data.md).

## <a name="define-custom-api"></a>Definición de la API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]

## Pasos siguientes

En este tema se ha explicado cómo usar el método **InvokeApiAsync** para llamar a una API personalizada bastante sencilla desde su aplicación de Windows Phone. Para obtener más información acerca del método **InvokeApiAsync**, consulte la publicación [API personalizada en Servicios móviles de Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

También, considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Referencia del script del servidor de Servicios móviles] <br/>Obtenga más información sobre la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de código fuente] <br/>Obtenga información sobre cómo usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[Get started with data]: mobile-services-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-windows-phone-get-started-users.md
[Get started with push notifications]: ../mobile-services-windows-phone-get-started-push.md

[Almacenamiento de scripts de servidor en control de código fuente]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO3-->