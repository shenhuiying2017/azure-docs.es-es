---
title: "Autenticación con las API de REST de Mobile Engagement: configuración manual"
description: "Describe cómo configurar manualmente la autenticación para las API de REST de Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticación con las API de REST de Mobile Engagement: configuración manual
Esta es una documentación de apéndice de [Autenticación con las API de REST de Mobile Engagement](mobile-engagement-api-authentication.md). Asegúrese de leerla primero para obtener el contexto.
Describe una forma alternativa de realizar una única configuración para establecer la autenticación para las API de REST de Mobile Engagement mediante Azure Portal.

> [!NOTE]
> Las instrucciones siguientes se basan en esta [guía de Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) y se personalizan según lo necesario para la autenticación para las API de Mobile Engagement. Así que consúltela si desea conocer los pasos siguientes en detalle.

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com/).
2. Seleccione **Active Directory** en el panel izquierdo.

     ![seleccionar Active Directory][1]

3. Para ver las aplicaciones en su directorio, haga clic en **Registros de aplicaciones**.

     ![ver aplicaciones][3]

4. Haga clic en **Nuevo registro de aplicaciones**.

     ![Agregar una aplicación][4]

5. Rellene el nombre de la aplicación y deje el tipo de aplicación como **Aplicación web o API web** y haga clic en el botón Siguiente. Puede proporcionar cualquier dirección URL ficticia en **DIRECCIÓN URL DE INICIO DE SESIÓN**, porque no se usan para este escenario y las direcciones URL mismas no se validan.
6. Una vez que lo haga, tendrá una aplicación de Azure AD con el nombre que proporcionó. Es su **AD\_APP\_NAME** (anótelo).

     ![nombre de la aplicación][8]

7. Haga clic en el nombre de la aplicación.
8. Busque el **identificador de aplicación** y anótelo, porque será el identificador de cliente que se usará como **CLIENT\_ID** para las llamadas de API.

     ![configurar aplicación ][10]

9. Busque la sección **Claves** que se encuentra a la derecha.

     ![configurar aplicación ][11]

10. Cree una clave nueva y cópiela y guárdela de inmediato para poder usarla. No se volverá a mostrar.

     ![configurar aplicación ][12]

    > [!IMPORTANT]
    > Esta clave expira al final de la duración especificada. Por lo tanto, asegúrese de renovarla cuando llegue el momento o, de lo contrario, la autenticación de la API dejará de funcionar. También puede eliminar y volver a crear esta clave si cree que se ha visto comprometida.
    >
    >
11. Haga clic en el botón **Puntos de conexión** en la parte superior de la página y copie el **PUNTO DE CONEXIÓN DE TOKEN DE OAUTH 2.0**.

    ![][14]

16. Este punto de conexión tendrá el formato siguiente, donde el GUID de la dirección URL es el **TENANT_ID** (anótelo): `https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Ahora procederemos a configurar los permisos para esta aplicación. Para ello, tendrá que abrir el [Portal de Azure](https://portal.azure.com). 
18. Haga clic en **Grupos de recursos** y busque el grupo de recursos de **Mobile Engagement**.

    ![][15]

19. Haga clic en el grupo de recursos de **Mobile Engagement** y desplácese hasta la sección **Configuración** aquí.

    ![][16]

20. Haga clic en **Usuarios** en la sección Configuración y, después, haga clic en **Agregar** para agregar un usuario.

    ![][17]

21. Haga clic en **Seleccionar un rol**.

    ![][18]

22. Haga clic en **Propietario**.

    ![][19]

23. Busque el nombre de la aplicación (**AD\_APP\_NAME**) en el cuadro de búsqueda. No verá esto de forma predeterminada aquí. Una vez que lo encuentre, selecciónelo y haga clic en **Seleccionar** en la parte inferior de la sección.

    ![][20]

24. En la sección **Agregar acceso**, se mostrará como **1 usuario, 0 grupos**. Haga clic en **Aceptar** en esta sección para confirmar el cambio.

    ![][21]

Ahora completó la configuración de Azure AD necesaria y ya está listo para llamar a las API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



