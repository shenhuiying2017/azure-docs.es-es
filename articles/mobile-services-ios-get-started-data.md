<properties urlDisplayName="Get Started with Data" pageTitle="Introducción a los datos (iOS) | Centro de desarrollo móvil" metaKeywords="Azure iOS data, Azure mobile services data, " description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Incorporación de servicios móviles a una aplicación existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de iOS. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial <a href="/es-es/develop/mobile/tutorials/get-started-ios">Introducción a Servicios móviles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación de iOS]
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualizar la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

Este tutorial requiere lo siguiente:

+ [SDK de Servicios móviles para iOS] and [XCode 4.5][Instalación de Xcode] y iOS 5.0 o posterior.
+ Una cuenta de Microsoft Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Evaluación gratuita de Azure</a>.</p></div>

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][GitHub], que es una aplicación iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de iOS de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1. Descargue la [aplicación de ejemplo GetStartedWithData][GitHub].

2. En Xcode, abra el proyecto descargado y examine el archivo TodoService.m.

   	Tenga en cuenta que hay ocho comentarios **// TODO** que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

3. Presione el botón **Run** (Ejecutar), o Command + R, para volver a compilar el proyecto e iniciar la aplicación.

4. En la aplicación, escriba algo de texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

   	![][0]  

   	Observe que el texto guardado se muestra en la lista a continuación.

##<a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Adición de una tabla nueva al servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

1. En Xcode, seleccione un emulador en el que implementar (iPhone o iPad), presione el botón **Run** (Ejecutar), o Comando + R, para recompilar el proyecto e iniciar la aplicación.

   	Esta acción ejecuta su cliente de Servicios móviles de Azure, creado con el SDK de iOS, que consulta elementos desde su servicio móvil.

2. Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y elija su servicio móvil.

4. Haga clic en la pestaña **Datos** y después en **Examinar**.

   	![][9]

   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Con esto concluye el tutorial **Introducción a los datos** para iOS.

##<a name="next-steps"></a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts]
  <br/>Más información sobre el uso de scripts de servidor en servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos se controla en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

* [Introducción a la autenticación]
	<br/>Cómo autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Vea cómo enviar una notificación de inserción muy básica a su aplicación con Servicios móviles.

<!-- Anchors. -->
[Descarga del proyecto de la aplicación de iOS]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualizar la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-ios
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Instalación de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Repositorio de GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
