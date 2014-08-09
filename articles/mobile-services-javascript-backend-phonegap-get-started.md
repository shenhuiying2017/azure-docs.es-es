<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

[Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-get-started "Tienda Windows") [Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/es-es/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/es-es/documentation/articles/mobile-services-android-get-started "Android") [HTML](/es-es/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/es-es/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/es-es/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

Introducción a los Servicios móviles
====================================

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo.

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png)

### Requisitos adicionales

Completar este tutorial requiere las herramientas de PhoneGap (se requiere v3.2+ para los proyectos de Windows Phone 8).

PhoneGap admite el desarrollo para varias plataformas. Además de las propias herramientas de PhoneGap, debe instalar las herramientas para cada plataforma a la que se dirige:

-   Windows Phone: Instale [Visual Studio 2012 Express para Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
-   iOS: Instale [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) (se requiere v4.4+)
-   Android: Instale las [herramientas para desarrolladores de Android (en inglés)](https://go.microsoft.com/fwLink/p/?LinkID=280125)
    (El SDK de Servicios móviles para Android es compatible con aplicaciones para Android 2.2 o una versión posterior. Se requiere Android 4.2 o superior para ejecutar la aplicación de inicio rápido).

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Creación de una aplicación nuevaCreación de una aplicación nueva de PhoneGap
----------------------------------------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de PhoneGap que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **PhoneGap** bajo **Choose platform** y expanda **Create a new PhoneGap app**.

        ![][0]

        Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación PhoneGap conectada al servicio móvil.

![](./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png)

1.  Si aún no lo ha hecho, descargue e instale PhoneGap y al menos una de las herramientas de desarrollo de plataforma (Windows Phone, iOS o Android).

2.  Haga clic en **Create TodoItems table** para crear una tabla donde almacenar datos de la aplicación.

3.  En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

    De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil, además del SDK de JavaScript para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

Ejecución de la nueva aplicación de PhoneGap
--------------------------------------------

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo.

2.  Abra y ejecute el proyecto de acuerdo con las siguientes instrucciones para cada plataforma.

    -   **Windows Phone 8**

    1.  Windows Phone 8: Abra el archivo .sln de la carpeta **platforms\\wp8** en Visual Studio 2012 Express para Windows Phone.

    2.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png)

    -   **iOS**

    1.  Abra el proyecto de la carpeta **platforms/ios** en Xcode.

    2.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png)

    -   **Android**

        1.  En Eclipse, haga clic en **File**, **Import**, expanda **Android**, haga clic en **Existing Android Code into Workspace** y, por último, en **Next.**

        2.  Haga clic en **Browse**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **OK**, asegúrese de que el proyecto TodoActivity esté marcado y, a continuación, haga clic en **Finish**. &lt;p\>Esta acción importa los archivos de proyecto al área de trabajo actual.&lt;/p\>

        3.  Haga clic en la opción **Run** del menú **Run** para iniciar el proyecto en el emulador de Android.

            ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png)

        > [WACOM.NOTE]Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

3.  Después de iniciar la aplicación en uno de los emuladores móviles anteriormente, escriba algún texto en el cuadro de texto y, a continuación, haga clic en **Add**.

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla **TodoItem**. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    **Importante**

    Los cambios en este proyecto de plataforma se sobrescribirán si el proyecto principal se recompila con las herramientas de PhoneGap. En su lugar, realice cambios en el directorio www raíz del proyecto tal como se describe en la sección siguiente.

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)

Realización de actualizaciones de aplicaciones y recompilación de proyectos para cada plataforma
------------------------------------------------------------------------------------------------

1.  Realice cambios en los archivos de código en el directorio Â´wwwÂ´, que en este caso es Â´todolist/wwwÂ´.

2.  Compruebe que todas las herramientas de la plataforma de destino se encuentren accesibles en la ruta de acceso del sistema.

3.  Abra un símbolo del sistema en el directorio raíz del proyecto y ejecute uno de los siguientes comandos específicos de la plataforma:

    -   **Windows Phone**

        Ejecute el siguiente comando desde el símbolo del sistema de desarrollador para Visual Studio:

    phonegap local build wp8

    -   **iOS**

        Abra el terminal y ejecute el siguiente comando:

    phonegap local build ios

    -   **Android**

        Abra un símbolo del sistema o ventana de terminal y ejecute el siguiente comando.

              phonegap local build android

4.  Abra cada proyecto en el entorno de desarrollo adecuado como se describe en la sección anterior.

> [WACOM.NOTE]Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, que se encuentra en el archivo js/index.js.

Pasos siguientes
----------------

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a los datos](/es-es/documentation/articles/mobile-services-html-get-started-data)
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/es-es/documentation/articles/mobile-services-html-get-started-users)
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.


