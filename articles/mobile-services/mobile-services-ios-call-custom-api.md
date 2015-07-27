<properties
	pageTitle="Cómo llamar a una API personalizada desde un cliente iOS"
	description="Obtenga información acerca de cómo definir una API personalizada y, a continuación, llamarla desde una aplicación de iOS que utiliza Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# Llamada a una API personalizada desde un cliente iOS (backend JavaScript)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

En este tema se muestra cómo realizar una llamada a una API personalizada desde una aplicación de iOS. Una API personalizada le permite definir extremos personalizados con funcionalidad de servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura de base de datos. Mediante una API personalizada, tiene más control sobre la mensajería, incluidos los encabezados HTTP y el formato del cuerpo.

## <a name="define-custom-api"></a>Definición de una API personalizada

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## Pasos siguientes

En este tema se ha explicado cómo usar el método **invokeApi** para llamar a una API personalizada bastante sencilla desde su aplicación de iOS. Para obtener más información acerca del método **invokeApi**, consulte la publicación [API personalizada en Servicios móviles de Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

También, considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

* [Referencia del script del servidor de Servicios móviles] <br/>Obtenga más información sobre la creación de API personalizadas.

* [Almacenamiento de scripts de servidor en control de código fuente] <br/>Obtenga información sobre cómo usar la característica de control de código fuente para desarrollar y publicar de manera más fácil y segura código script de la API personalizada.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[Almacenamiento de scripts de servidor en control de código fuente]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO3-->