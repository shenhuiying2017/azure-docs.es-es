<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles
==========================================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "C# para Tienda Windows")[Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage "Windows Phone")

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de 5 minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles. Este tutorial le guiará a través de los siguientes pasos básicos para actualizar el inicio rápido de Servicios móviles para cargar imágenes al servicio de almacenamiento de blobs:

1.  [Instalación de la biblioteca de clientes de almacenamiento](#install-storage-client)
2.  [Actualización del script de inserción para generar una SAS](#update-scripts)
3.  [Actualización de la aplicación cliente para capturar imágenes](#add-select-images)
4.  [Carga de imágenes para probar la aplicación](#test)

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows 8 o una versión superior
-   [Cuenta de almacenamiento de Azure](/es-es/manage/services/storage/how-to-create-a-storage-account)
-   Una cámara u otro dispositivo de captura de imágenes conectado a su equipo.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-windows-store-get-started).

Instalación del cliente de almacenamiento para aplicaciones de la Tienda Windows
--------------------------------------------------------------------------------

Para poder utilizar una SAS para cargar imágenes al almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic en el nombre del proyecto y, a continuación, seleccione **Manage NuGet Packages**.

2.  En el panel izquierdo, seleccione la categoría **En línea**, busque `WindowsAzure.Storage`, haga clic en **Instalar** en el paquete de **almacenamiento de Azure** y acepte los contratos de licencia.

	![](./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png) 
	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

Actualización del script de inserción registrado en el Portal de administración
-------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

Pasos siguientes
----------------

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

-   [Envío de correo electrónico desde Servicios móviles con SendGrid](/es-es/develop/mobile/tutorials/send-email-with-sendgrid/)

    Aprenda a agregar la funcionalidad de correo electrónico a su Servicio móvil con el servicio de correo electrónico SendGrid. Este tema demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid.

-   [Programación de trabajos de back-end en Servicios móviles](/es-es/documentation/articles/mobile-services-schedule-recurring-tasks)

    Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkId=262293)

    Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.

-   [Referencia conceptual de Servicios móviles con .NET](/es-es/develop/mobile/how-to-guides/work-with-net-client-library)

    Obtenga más información sobre el uso de Servicios móviles con .NET


