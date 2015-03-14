<properties 
	pageTitle="Introducción a las notificaciones de inserción con un servicio móvil de back-end de JavaScript" 
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
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/15/2014" 
	ms.author="glenga"/>


# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo usar Servicios móviles de Azure con un back-end de JavaScript para enviar notificaciones de inserción a una aplicación universal de Windows. Aprenderá a habilitar las notificaciones de inserción con los Centros de notificaciones de Azure en un proyecto de aplicación universal para Windows. Cuando termine, cada vez que se inserte un registro en la tabla TodoList, el servicio móvil enviará una notificación de inserción desde el back-end de JavaScript a todas las aplicaciones registradas en la Tienda Windows y la Tienda de Windows Phone. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

>[AZURE.NOTE]En este tema se muestra cómo usar las herramientas de Visual Studio 2013 Update 3 para agregar compatibilidad con las notificaciones de inserción de Servicios móviles a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para agregar notificaciones de inserción de Servicios móviles a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Si no puede actualizar a Visual Studio 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) del tema.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Registro de la aplicación para las notificaciones de inserción](#register)
2. [Actualización del servicio para enviar notificaciones de inserción](#update-service)
3. [Prueba de las notificaciones de inserción en su aplicación](#test)

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) activa.
* [Visual Studio 2013 Express para Windows](http://go.microsoft.com/fwlink/?LinkId=257546) con Update 3 o una versión posterior. 

##<a id="register"></a>Registro de la aplicación para notificaciones de inserción

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Vaya a la carpeta de proyecto <code>\services\mobileServices\scripts</code>, copie el archivo de script &lt;<em>nombre_del_servicio</em>&gt;>.push.register.js  push.register.js generado en la carpeta compartida <code>\js</code> y elimínelo de los proyectos de aplicación individuales de Windows y Windows Phone.</p></li> 
<li><p>Abra el archivo de script en la carpeta de proyecto compartida <code>\js</code>, busque el código en la escucha de eventos <em>activated</em> que registra la URL de canal del dispositivo con el Centro de notificaciones y elimine la función de la promesa <strong>done</strong>.</p>
<p>En este tutorial se envían notificaciones cuando se inserta un elemento nuevo y no cuando se llama a una API personalizada.</p></li>
<li><p>En el proyecto de aplicación de Windows, abra el archivo default.html y cambie la ruta de acceso de la referencia del archivo de script por la carpeta de proyecto compartida, <code>\js</code> de modo que se parezca a la siguiente:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Repita este paso para el proyecto de aplicación de Windows Phone.</p>
<p>Ahora, los dos proyectos usan una versión compartida del script de registro de inserción.</p></li>
</ol>

Tras habilitar las notificaciones de inserción en la aplicación, actualice el servicio móvil para enviarlas. 

##<a id="update-service"></a>Actualización del servicio para enviar notificaciones de inserción

Los pasos siguientes sirven para actualizar el script de inserción registrado para la tabla TodoItem. Puede implementar código similar en cualquier script del servidor o ubicación de los servicios back-end. 

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>Prueba de las notificaciones de inserción en su aplicación

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se han mostrado los aspectos básicos en la habilitación de aplicaciones de la Tienda Windows para que envíen notificaciones de inserción mediante Servicios móviles y Centros de notificaciones. Le recomendamos que después realice el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados], que muestra cómo utilizar etiquetas para enviar notificaciones de inserción desde un Servicio móvil a solo un usuario autenticado.

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]
  <br/>Obtenga más información acerca de cómo utilizar Servicios móviles desde aplicaciones HTML y JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/

[Uso de un cliente HTML/JavaScript para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
