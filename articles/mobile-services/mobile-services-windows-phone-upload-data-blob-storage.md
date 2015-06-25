<properties 
	pageTitle="Uso de Servicios móviles para cargar imágenes en el almacenamiento de blobs (Windows Phone) | Servicios móviles" 
	description="Obtenga información acerca de cómo usar Servicios móviles para cargar imágenes en el almacenamiento de blobs de Azure." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	writer="wesmc" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Información general

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de cinco minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agrega funcionalidad al [proyecto de aplicación de ejemplo GetStartedWithData](mobile-services-windows-phone-get-started-data.md) para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles.


##Requisitos previos

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2012 Express para Windows 8 o una versión posterior
+ [SDK de Windows Phone 8.0] o una versión posterior.
+ El Administrador de paquetes NuGet instalado para Microsoft Visual Studio.
+ [Cuenta de almacenamiento de Azure][How To Create a Storage Account]
+ Realice el tutorial [Incorporación de Servicios móviles a una aplicación existente](mobile-services-windows-phone-get-started-data.md)  


##Instalación del cliente de almacenamiento para aplicaciones de Windows Phone

Para poder utilizar una SAS para cargar imágenes al almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de Windows Phone.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, seleccione **Incluir versión preliminar**, busque **WindowsAzure.Storage-Preview**, haga clic en **Instalar** en el paquete de **almacenamiento de Azure** y, a continuación, acepte los contratos de licencia.

  	![][2]

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

##Actualización del script de inserción registrado en el Portal de administración


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Para agregar propiedades nuevas al objeto TodoItem, debe tener habilitado el esquema dinámico en el servicio móvil. Cuando el esquema dinámico está habilitado, automáticamente se agregan columnas nuevas a la tabla TodoItem que se asignan a estas nuevas propiedades.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##Pasos siguientes

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

+ [Envío de correo electrónico desde servicios móviles con SendGrid]
 
  Aprenda a agregar la funcionalidad de correo electrónico a su Servicio móvil con el servicio de correo electrónico SendGrid. Este tema demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid.

+ [Programar trabajos de back-end en Servicios móviles]

  Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

+ [Referencia del script del servidor de servicios móviles]

  Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.
 
+ [Referencia conceptual de Servicios móviles con .NET]

  Obtenga más información sobre el uso de Servicios móviles con .NET.
  
 

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[Envío de correo electrónico desde servicios móviles con SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Programar trabajos de back-end en Servicios móviles]: /develop/mobile/tutorials/schedule-backend-tasks/
[Referencia del script del servidor de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: /manage/services/storage/how-to-create-a-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referencia conceptual de Servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[SDK de Windows Phone 8.0]: http://www.microsoft.com/download/details.aspx?id=35471



<!--HONumber=54--> 