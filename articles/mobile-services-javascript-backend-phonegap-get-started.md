<properties pageTitle="Introducción a los Servicios móviles con PhoneGap | Centro de desarrollo móvil" metaKeywords="" description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de PhoneGap para iOS, Android y Windows Phone." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>Introducción a los Servicios móviles

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación con los Servicios móviles de Azure. Con este tutorial, creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. 

La siguiente captura de pantalla muestra la aplicación final:

![][3]

### <a name="additional-requirements"></a>Requisitos adicionales

Completar este tutorial requiere las herramientas de PhoneGap (se requiere v3.2+ para los proyectos de Windows Phone 8). 

PhoneGap admite el desarrollo para varias plataformas. Además de las propias herramientas de PhoneGap, debe instalar las herramientas para cada plataforma a la que se dirige:

- Windows Phone: instale [Visual Studio 2012 Express para Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
- iOS: instale [Xcode] (se requiere v4.4+)
- Android: instale las [herramientas para desarrolladores de Android][Android SDK]
	<br/>(el SDK de Servicios móviles para Android es compatible con aplicaciones para Android 2.2 o una versión posterior. Se requiere Android 4.2 o superior para ejecutar la aplicación de inicio rápido).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Creación de una aplicación PhoneGap</h2>

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección, creará una aplicación de PhoneGap que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **PhoneGap** bajo **Elegir una plataforma** y expanda **Creación de una aplicación de PhoneGap**.

   	![][0]

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación PhoneGap conectada al servicio móvil.

  	![][1]

3. Si aún no lo ha hecho, descargue e instale PhoneGap y al menos una de las herramientas de desarrollo de plataforma (Windows Phone, iOS o Android).

4. Haga clic en **Crear tabla TodoItems** para crear una tabla donde almacenar datos de la aplicación.

5. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**. 

	De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil, además del SDK de JavaScript para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la nueva aplicación de PhoneGap

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva. 

1.	Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo. 

2.	Abra y ejecute el proyecto de acuerdo con las siguientes instrucciones para cada plataforma.

	+ **Windows Phone 8**

	1. Windows Phone 8: abra el archivo .sln de la carpeta **platforms\wp8** en Visual Studio 2012 Express para Windows Phone.
	
	2. Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.
	
	  	![][2]

	+ **iOS**

	1. Abra el proyecto de la carpeta **platforms/ios** en Xcode.
	
	2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador del iPhone, que es la solución predeterminada para este proyecto.
	
	  	![][3]

	+ **Android**

		1. En Eclipse, haga clic en **Archivo**, **Importar**, expanda **Android**, haga clic en **Código existente de Android en el área de trabajo** y, por último, haga clic en **Siguiente.** 
		
		2. Haga clic en **Examinar**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **Aceptar**, asegúrese de que el proyecto TodoActivity esté marcado y, a continuación, haga clic en **Finalizar**. <p>De esta forma se importan los archivos del proyecto en el área de trabajo actual.</p>
		
		3. Haga clic en la opción **Ejecutar**, haga clic en **Ejecutar** para iniciar el proyecto en el emulador de Android.
		
			![][4]
	
		>[WACOM.NOTE]Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.
			
	
3. Después de iniciar la aplicación en uno de los emuladores móviles anteriormente, escriba algún texto en el cuadro de texto y, a continuación, haga clic en **Agregar**.

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla **TodoItem**. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	<div class="dev-callout"><strong>Importante</strong> <p>Los cambios en este proyecto de plataforma se sobrescribirán si el proyecto principal se recompila con las herramientas de PhoneGap. En su lugar, realice cambios en el directorio www raíz del proyecto tal como se describe en la sección siguiente.</p></div>

4. De nuevo en el Portal de administración, haga clic en la pestaña <strong>Datos</strong> y, a continuación, en la tabla <strong>TodoItems</strong> .

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)
	

## Realización de actualizaciones de aplicaciones y recompilación de proyectos para cada plataforma

1. Realice cambios en los archivos de código en el directorio 'www', que en este caso es 'todolist/www'.

2. Compruebe que todas las herramientas de la plataforma de destino se encuentren accesibles en la ruta de acceso del sistema. 

2. Abra un símbolo del sistema en el directorio raíz del proyecto y ejecute uno de los siguientes comandos específicos de la plataforma: 

	+ **Windows Phone**

		Ejecute el siguiente comando desde el símbolo del sistema de desarrollador para Visual Studio:

    		phonegap local build wp8

	+ **iOS**
 
		Abra el terminal y ejecute el siguiente comando:

    		phonegap local build ios

	+ **Android**

		Abra un símbolo del sistema o ventana de terminal y ejecute el siguiente comando. 

		    phonegap local build android


4. Abra cada proyecto en el entorno de desarrollo adecuado como se describe en la sección anterior.

>[WACOM.NOTE]Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, que se encuentra en el archivo js/index.js.

## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.
  
<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Introducción a los datos]: /en-us/documentation/articles/mobile-services-html-get-started-data
[Introducción a la autenticación]: /en-us/documentation/articles/mobile-services-html-get-started-users
[Introducción a las notificaciones de inserción]: /en-us/develop/mobile/tutorials/mobile-services-html-get-started-push
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Portal de administración]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374


<!--HONumber=35.1-->
