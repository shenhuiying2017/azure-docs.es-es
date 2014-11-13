<properties pageTitle="Autorizaci&oacute;n en el servicio (Android) | Centro de desarrollo m&oacute;vil" metaKeywords="" description="Aprenda a autorizar a los usuarios en el back-end .NET de Servicios m&oacute;viles de Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Autorizaci&oacute;n en el servicio de usuarios de Servicios m&oacute;viles" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# Autorización en el servicio de usuarios de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

En este tema se muestra cómo autorizar a los usuarios autenticados el acceso a los datos de Servicios móviles de Azure desde una aplicación Android. En este tutorial, agregará código a los métodos de acceso a datos de su controlador para filtrar las consultas según el identificador de usuario de los usuarios autenticados, lo que garantiza que cada usuario solo pueda ver sus propios datos.

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a la autenticación][Introducción a la autenticación]. Antes de comenzar este tutorial, primero debe completar [Introducción a la autenticación][Introducción a la autenticación].

## <a name="register-scripts"></a>Modificación de los métodos de acceso a datos

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## Prueba de la aplicación

1.  En Eclipse, abra el proyecto que ha modificado al completar el tutorial [Introducción a la autenticación][Introducción a la autenticación].

2.  Haga clic en la opción **Run** del menú **Run** para iniciar el proyecto en el emulador de Android.

    Tenga en cuenta que esta vez, aunque existan elementos ya en la tabla TodoItem de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna userID y ahora cuentan con valores nulos.

3.  En la aplicación, escriba un texto en **Add a ToDo item** y, a continuación, haga clic en **Save**.

    ![][0]

    De esta forma, se inserta tanto el texto como el userId en la tabla TodoItem en el servicio móvil. Puesto que el nuevo elemento cuenta con un valor userID correcto, el servicio móvil lo devuelve y se muestra en la segunda columna.

4.  Vuelva a la tabla **todoitem** en el [Portal de administración de Azure], haga clic en **Browse** y compruebe que los elementos recién agregados dispongan ahora de un valor userId asociado.

5.  (Opcional) Si dispone de cuentas de inicio de sesión adicionales, puede comprobar que los usuarios pueden solo ver sus propios datos si cierran la aplicación y, a continuación, la vuelven a ejecutar. Cuando se muestre el cuadro de diálogo de credenciales de inicio de sesión, especifique un inicio de sesión distinto y, a continuación, compruebe que no se muestran los elementos especificados en la cuenta anterior.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de la autenticación. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con Android][Referencia conceptual de Servicios móviles con Android]
    Obtenga más información sobre cómo utilizar Servicios móviles con Android.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [0]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Referencia conceptual de Servicios móviles con Android]: /es-es/documentation/articles/mobile-services-android-how-to-use-client-library/
