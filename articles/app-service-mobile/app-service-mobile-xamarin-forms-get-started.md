---
title: "Introducción a Mobile Apps mediante Xamarin.Forms"
description: Siga este tutorial para empezar a usar Mobile Apps para el desarrollo de Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 01a8e356649c070a5cf36ae070195abce699f85e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-xamarinforms-app"></a>Creación de una aplicación Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Información general
En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.Forms mediante la característica Mobile Apps de Azure App Service como back-end. Va a crear tanto un back-end de Mobile Apps nuevo como una aplicación Xamarin.Forms simple de la lista de tareas pendientes que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Apps para aplicaciones Xamarin.Forms.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para más información, consulte [Obtener una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Para más información, consulte la página [Configuración e instalación de Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Un equipo Mac con Xcode v7.0 o versiones posteriores y Xamarin Studio Community instalados. Para más información, consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) de Visual Studio y Xamarin, y [Configuración, instalación y comprobaciones para usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Creación de un back-end de Mobile Apps

Para crear un nuevo back-end de Mobile Apps, haga lo siguiente:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ya ha configurado un back-end de Mobile Apps que puede usar con las aplicaciones cliente móviles. Después, va a descargar un proyecto de servidor para un back-end de lista de tareas sencillo y lo publicará en Azure.

## <a name="configure-the-server-project"></a>Configuración del proyecto de servidor

Para configurar el proyecto de servidor para que use el back-end de Node.js o. NET, haga lo siguiente:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Descarga y ejecución de la solución Xamarin.Forms

Puede descargar la solución en cualquiera de estas dos maneras. Descárguela en un equipo Mac y ábrala en Xamarin Studio, o descárguela en un equipo Windows y ábrala en Visual Studio usando un equipo Mac en red para crear la aplicación iOS. Para más información, consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) de Visual Studio y Xamarin.

En un equipo Mac o Windows, haga lo siguiente:

1. Vaya a [Azure Portal].

2. En la hoja Configuración de la aplicación móvil, haga clic en **Inicio rápido** (en Implementación) > **Xamarin.Forms**. En el paso 3, haga clic en **Crear una nueva aplicación** en caso de que no esté seleccionado.  A continuación, haga clic en el botón **Descargar** .

   Esta acción permite descargar un proyecto que contiene la aplicación cliente que está conectada a la aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

3. Extraiga el proyecto descargado y, después, ábralo en Xamarin Studio (Mac) o Visual Studio (Windows).

   ![Proyecto extraído en Xamarin Studio][9]

   ![Proyecto extraído en Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Opcional) Ejecución del proyecto de iOS
En esta sección va a ejecutar el proyecto de iOS de Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

#### <a name="in-xamarin-studio"></a>En Xamarin Studio
1. Haga clic con el botón derecho en el proyecto de iOS y, después, seleccione **Establecer como proyecto de inicio**.

2. En el menú **Ejecutar**, seleccione **Iniciar depuración** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

#### <a name="in-visual-studio"></a>En Visual Studio
1. Haga clic con el botón derecho en el proyecto de iOS y, después, seleccione **Establecer como proyecto de inicio**.

2. En el menú **Compilar**, seleccione **Administrador de configuración**.

3. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** junto al proyecto de iOS.

4. Para compilar el proyecto e iniciar la aplicación en el emulador de iPhone, seleccione la tecla **F5**.

   > [!NOTE]
   > Si tiene problemas al compilar el proyecto, ejecute el Administrador de paquetes NuGet y actualice a la versión más reciente de los paquetes de soporte de Xamarin. Los proyectos de la guía de inicio rápido pueden tardar en actualizarse a las versiones más recientes.    
   >
   >

5. En la aplicación, escriba un texto significativo, como *Aprender Xamarin*, y seleccione el botón del signo más (**+**).

    ![][10]

    Esta acción envía una solicitud POST al nuevo back-end Mobile Apps hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de Mobile Apps devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    > [!NOTE]
    > Encontrará el código con el que se accede al back-end de Mobile Apps en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.
    >
    >

## <a name="optional-run-the-android-project"></a>(Opcional) Ejecución del proyecto de Android
Esta sección va a ejecutar el proyecto droid de Xamarin para Android. Puede omitir esta sección si no está trabajando con dispositivos Android.

#### <a name="in-xamarin-studio"></a>En Xamarin Studio

1. Haga clic con el botón derecho en el proyecto de Android y, después, seleccione **Establecer como proyecto de inicio**.

2. Para compilar el proyecto e iniciar la aplicación en un emulador de Android, en el menú **Ejecutar**, seleccione **Iniciar depuración**.

#### <a name="in-visual-studio"></a>En Visual Studio

1. Haga clic con el botón derecho en el proyecto de Android (Droid) y, después, seleccione **Establecer como proyecto de inicio**.

2. En el menú **Compilar**, seleccione **Administrador de configuración**.

3. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** junto al proyecto de Android.

4. Para compilar el proyecto e iniciar la aplicación en un emulador de Android, presione la tecla **F5**.

   > [!NOTE]
   > Si tiene problemas al compilar el proyecto, ejecute el Administrador de paquetes NuGet y actualice a la versión más reciente de los paquetes de soporte de Xamarin. Los proyectos de la guía de inicio rápido pueden tardar en actualizarse a las versiones más recientes.    
   >
   >

5. En la aplicación, escriba un texto significativo, como *Aprender Xamarin*, y seleccione el botón del signo más (**+**).

    ![][11]
    
    Esta acción envía una solicitud POST al nuevo back-end Mobile Apps hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de Mobile Apps devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.
    
    > [!NOTE]
    > Encontrará el código con el que se accede al back-end de Mobile Apps en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Opcional) Ejecución del proyecto de Windows

