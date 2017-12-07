---
title: "Autorización de las cuentas de desarrollador mediante Azure Active Directory B2C - Azure API Management | Microsoft Docs"
description: Aprenda a autorizar a los usuarios para que usen Azure Active Directory B2C en API Management.
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: d843757c2ed63c4f8cff09d809c2de382b2aeb59
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> La integración de Azure Active Directory B2C solo está disponible en los niveles [Desarrollador y Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory B2C en Azure API Management
## <a name="overview"></a>Información general
Azure Active Directory B2C es una solución de administración de identidades en la nube, destinada a aplicaciones móviles y web orientadas al consumidor. Puede usarlo para administrar el acceso a su portal para desarrolladores. Esta guía le muestra la configuración que se necesita en el servicio de API Management para integrarse con Azure Active Directory B2C. Para más información sobre cómo habilitar el acceso al portal para desarrolladores con Azure Active Directory clásico, consulte [Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory].

> [!NOTE]
> Para completar los pasos descritos en esta guía, primero debe tener un inquilino de Azure Active Directory B2C en el que crear una aplicación. Además, debe tener listas las directivas de registro e inicio de sesión. Para más información, consulte [Introducción a Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorización de cuentas de desarrollador con Azure Active Directory B2C

1. Para comenzar, haga clic en **Portal para editores** en Azure Portal para el servicio API Management. De este modo, se abre el portal del publicador de API Management.

   ![Portal del publicador][api-management-management-console]

   > [!NOTE]
   > Si todavía no ha creado una instancia del servicio API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el [tutorial Introducción a Azure API Management][Get started with Azure API Management].

2. En el menú **API Management**, haga clic en **Seguridad**. En la pestaña **Identidades**, elija **Azure Active Directory B2C**.

  ![Identidades externas 1][api-management-howto-aad-b2c-security-tab]

3. Anote la **URL de redireccionamiento** y cambie a Azure Active Directory B2C en Azure Portal.

  ![Identidades externas 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Haga clic en el botón **Aplicaciones**.

  ![Registro de una aplicación nueva 1][api-management-howto-aad-b2c-portal-menu]

5. Haga clic en el botón **Agregar** para crear una nueva aplicación de Azure Active Directory B2C.

  ![Registro de una aplicación nueva 2][api-management-howto-aad-b2c-add-button]

6. En la hoja **Nueva aplicación**, escriba un nombre para la aplicación. Elija **Sí** en **Aplicación web o API Web** y elija **Sí** en **Permitir flujo implícito**. Después, copie la **URL de redireccionamiento** de la sección **Azure Active Directory B2C** de la pestaña **Identidades** del portal del publicador y péguela en el cuadro de texto **Dirección URL de respuesta**.

  ![Registro de una aplicación nueva 3][api-management-howto-aad-b2c-app-details]

7. Haga clic en el botón **Crear** . Cuando se crea la aplicación, aparece en la hoja **Aplicaciones**. Haga clic en el nombre de la aplicación para ver sus detalles.

  ![Registro de una aplicación nueva 4][api-management-howto-aad-b2c-app-created]

8. En la hoja **Propiedades**, copie el **identificador de aplicación** en el Portapapeles.

  ![Identificador de aplicación 1][api-management-howto-aad-b2c-app-id]

9. Vuelva al portal del publicador y pegue el identificador en el cuadro de texto **Id. de cliente**.

  ![Identificador de aplicación 2][api-management-howto-aad-b2c-client-id]

10. Vuelva a Azure Portal, haga clic en el botón **Claves** y, luego, haga clic en **Generar clave**. Haga clic en **Guardar** para guardar la configuración y mostrar la **clave de la aplicación**. Copie la clave en el Portapapeles.

  ![Clave de aplicación 1][api-management-howto-aad-b2c-app-key]

11. Vuelva al portal del publicador y pegue la clave en el cuadro de texto **Secreto del cliente** .

  ![Clave de aplicación 2][api-management-howto-aad-b2c-client-secret]

12. Especifique el nombre de dominio del inquilino de Azure Active Directory B2C en **Inquilino permitido**.

  ![Inquilino permitido][api-management-howto-aad-b2c-allowed-tenant]

13. Especifique la **directiva de suscripción** y la **directiva de inicio de sesión**. Si lo desea, también puede proporcionar la **directiva de edición de perfil** y la **directiva de restablecimiento de contraseña**.

  ![Directivas][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Para más información sobre las directivas, consulte [Azure Active Directory B2C: marco de directivas extensible].

14. Una vez que especifique la configuración deseada, haga clic en **Guardar**.

  Una vez que se guardan los cambios, los desarrolladores podrán crear cuentas nuevas e iniciar sesión en el portal para desarrolladores con Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Registro de una cuenta de desarrollador con Azure Active Directory B2C

1. Para registrar una cuenta de desarrollador con Azure Active Directory B2C, abra una nueva ventana del explorador y vaya al portal para desarrolladores. Haga clic en el botón **Registrarse**.

   ![Portal para desarrolladores 1][api-management-howto-aad-b2c-dev-portal]

2. Elija registrarse con **Azure Active Directory B2C**.

   ![Portal para desarrolladores 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Se le redirigirá a la directiva de registro que configuró en la sección anterior. Elija registrarse con la dirección de correo electrónico o una de sus cuentas sociales existentes.

   > [!NOTE]
   > Si Azure Active Directory B2C es la única opción habilitada en la pestaña **Identidades** del portal para editores, se le redirigirá a la directiva de registro de forma directa.

   ![portal para desarrolladores][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Una vez completado el registro, se le redirigirá de nuevo al portal para desarrolladores. Ahora ya inició sesión en el portal para desarrolladores de la instancia del servicio API Management.

    ![Registro completado][api-management-registration-complete]

## <a name="next-steps"></a>Pasos siguientes

*  [Introducción a Azure Active Directory B2C]
*  [Azure Active Directory B2C: marco de directivas extensible]
*  [Uso de una cuenta Microsoft como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de Google como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de LinkedIn como proveedor de identidades en Azure Active Directory B2C]
*  [Uso de una cuenta de Facebook como proveedor de identidades en Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Introducción a Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: marco de directivas extensible]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Uso de una cuenta Microsoft como proveedor de identidades en Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Uso de una cuenta de Google como proveedor de identidades en Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Uso de una cuenta de Facebook como proveedor de identidades en Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Uso de una cuenta de LinkedIn como proveedor de identidades en Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
