<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="wesmc" writer="wesmc" services="mobile-services,storage"  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Back-end de .NET" >Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de 5 minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles. Este tutorial le guiará a través de los siguientes pasos básicos para actualizar el inicio rápido de Servicios móviles para cargar imágenes al servicio de almacenamiento de blobs:

1.  [Instalación de la biblioteca de clientes de almacenamiento][Instalación de la biblioteca de clientes de almacenamiento]
2.  [Actualización del script de inserción para generar una SAS][Actualización del script de inserción para generar una SAS]
3.  [Actualización de la aplicación cliente para capturar imágenes][Actualización de la aplicación cliente para capturar imágenes]
4.  [Carga de imágenes para probar la aplicación][Carga de imágenes para probar la aplicación]

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows 8 o una versión posterior
-   [SDK de Windows Phone 8.0][SDK de Windows Phone 8.0] o una versión posterior.
-   El Administrador de paquetes NuGet instalado para Microsoft Visual Studio.
-   [Cuenta de almacenamiento de Azure][Cuenta de almacenamiento de Azure]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

## <a name="install-storage-client"></a>Instalación del cliente de almacenamiento para aplicaciones de Windows Phone

Para poder utilizar una SAS para cargar imágenes al almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de Windows Phone.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **En línea** y después **Include Prerelease**, busque **WindowsAzure.Storage-Preview**, haga clic en **Instalar** en el paquete **Almacenamiento de Azure** y, a continuación, acepte el contrato de licencia.

    ![][0]

    Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

## <a name="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

> [WACOM.NOTE]Para agregar propiedades nuevas al objeto TodoItem, debe tener habilitado el esquema dinámico en el servicio móvil. Cuando el esquema dinámico está habilitado, automáticamente se agregan columnas nuevas a la tabla TodoItem que se asignan a estas nuevas propiedades.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

-   [Envío de correo electrónico desde Servicios móviles con SendGrid][Envío de correo electrónico desde Servicios móviles con SendGrid]

Aprenda a agregar la funcionalidad de correo electrónico a su Servicio móvil con el servicio de correo electrónico SendGrid. Este tema demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid.

-   [Programación de trabajos de back-end en Servicios móviles][Programación de trabajos de back-end en Servicios móviles]

Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]

Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]

Obtenga más información sobre el uso de Servicios móviles con .NET.

 
 


  [Instalación de la biblioteca de clientes de almacenamiento]: #install-storage-client
  [Actualización del script de inserción para generar una SAS]: #update-scripts
  [Actualización de la aplicación cliente para capturar imágenes]: #add-select-images
  [Carga de imágenes para probar la aplicación]: #test
  [SDK de Windows Phone 8.0]: http://www.microsoft.com/es-es/download/details.aspx?id=35471
  [Cuenta de almacenamiento de Azure]: /es-es/manage/services/storage/how-to-create-a-storage-account
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [Envío de correo electrónico desde Servicios móviles con SendGrid]: /es-es/develop/mobile/tutorials/send-email-with-sendgrid/
  [Programación de trabajos de back-end en Servicios móviles]: /es-es/develop/mobile/tutorials/schedule-backend-tasks/
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
