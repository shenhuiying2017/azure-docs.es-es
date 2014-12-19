<properties pageTitle="Uso de Servicios móviles para cargar imágenes en el almacenamiento de blobs (Tienda Windows) | Servicios móviles" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure. 

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de 5 minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles. Este tutorial le guiará a través de los siguientes pasos básicos para actualizar el inicio rápido de Servicios móviles para cargar imágenes al servicio de almacenamiento de blobs:

1. [Instalación de la biblioteca de clientes de almacenamiento]
2. [Actualización de la aplicación cliente para capturar imágenes]
3. [Instalación del cliente de almacenamiento en el proyecto de servicio móvil]
4. [Actualización de la definición de TodoItem en el modelo de datos]
5. [Actualización del controlador de tablas para generar una SAS]
6. [Carga de imágenes para probar la aplicación]

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2013 o una versión posterior.
+ El Administrador de paquetes NuGet instalado para Microsoft Visual Studio.
+ [Cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a Servicios móviles]. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Instalación del cliente de almacenamiento para aplicaciones de la Tienda Windows

Para poder utilizar una SAS para cargar imágenes de su aplicación en el almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación cliente y luego seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea** y después **Incluir versión preliminar**, busque **WindowsAzure.Storage-Preview**, haga clic en **Instalar** en el paquete **Almacenamiento de Azure** y, a continuación, acepte el contrato de licencia. 

  	![][2]

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Instalación de la biblioteca de clientes de almacenamiento]: #install-storage-client
[Actualización de la aplicación cliente para capturar imágenes]: #add-select-images
[Instalación del cliente de almacenamiento en el proyecto de servicio móvil]: #storage-client-server
[Actualización de la definición de TodoItem en el modelo de datos]: #update-data-model
[Actualización del controlador de tablas para generar una SAS]: #update-scripts
[Carga de imágenes para probar la aplicación]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Envío de correo electrónico desde Servicios móviles con SendGrid]: /es-es/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Programación de trabajos de back-end en Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Creación de una cuenta de almacenamiento]: /es-es/documentation/articles/storage-create-storage-account/
[Biblioteca de clientes de almacenamiento de Azure para aplicaciones de la Tienda]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[SDK de Windows Phone 8.0]: http://www.microsoft.com/es-es/download/details.aspx?id=35471


