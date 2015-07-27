<properties 
	pageTitle="Uso de Servicios móviles para cargar imágenes en el almacenamiento de blobs (Windows Phone) | Servicios móviles" 
	description="Obtenga información acerca de cómo usar Servicios móviles para cargar imágenes en el almacenamiento de blobs de Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Información general
Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación Windows Phone 8 o Windows Phone 8.1 Silverlight cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de cinco minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agrega funcionalidad al [proyecto de aplicación de ejemplo GetStartedWithData](mobile-services-dotnet-backend-windows-phone-get-started-data.md) para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles.

##Requisitos previos 

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2013 o una versión posterior
+ [SDK de Windows Phone 8.0] o una versión posterior
+ El Administrador de paquetes NuGet instalado para Microsoft Visual Studio.
+ [Cuenta de almacenamiento de Azure][How To Create a Storage Account]
+ Realice el tutorial [Incorporación de Servicios móviles a una aplicación existente](mobile-services-dotnet-backend-windows-phone-get-started-data.md)  

>[AZURE.NOTE]Este tutorial solo es válido para aplicaciones de Windows Phone 8 y Windows Phone 8.1 Silverlight. No admite aplicaciones Windows Phone Store 8.1 o Windows 8.1 universales.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Instalación del cliente de Almacenamiento para aplicaciones de la Tienda Windows

Para poder utilizar una SAS para cargar imágenes de su aplicación en el almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación cliente y luego seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, seleccione **Incluir versión preliminar**, busque **WindowsAzure.Storage-Preview**, haga clic en **Instalar** en el paquete de **almacenamiento de Azure** y, a continuación, acepte los contratos de licencia.

  	![][2]

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[SDK de Windows Phone 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=July15_HO3-->