En esta sección va a ejecutar el proyecto de WinApp de Xamarin para dispositivos Windows. Puede omitir esta sección si no está trabajando con dispositivos Windows.

#### <a name="in-visual-studio"></a>En Visual Studio

1. Haga clic con el botón derecho en cualquiera de los proyectos de Windows y, después, seleccione **Establecer como proyecto de inicio**.

2. En el menú **Compilar**, seleccione **Administrador de configuración**.

3. En el cuadro de diálogo **Administrador de configuración**, active las casillas **Compilar** e **Implementar** junto al proyecto de Windows que haya elegido.

4. Para compilar el proyecto e iniciar la aplicación en un emulador de Windows, presione la tecla **F5**.

   > [!NOTE]
   > Si tiene problemas al compilar el proyecto, ejecute el Administrador de paquetes NuGet y actualice a la versión más reciente de los paquetes de soporte de Xamarin. Los proyectos de la guía de inicio rápido pueden tardar en actualizarse a las versiones más recientes.    
   >
   >

5. En la aplicación, escriba un texto significativo, como *Aprender Xamarin*, y seleccione el botón del signo más (**+**).

    Esta acción envía una solicitud POST al nuevo back-end Mobile Apps hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de Mobile Apps devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.
    
    ![][12]
    
    > [!NOTE]
    > Encontrará el código con el que se accede al back-end de Mobile Apps en el archivo de C# TodoItemManager.cs del proyecto de biblioteca de clases portable de la solución.
    >
    >

## <a name="next-steps"></a>pasos siguientes

* [Incorporación de autenticación a la aplicación](app-service-mobile-xamarin-forms-get-started-users.md)  
  Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Agregar notificaciones push a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md)  
  Aprenda a agregar a la aplicación compatibilidad con notificaciones push y a configurar su back-end de Mobile Apps para usar Azure Notification Hubs para enviar notificaciones push.

* [Habilitación de la sincronización sin conexión para su aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Aprenda a agregar compatibilidad sin conexión para la aplicación mediante un back-end de Mobile Apps. Con la sincronización sin conexión, podrá ver, agregar o modificar los datos en la aplicación móvil, incluso cuando no hay ninguna conexión de red.

* [Uso del cliente administrado para Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
  : aprenda a trabajar con el SDK de cliente administrado en la aplicación Xamarin.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
