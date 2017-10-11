---
title: "Problema en la configuración del inicio de sesión único con contraseña para una aplicación ajena a la galería | Microsoft Docs"
description: "Comprender los problemas más comunes a los que se enfrentan los usuarios al configurar un inicio de sesión único con contraseña para las aplicaciones ajenas a la galería que no figuran en la Galería de aplicaciones de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9c76b6f3495e2dd759a156fcef97b57aece8d632
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problema en la configuración del inicio de sesión único con contraseña para una aplicación ajena a la galería

Este artículo le ayuda a conocer los problemas habituales a los que se enfrentan los usuarios al configurar un **inicio de sesión único con contraseña** con una aplicación ajena a la galería.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Captura de campos de inicio de sesión para una aplicación

La captura de campos de inicio de sesión solo es posible en páginas de inicio de sesión compatibles con HTML y **no se admite para páginas de inicio de sesión no estándar**, como aquellas que usan Flash u otras tecnologías incompatibles con HTML.

Existen dos maneras de capturar campos de inicio de sesión para aplicaciones personalizadas:

-   Captura automática de campos de inicio de sesión

-   Captura manual de campos de inicio de sesión

La **captura automática de campos de inicio de sesión** funciona bien con la mayoría de las páginas de inicio de sesión compatibles con HTML si usan **identificadores DIV conocidos para el campo de entrada de nombre de usuario y contraseña**. Este procedimiento realiza un barrido del HTML en la página para encontrar identificadores DIV que cumplen determinados criterios y, a continuación, guarda esos metadatos para esta aplicación, de forma que las contraseñas puedan reproducirse en ella posteriormente.

La **captura manual de campos de inicio de sesión** puede utilizarse en el caso de que el **proveedor de la aplicación no etiquete** los campos de entrada que se utilizan para iniciar sesión. La captura manual de campos de inicio de sesión también puede utilizarse en el caso de que el **proveedor presente varios campos** que no puedan detectarse automáticamente. Azure AD puede almacenar datos de tantos campos como haya en la página de inicio de sesión, siempre que se indique dónde se encuentran esos campos en la página.

Por lo general, **si la captura automática de campos de inicio de sesión no funciona, se recomienda siempre probar la opción manual.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Captura automática de campos de inicio de sesión para una aplicación

Para configurar **Inicio de sesión único basado en contraseña** para una aplicación con la **captura automática de campos de inicio de sesión**, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Seleccione el modo de **Inicio de sesión con contraseña**.

9.  Escriba la **dirección URL de inicio de sesión**. Es la dirección URL en la que los usuarios escriben su nombre de usuario y contraseña para iniciar sesión. **Asegúrese de que los campos de inicio de sesión estén visibles en la dirección URL que proporcione**.

10. Haga clic en el botón **Save** (Guardar).

11. Una vez hecho esto, se cargará automáticamente esa dirección URL con un cuadro de entrada de un nombre de usuario y una contraseña y se le permitirá usar Azure AD para transmitir de forma segura las contraseñas de esa aplicación mediante la extensión de explorador del panel de acceso.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Captura manual de campos de inicio de sesión para una aplicación

