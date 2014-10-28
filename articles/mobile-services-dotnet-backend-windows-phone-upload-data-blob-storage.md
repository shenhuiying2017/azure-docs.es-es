<properties linkId="mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de 5 minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles. Este tutorial le guiará a través de los siguientes pasos básicos para actualizar el inicio rápido de Servicios móviles para cargar imágenes al servicio de almacenamiento de blobs:

1.  [Instalación de la biblioteca de clientes de almacenamiento][]
2.  [Actualización de la aplicación cliente para capturar imágenes][]
3.  [Instalación del cliente de almacenamiento en el proyecto de servicio móvil][]
4.  [Actualización de la definición de TodoItem en el modelo de datos][]
5.  [Actualización del controlador de tablas para generar una SAS][]
6.  [Carga de imágenes para probar la aplicación][]

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2013 o una versión posterior
-   [SDK de Windows Phone 8.0][] o una versión posterior.
-   El Administrador de paquetes NuGet instalado para Microsoft Visual Studio.
-   [Cuenta de almacenamiento de Azure][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][].

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage][]]

## <a name="install-storage-client"></a>Instalación del cliente de almacenamiento para aplicaciones de la Tienda Windows

Para poder utilizar una SAS para cargar imágenes de su aplicación en el almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación cliente y luego seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **En línea** y después **Include Prerelease**, busque **WindowsAzure.Storage-Preview**, haga clic en **Instalar** en el paquete **Almacenamiento de Azure** y, a continuación, acepte el contrato de licencia.

    ![][]

    Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage][]]

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "C# para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Windows Phone"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage "Back-end de JavaScript"
  [Instalación de la biblioteca de clientes de almacenamiento]: #install-storage-client
  [Actualización de la aplicación cliente para capturar imágenes]: #add-select-images
  [Instalación del cliente de almacenamiento en el proyecto de servicio móvil]: #storage-client-server
  [Actualización de la definición de TodoItem en el modelo de datos]: #update-data-model
  [Actualización del controlador de tablas para generar una SAS]: #update-scripts
  [Carga de imágenes para probar la aplicación]: #test
  [SDK de Windows Phone 8.0]: http://www.microsoft.com/es-es/download/details.aspx?id=35471
  [Cuenta de almacenamiento de Azure]: /es-es/documentation/articles/storage-create-storage-account/
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
  [mobile-services-dotnet-backend-configure-blob-storage]: ../includes/mobile-services-dotnet-backend-configure-blob-storage.md
  []: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-windows-phone-upload-to-blob-storage]: ../includes/mobile-services-windows-phone-upload-to-blob-storage.md
