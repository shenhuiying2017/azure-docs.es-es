<properties 
	pageTitle="Introducción a la notificación de inserción usando un servicio móvil de back-end de .NET" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure y centros de notificaciones para enviar notificaciones de inserción a la aplicación universal de Windows." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="glenga"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación universal para Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, el servicio móvil enviará una notificación de inserción desde el back-end de .NET a todas las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone, siempre que se inserte un registro en la tabla TodoList. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[AZURE.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para agregar la compatibilidad con las notificaciones de inserción de Servicios móviles para una aplicación universal para Windows. Se pueden seguir estos mismos pasos para agregar notificaciones de inserción de Servicios móviles a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para agregar notificaciones de inserción a una aplicación de Windows Phone 8 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a las notificaciones de inserción en servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

> Si no se puede actualizar a Visual Studio Professional 2013 Update 3 o si prefiere manualmente, agregar el proyecto de servicio móvil a una solución de aplicación de tienda Windows, vea [esta versión](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) del tema.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de una aplicación para notificaciones de inserción](#register)
2. [Actualización del servicio para enviar notificaciones de inserción](#update-service)
3. [Habilitación de notificaciones de inserción para pruebas locales](#local-testing)
4. [Prueba de las notificaciones de inserción en su aplicación](#test)

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 o una versión posterior). <br/>Hay disponible una versión de prueba gratuita. 

##<a id="register"></a>Registro de una aplicación para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Vaya a la <code>\Services\MobileServices\your_service_name</code> carpeta de proyecto, abra el archivo de código push.register.cs generado e inspeccione el método <strong>UploadChannel</strong> que registra la URL de canal del dispositivo en el Centro de notificaciones.</p></li> 
<li><p>Abra el archivo de código compartido App.xaml.cs y observe que se ha agregado una llamada al nuevo método <strong>UploadChannel</strong> en el controlador de eventos <strong>OnLaunched</strong>.</p> <p>Así se garantiza que se intentará registrar el dispositivo siempre que se inicie la aplicación.</p></li>
<li><p>Repita los pasos anteriores para agregar notificaciones de inserción al proyecto de aplicación de tienda de Windows Phone, a continuación, en el archivo compartido App.xaml.cs, quite la llamada adicional a <strong>UploadChannel</strong> y el <code>#if...#endif</code> contenedor condicional restante.</p> <p>Ambos proyectos pueden compartir ahora una sola llamada a <strong>UploadChannel</strong>.</p>

> [AZURE.NOTE] También puede simplificar el código generado unificando las definiciones de <code>#if...#endif</code> [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) ajustadas en una única definición sin ajustar usada por ambas versiones de la aplicación.

</li>
</ol>

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas. 

##<a id="update-service"></a>Actualización del servicio para enviar notificaciones de inserción

Los pasos siguientes actualizan la clase TodoItemController existente para enviar una notificación de inserción a todos los dispositivos registrados cuando se inserta un elemento nuevo. Puede implementar código similar en cualquier elemento [ApiController], [TableController] personalizado, o en cualquier otra parte de los servicios back-end. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Los pasos pendientes de esta sección son opcionales. Permiten probar la aplicación con un servicio móvil que se ejecuta en el equipo local. Si quiere probar las notificaciones de inserción con el servicio móvil que se ejecuta en Azure, vaya directamente a la última sección. Esto se debe a que el Asistente para agregar notificaciones de inserción ya ha configurado la aplicación para conectar con el servicio que se ejecuta en Azure. 

>[AZURE.NOTE]Nunca use un servicio móvil de producción para probar y desarrollar el trabajo. Para probarlo, publique siempre el proyecto de servicio móvil en un servicio de almacenamiento provisional independiente.

<ol start="5">
<li><p>Abra el archivo de proyecto App.xaml.cs compartido y busque las líneas de código que crean una nueva instancia de la clase <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> para obtener acceso al servicio móvil que se ejecuta en Azure.</p></li>
<li><p>Convierta este código en comentario y agregue el código necesario para crear una clase <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> nueva con el mismo nombre, pero con la URL del host local en el constructor. Algo similar a lo siguiente:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>Con esta clase <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, la aplicación conectará con el servicio local en lugar de con la versión que se hospeda en Azure. Para volver y ejecutar la aplicación con el servicio móvil que se hospeda en Azure, cambie de nuevo a las definiciones de <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> originales.</p></li>
</ol>

##<a id="test"></a> Prueba de las notificaciones de inserción en su aplicación

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos presentado las nociones para habilitar una aplicación de la Tienda Windows para que envíe notificaciones de inserción con Servicios móviles y Centros de notificaciones. Le recomendamos que después complete el tutorial [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo enviar estas notificaciones con etiquetas desde un servicio móvil a un único usuario autenticado.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Uso de un cliente .NET para Servicios móviles de Azure]
  <br/>Obtenga más información acerca de cómo utilizar Servicios móviles desde aplicaciones de Windows en C#.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/

[Uso de un cliente .NET para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/


<!--HONumber=42-->
