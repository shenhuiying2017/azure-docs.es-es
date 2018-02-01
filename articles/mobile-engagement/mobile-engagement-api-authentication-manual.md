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
ms.openlocfilehash: 0b4a999c6778040e71f862d3a010b6635e84b26e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis-manual-setup"></a>Autenticación con las API de REST de Mobile Engagement: configuración manual
Esta documentación es un apéndice de [Autenticación con las API de REST de Mobile Engagement](mobile-engagement-api-authentication.md). Asegúrese de leer ese artículo primero para entender el contexto. Describe también una forma alternativa de realizar una única configuración de la autenticación para las API de REST de Mobile Engagement mediante Azure Portal.

> [!NOTE]
> Las siguientes instrucciones se basan en [esta guía de Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Se personalizan para los requisitos de autenticación de las API de Mobile Engagement. Consúltelas, si desea conocer los pasos siguientes en detalle.

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com/).
2. Seleccione **Active Directory** en el panel izquierdo.

   ![Seleccione Active Directory][1]

3. Para ver las aplicaciones en el directorio, haga clic en **Registros de aplicaciones**.

   ![Ver las aplicaciones][3]

4. Seleccione **Nuevo registro de aplicaciones**.

   ![Agregar aplicación][4]

5. Rellene el nombre de la aplicación. Deje el tipo de aplicación como **Web app/API** y, a continuación, seleccione el botón **Siguiente**. Puede especificar direcciones URL ficticias en **URL DE INICIO DE SESIÓN**. No se utilizan para este escenario y no se validan las propias direcciones URL.

   Cuando termine, tendrá una aplicación de Azure Active Directory (Azure AD) con el nombre especificado. Es su **AD\_APP\_NAME** (nombre de aplicación de AD), por lo que debería anotarlo.

   ![Nombre de la aplicación][8]

7. Seleccione el nombre de la aplicación.

8. Busque el **Identificador de aplicación** y tome nota de este valor. Es el identificador del cliente que se usará como **CLIENT\_ID** para las llamadas a la API.

   ![Busque el identificador de la aplicación][10]

9. Busque la sección **Claves** que se encuentra a la derecha.

   ![Sección Claves][11]

10. Cree una nueva clave y, a continuación, cópiela inmediatamente. No se vuelve a mostrar.

    ![Panel de claves con detalles de la clave][12]

    > [!IMPORTANT]
    > Esta clave expira al final de la duración especificada. Asegúrese de renovarla cuando llegue el momento; en caso contrario, la autenticación de la API no volverá a funcionar. Si cree que se ha visto comprometida, puede eliminarla y volver a crearla.
    >
    
11. Seleccione el botón **Puntos de conexión** en la parte superior de la página. A continuación, copie el **PUNTO DE CONEXIÓN DE TOKEN de OAUTH 2.0**.

    ![Copiar el punto de conexión][14]

16. Este punto de conexión tiene el formato siguiente, donde el GUID de la dirección URL es el **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Ahora se configuran los permisos para esta aplicación. Para iniciar el proceso, vaya a [Azure Portal](https://portal.azure.com).

18. Seleccione **Grupos de recursos** y busque el grupo de recursos **MobileEngagement**.

    ![Buscar MobileEngagement][15]

19. Seleccione el grupo de recursos **MobileEngagement** y, a continuación, seleccione **Toda la configuración**.

    ![Ir a la configuración de MobileEngagement][16]

20. En la sección **Configuración**, seleccione **Usuarios**. Después, para agregar un usuario, seleccione **Agregar**.

    ![Adición de un usuario][17]

21. Haga clic en **Seleccionar un rol**.

    ![Seleccione un rol.][18]

22. Seleccione **Propietario**.

    ![Seleccionar Propietario como rol][19]

23. Busque el nombre de la aplicación (**AD\_APP\_NAME**) en el cuadro de búsqueda. Este nombre no está aquí de forma predeterminada. Una vez que lo encuentre, selecciónelo. Después, haga clic en **Seleccionar** en la parte inferior de la sección.

    ![Seleccionar el nombre][20]

24. En la sección **Agregar acceso**, aparece como **1 usuario, 0 grupos**. Para confirmar el cambio, haga clic en **Aceptar**.

    ![Confirmar que ha agregado el usuario][21]

Ahora ha completado la configuración de Azure AD necesaria y ya está todo preparado para llamar a las API.

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



