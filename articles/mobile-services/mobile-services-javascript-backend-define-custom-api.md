<properties
	pageTitle="Definición de un extremo de API personalizada en un servicio móvil back-end de JavaScript | Servicios móviles de Azure"
	description="Más información sobre cómo definir un extremo de API personalizada en un servicio móvil back-end de JavaScript"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# Definición de un extremo de API personalizada en un servicio móvil back-end de JavaScript

> [AZURE.SELECTOR]
- [Back-end de JavaScript](./mobile-services-javascript-backend-define-custom-api.md)
- [Back-end de .NET](./mobile-services-dotnet-backend-define-custom-api.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para ver la versión equivalente de este tema en Aplicaciones móviles, consulte [Cómo definir un controlador de API personalizada](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#CustomAPI).

En este tema se muestra cómo definir un extremo de API personalizada en un servicio móvil back-end de JavaScript. Una API personalizada le permite definir extremos personalizados con funcionalidad de servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura de base de datos. Mediante una API personalizada, tiene más control sobre la mensajería, incluidos los encabezados HTTP y el formato del cuerpo.

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

Para obtener información sobre cómo invocar una API personalizada en la aplicación con una biblioteca de cliente de Servicios móviles, vea [Llamada a una API personalizada](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) en la referencia del SDK de cliente.


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0727_2016-->