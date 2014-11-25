<properties pageTitle="Get Started with Azure Mobile Services for Appcelerator Titanium" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Appcelerator development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator="" team;mahender" />

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial le mostrará cómo aprovechar los Servicios móviles de Azure en las aplicaciones con Appcelerator integrado.

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de tareas pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará usa JavaScript para la lógica de negocios de servidor.

Para completar este tutorial se necesita [Appcelerator Titanium][Appcelerator Titanium].

Si compila para iOS, también necesitará Xcode 5.1 y el SDK de iOS 7.1, o versiones posteriores.

Si compila para Android, también necesitará Android 4.3 o un SDK superior.

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación Appcelerator

1.  En el portal de Servicios móviles, seleccione la pestaña **Datos** para el servicio móvil.

2.  Haga clic en **Agregar una tabla** y cree una nueva tabla llamada **TodoItem**.

3.  Descargue una nueva [aplicación Lista de tareas pendientes][aplicación Lista de tareas pendientes] Appcelerator y descomprima el proyecto.

4.  Si todavía no lo tiene, descargue e instale [Appcelerator Titanium Studio][Appcelerator Titanium] y el SDK v3.2.1 o superior. También necesita Xcode (v 5.0 +) y/o el SDK de Android (v 4.3+) para ejecutar el proyecto.

5.  Vuelva al portal de Servicios móviles y, bajo **Panel**, seleccione **Administrar claves** y copie la información de **Clave de aplicación**.

6.  En el archivo index.js de la aplicación, reemplace `<---App Name---->` y `<------------APP KEY------------>` por los valores del servicio móvil.

## Ejecución de la nueva aplicación Appcelerator

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Abra Titanium Studio y vaya a **File -\> Import** para importar el proyecto que descargó anteriormente.

    ![](./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png)

2.  En la siguiente pantalla, seleccione **Existing Projects into Workspace** y haga clic en **Next**.

    ![][1]

3.  En la pantalla de selección de proyecto, use la opción Browse y busque el proyecto de Titanium para Azure que descargó del Portal de administración de Azure.

    ![][2]

4.  Por último, se mostrará el proyecto que ha seleccionado en la sección de proyectos. Opcionalmente, puede activar la opción "Copy projects into workspace", que copiará el proyecto descargado en el área de trabajo. Por último, haga clic en Finish para abrir el proyecto en Titanium Studio.

    ![][3]

5.  Seleccione en qué plataforma (iOS o Android) le interesa ejecutar el proyecto.

    ![][4]

6.  Presione el botón Run para compilar el proyecto e inicie la aplicación en el simulador de iPhone o en el emulador de Android.

7.  Realice la selección basándose en la configuración del Portal de administración de Azure.

    ![][5]

8.  En la pantalla siguiente, haga clic en el icono del signo más (+), escriba un texto significativo, como por ejemplo "Finalizar este tutorial", y haga clic en el botón Save.

    ![][6]

    ![][7]

Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Windows Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

![][8]

> [WACOM.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo TodoService.m.

1.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![][9]

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![][10]

## <a name="next-steps"> </a>Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

 

  [Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
  [aplicación Lista de tareas pendientes]: http://go.microsoft.com/fwlink/p/?LinkId=506859
  
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
  [7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
  [8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
  [9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
  [10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png
  [Introducción a la autenticación]: /es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push
