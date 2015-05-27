<properties 
	pageTitle="Uso de servicios móviles para cargar imágenes en el almacenamiento de blobs (Tienda Windows) | Servicios móviles" 
	description="Obtenga información acerca de cómo usar Servicios móviles para cargar imágenes en el almacenamiento de blobs de Azure y acceder a las imágenes desde su aplicación de la Tienda Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/22/2015" 
	ms.author="glenga"/>

# Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para permitir que su aplicación cargue y almacene imágenes generadas por el usuario en el almacenamiento de Azure. Servicios móviles utiliza una Base de datos SQL para almacenar datos. Sin embargo, los datos de objetos binarios grandes (BLOB) se almacenan de manera eficiente en el servicio de almacenamiento de blobs de Azure.

No puede distribuir de manera segura con la aplicación cliente las credenciales que se requieren para cargar con seguridad datos al servicio de almacenamiento de blobs. En lugar de eso, debe almacenar estas credenciales en su servicio móvil y usarlas para generar una firma de acceso compartido (SAS) que se utiliza para cargar una imagen nueva. Servicios móviles devuelve de manera segura a SAS, una credencial de expiración breve (en este caso de cinco minutos), a la aplicación cliente. Luego la aplicación utiliza esta credencial temporal para cargar la imagen. En este ejemplo, las descargas del servicio BLOB son públicas.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido de Servicios móviles para tomar fotografías y cargar las imágenes a Azure a través de una SAS generada por Servicios móviles. Este tutorial le guiará a través de los siguientes pasos básicos para actualizar el inicio rápido de Servicios móviles para cargar imágenes al servicio de almacenamiento de blobs:

1. [Instalación de la biblioteca de clientes de almacenamiento]
2. [Actualización del script de inserción para generar una SAS]
3. [Actualización de la aplicación cliente para capturar imágenes]
4. [Carga de imágenes para probar la aplicación]

Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2012 Express para Windows 8 o una versión posterior
+ [Cuenta de almacenamiento de Azure][How To Create a Storage Account]
+ Una cámara u otro dispositivo de captura de imágenes conectado a su equipo.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles].

##<a name="install-storage-client"></a>Instalación del cliente de Almacenamiento para aplicaciones de la Tienda Windows

Para poder utilizar una SAS para cargar imágenes al almacenamiento de blobs, primero debe agregar el paquete NuGet que instala la biblioteca de clientes de almacenamiento para aplicaciones de la Tienda Windows.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea**, busque `WindowsAzure.Storage`, haga clic en **Instalar** en el paquete de **Almacenamiento de Azure** y, a continuación, acepte los contratos de licencia.

  	![][2]

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto.

A continuación, actualizará la aplicación de inicio rápido para capturar y cargar imágenes.

##<a name="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha podido cargar de manera segura imágenes al integrar su servicio móvil con el servicio BLOB, revise algunos de los otros temas relacionados con la integración y el servicio back-end:

+ [Envío de correo electrónico desde servicios móviles con SendGrid]
 
  Aprenda a agregar la funcionalidad de correo electrónico a su Servicio móvil con el servicio de correo electrónico SendGrid. Este tema demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid.

+ [Programar trabajos de back-end en Servicios móviles]

  Aprenda a utilizar la funcionalidad del programador de trabajos de Servicios móviles para definir el código de script de servidor que se ejecuta según una programación que define usted.

+ [Referencia del script del servidor de servicios móviles]

  Temas de referencia para utilizar scripts de servidor con la finalidad de ejecutar tareas del lado servidor e integración con otros componentes de Azure y recursos externos.
 
+ [Referencia conceptual de Servicios móviles con .NET]

  Obtenga más información sobre el uso de Servicios móviles con .NET.
  
 
<!-- Anchors. -->
[Instalación de la biblioteca de clientes de almacenamiento]: #install-storage-client
[Actualización de la aplicación cliente para capturar imágenes]: #add-select-images
[Actualización del script de inserción para generar una SAS]: #update-scripts
[Carga de imágenes para probar la aplicación]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Envío de correo electrónico desde servicios móviles con SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Programar trabajos de back-end en Servicios móviles]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Referencia del script del servidor de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Introducción a los Servicios móviles]: mobile-services-windows-store-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: /manage/services/storage/how-to-create-a-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Referencia conceptual de Servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=54-->