Para capturar manualmente los campos de inicio de sesión, primero debe tener instalada la extensión del explorador del panel de acceso y **no utilizar el modo InPrivate, incógnito o privado.** Para instalar la extensión del explorador, siga los pasos descritos en la sección [Cómo instalar la extensión de explorador del Panel de acceso](#i-cannot-manually-detect-sign-in-fields-for-my-application).
.

Para configurar **Inicio de sesión único basado en contraseña** para una aplicación con la **captura manual de campos de inicio de sesión**, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Seleccione el modo de **Inicio de sesión con contraseña**.

9.  Escriba la **dirección URL de inicio de sesión**. Es la dirección URL en la que los usuarios escriben su nombre de usuario y contraseña para iniciar sesión. **Asegúrese de que los campos de inicio de sesión estén visibles en la dirección URL que proporcione**.

10. Haga clic en el botón **Save** (Guardar).

11. Una vez hecho esto, se cargará automáticamente esa dirección URL con un cuadro de entrada de un nombre de usuario y una contraseña y se le permitirá usar Azure AD para transmitir de forma segura las contraseñas de esa aplicación mediante la extensión de explorador del panel de acceso. En el caso de que se produzca un error, puede **cambiar el modo de inicio de sesión para usar la captura manual de campos de inicio de sesión** siguiendo con el paso 12.

12. Haga clic en **Establecer configuración de inicio de sesión único con contraseña de &lt;nombre de la aplicación&gt;**.

13. Seleccione la opción de configuración **Detectar campos de inicio de sesión manualmente**.

14. Haga clic en **Aceptar**.

15. Haga clic en **Guardar**.

16. Siga las instrucciones en pantalla para usar el panel de acceso.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Error "No pudimos encontrar ningún campo de inicio de sesión en esa URL"

Este error aparece cuando no funciona la detección automática de campos de inicio de sesión. Para resolver este problema, pruebe la detección manual de campos de inicio de sesión mediante los pasos descritos en la sección [Captura manual de campos de inicio de sesión para una aplicación](#how-to-manually-capture-sign-in-fields-for-an-application).

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Error "No se puede guardar la configuración del inicio de sesión único"

En algunos casos poco frecuentes, puede producirse un error al actualizar la configuración del inicio de sesión único. Para resolver este problema, intente guardar la configuración del inicio de sesión único de nuevo.

Si el problema persiste, abra un caso de soporte técnico y proporcione la información recopilada en las secciones [Visualización de los detalles de una notificación del portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) y [Obtención de ayuda mediante el envío de detalles de la notificación a un ingeniero de soporte técnico](#how-to-get-help-by-sending-notification-details-to-a-support-engineer).

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>No se detectan manualmente campos de inicio de sesión para mi aplicación

Entre los comportamientos que pueden observarse cuando la detección manual no funciona se incluyen los siguientes:

-   El proceso de captura manual parece haber funcionado, pero los campos capturados no son correctos.

-   No se resaltan los campos correspondientes al realizar el proceso de captura.

-   El proceso de captura me dirige a la página de inicio de sesión de la aplicación según lo previsto, pero no ocurre nada.

-   La captura manual parece funcionar, pero el SSO no se realiza cuando los usuarios acceden a la aplicación desde el panel de acceso.

Compruebe lo siguiente si se produce alguno de estos problemas:

-   Asegúrese de tener **instalada** y **habilitada** la versión más reciente de la extensión del explorador del panel de acceso, para lo que debe seguir los pasos descritos en la sección [Cómo instalar la extensión de explorador del Panel de acceso](#how-to-install-the-access-panel-browser-extension).

-   Asegúrese de que el explorador no se encuentre en los modos **InPrivate, incógnito o privado** mientras se realiza el proceso de captura. La extensión del panel de acceso no es compatible con estos modos.

-   Asegúrese de que los usuarios no intentan iniciar sesión en la aplicación desde el panel de acceso en **los modos incógnito, InPrivate o privado**. La extensión del panel de acceso no es compatible con estos modos.

-   Intente realizar el proceso de captura manual de nuevo, asegurándose de que los marcadores rojos se encuentren sobre los campos correctos.

-   Si el proceso de captura manual parece no responder o la página de inicio de sesión no hace nada (caso 3 anterior), intente realizar el proceso de captura manual de nuevo. Sin embargo, esta vez, tras completar el proceso, presione el botón **F12** para abrir la consola del desarrollador del explorador. Una vez allí, abra la **consola** y escriba **window.location=”&lt;escriba la dirección URL de inicio de sesión especificada al configurar la aplicación&gt;”** y, a continuación, presione **Entrar**. De este modo, se forzará una redirección de página que finalizará el proceso de captura y almacenará los campos que se han capturado.

Si ninguno de estos métodos funciona, podemos ayudarle. Abra un caso de soporte técnico en el que explique todo lo que ha intentado, además de proporcionar la información recopilada en las secciones [Visualización de los detalles de una notificación del portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) y [Obtención de ayuda mediante el envío de detalles de la notificación a un ingeniero de soporte técnico](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) (si corresponde).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Cómo instalar la extensión de explorador del Panel de acceso

Para instalar la extensión de explorador del Panel de acceso, siga estos pasos:

1.  Abra el [Panel de acceso](https://myapps.microsoft.com) en uno de los exploradores admitidos e inicie sesión como **usuario** en su instancia de Azure AD.

2.  Haga clic en una **aplicación de SSO con contraseña** en el Panel de acceso.

3.  En el mensaje que le pregunta si desea instalar el software, seleccione **Instalar ahora**.

4.  Se le dirigirá al vínculo de descarga en función del explorador. **Agregue** la extensión al explorador.

5.  Si el explorador lo solicita, seleccione **Habilitar** o **Permitir** la extensión.

6.  Una vez instalada, **reinicie** la sesión del explorador.

7.  Inicie sesión en el panel de acceso y vea si puede **iniciar** las aplicaciones de SSO con contraseña.

También puede descargar la extensión para Chrome y Firefox desde los siguientes vínculos directos:

-   [Extensión del Panel de acceso para Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensión del panel de acceso para Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Visualización de los detalles de una notificación del portal

Puede ver los detalles de cualquier notificación del portal si sigue los pasos siguientes:

1.  Haga clic en el icono de **notificaciones** (la campana) de la esquina superior derecha de Azure Portal.

2.  Seleccione cualquier notificación con un estado de **Error** (aquellas con un icono (!) de color rojo situado junto a ellas).

  >[NOTA] No puede hacer clic en notificaciones con un estado **Correcto** o **En curso**.
  >
  >

3.  Esto hace que se abra la hoja **Detalles de la notificación**.

4.  Utilice esta información para conocer más detalles acerca del problema.

5.  Si aún necesita ayuda, también puede compartir esta información con un ingeniero de soporte técnico o el grupo de producto para obtener ayuda.

6.  Haga clic en el **icono** de **copia** situado a la derecha del cuadro de texto **Copiar error** para copiar todos los detalles de la notificación para compartirlos con un ingeniero de soporte técnico o un grupo de producto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Obtención de ayuda mediante el envío de detalles de la notificación a un ingeniero de soporte técnico

Es muy importante que comparta **todos los detalles que se muestran a continuación** con un ingeniero de soporte técnico si necesita ayuda, para que pueda ayudarle rápidamente. Puede hacer esto fácilmente **realizando una captura de pantalla** o haciendo clic en el **icono Copiar error**, que se encuentra a la derecha del cuadro de texto **Copiar error**.

## <a name="notification-details-explained"></a>Explicación de los detalles de la notificación

A continuación se explica más en profundidad lo que significa cada uno de los elementos de notificación y se dan ejemplos de cada uno de ellos.

### <a name="essential-notification-items"></a>Elementos esenciales de notificación

-   **Título**: el título descriptivo de la notificación

    -   Por ejemplo: **Configuración del proxy de aplicación**

-   **Descripción**: la descripción de lo que se produjo como resultado de la operación

    -   Por ejemplo: **la dirección url interna especificada ya se está usando en otra aplicación**

-   **Identificador de notificación**: el identificador único de la notificación

    -   Por ejemplo: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Identificador de solicitud de cliente**: el identificador de solicitud específico generado por el explorador

    -   Por ejemplo: **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Marca de tiempo UTC**: la marca de tiempo durante la que tuvo lugar la notificación, en formato UTC

    -   Por ejemplo: **2017-03-23T19:50:43.7583681Z**

-   **Identificador de transacción interno**: el identificador interno que podemos utilizar para buscar el error en nuestros sistemas

    -   Por ejemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN**: el usuario que realizó la operación

    -   Por ejemplo: **tperkins@f128.info**

-   **Identificador de inquilino**: el identificador único del inquilino del que formaba parte el usuario que realizó la operación

    -   Por ejemplo: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Identificador de objeto de usuario**: el identificador único del usuario que realizó la operación

    -   Por ejemplo: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementos detallados de notificación

-   **Nombre para mostrar**: **(puede estar vacío)** un nombre para mostrar más detallado del error

    -   Por ejemplo*: **Configuración del proxy de aplicación**

-   **Estado**: el estado específico de la notificación

    -   Por ejemplo*: **Error**

-   **Identificador de objeto**: **(puede estar vacío)** el identificador del objeto en el que se realizó la operación

    -   Por ejemplo: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalles**: la descripción detallada de lo que se produjo como resultado de la operación

    -   Por ejemplo: **la dirección url interna 'http://bing.com/' no es válida puesto que ya está en uso**

-   **Copiar error**: haga clic en el **icono de copia** situado a la derecha del cuadro de texto **Copiar error** para copiar todos los detalles de la notificación para compartirlos con un ingeniero de soporte técnico o un grupo de producto

    -   Por ejemplo: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)

