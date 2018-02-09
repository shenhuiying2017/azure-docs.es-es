---
title: "Autorización de las cuentas de desarrollador mediante Azure Active Directory - Azure API Management | Microsoft Docs"
description: "Obtenga información sobre cómo autorizar a los usuarios para que usen Azure Active Directory en Administración de API."
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
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory en Azure API Management

En esta guía se muestra cómo habilitar el acceso al portal para desarrolladores para todos los usuarios desde Azure Active Directory. También se muestra cómo administrar los grupos de usuarios de Azure Active Directory mediante la adición de grupos externos que contienen los usuarios de un directorio de Azure Active Directory.

> [!WARNING]
> La integración de Azure Active Directory solo está disponible en los niveles [Desarrollador, Estándar y Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>requisitos previos

- Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
- Importe y publique una instancia de API Management. Para más información, consulte [Importación y publicación](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). 
2. Seleccionar ![arrow](./media/api-management-howto-aad/arrow.png).
3. En el cuadro de búsqueda, escriba "api".
4. Haga clic en **Servicios de administración de API**.
5. Seleccione la instancia del servicio APIM.
6. En **Seguridad**, seleccione **Identidades**.

    ![Identidades externas](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Haga clic en **+ Agregar** desde la parte superior.

    Aparece la ventana **Agregar proveedor de identidad** a la derecha.
8. En **Tipo de proveedor**, seleccione **Azure Active Directory**.

    En la ventana aparecen controles que le permiten escribir otra información necesaria. Los controles se incluyen: **Id. de cliente** y **Secreto de cliente** (más adelante en el tutorial obtendrá información al respecto).
9. Anote la **dirección URL de redireccionamiento**.  
10. En el explorador, abra una pestaña diferente. 
11. Abra [Azure Portal](https://portal.azure.com).
12. Seleccionar ![arrow](./media/api-management-howto-aad/arrow.png).
13. Escriba "active" y aparecerá **Azure Active Directory**.
14. Seleccione **Azure Active Directory**.
15. En **Administrar**, seleccione **Registro de aplicación**.

    ![Registro de aplicación](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Haga clic en **Nuevo registro de aplicaciones**.

    Aparecerá la ventana **Crear** a la derecha. Aquí es donde debe especificar la información pertinente de la aplicación de AAD.
17. Escriba un nombre para la aplicación.
18. Para el tipo de aplicación, seleccione **Aplicación web o API**.
19. Como dirección URL de inicio de sesión, escriba la del portal para desarrolladores. En este ejemplo, la dirección URL de inicio de sesión es: https://apimwithaad.portal.azure-api.net/signin.
20. Haga clic en **Crear** para crear la aplicación.
21. Para buscar la aplicación, seleccione **Registros de aplicación** y busque por el nombre.

    ![Registro de aplicación](./media/api-management-howto-aad/find-your-app.png)
22. Después de que se ha registrado la aplicación, vaya a **Dirección URL de respuesta** y asegúrese de que la dirección URL de redireccionamiento esté establecida en el valor obtenido en el paso 9. 
23. Si quiere configurar la aplicación (por ejemplo, cambiar **la dirección URL del identificador de aplicación**) seleccione **Propiedades**.

    ![Registro de aplicación](./media/api-management-howto-aad/api-management-with-aad004.png)

    Si se van a usar varios directorios de Azure Active Directory para la aplicación, haga clic en **Sí** para **La aplicación es multiempresa**. El valor predeterminado es **No**.
24. Para establecer permisos de aplicación, seleccione**Permisos necesarios**.
25. Seleccione la aplicación y active las casillas **Leer datos de directorio** e **Iniciar sesión y leer el perfil de usuario**.

    ![Registro de aplicación](./media/api-management-howto-aad/api-management-with-aad005.png)

    Para obtener más información sobre la aplicación y los permisos delegados, consulte [Acceso a Graph API][Accessing the Graph API].
26. En la ventana izquierda, copie el valor de **Id. de aplicación**.

    ![Registro de aplicación](./media/api-management-howto-aad/application-id.png)
27. Vuelva a cambiar a la aplicación de API Management. Se debe mostrar la ventana **Add identity provider** (Agregar proveedor de identidades). <br/>Pegue el valor de **Application ID** (Id. de aplicación) en el cuadro **Client Id** (Id. de cliente).
28. Vuelva a la configuración de Azure Active Directory y haga clic en **Claves**.
29. Cree una nueva clave y especifique un nombre y una duración. 
30. Haga clic en **Save**(Guardar). Se genera la clave.

    Copie la clave en el Portapapeles.

    ![Registro de aplicación](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anote esta clave. Una vez que se cierre la ventana de configuración de Azure Active Directory, la clave no se puede mostrar de nuevo.
    > 
    > 
31. Vuelva a cambiar a la aplicación de API Management. <br/>En la ventana **Add identity provider** (Agregar proveedor de identidades), pegue la clave en el cuadro de texto **Client secret** (Secreto de cliente).
32. La ventana **Add identity provider** (Agregar proveedor de identidades) contiene el cuadro de texto **Allowed Tenants** (Inquilinos permitidos), donde especifica qué directorios tienen acceso a las API de la instancia del servicio API Management. <br/>Especifique los dominios de las instancias de Azure Active Directory a los que desea conceder acceso. Puede separar varios dominios mediante nuevas líneas, espacios o comas.

    En la sección **Inquilinos permitidos** pueden especificarse varios dominios. Para que un usuario pueda iniciar sesión desde otro dominio distinto al dominio original donde se registró la aplicación, un administrador global de ese otro dominio deberá conceder antes a la aplicación permiso de acceso a los datos del directorio. Para conceder permiso, el administrador global debe ir a `https://<URL of your developer portal>/aadadminconsent` (por ejemplo, https://contoso.portal.azure-api.net/aadadminconsent), escribir el nombre de dominio del inquilino de Active Directory al que desea conceder acceso y hacer clic en Enviar. En el ejemplo siguiente, un administrador global de `miaoaad.onmicrosoft.com` está intentando conceder permiso a este portal para desarrolladores en particular. 

33. Una vez especificada la configuración deseada, haga clic en **Add** (Agregar).

    ![Registro de aplicación](./media/api-management-howto-aad/api-management-with-aad007.png)

Después de guardar los cambios, los usuarios de la instancia de Azure Active Directory especificada pueden iniciar sesión en el portal para desarrolladores mediante los pasos descritos en [Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory](#log_in_to_dev_portal).

![Permisos](./media/api-management-howto-aad/api-management-aad-consent.png)

En la siguiente pantalla, se pedirá al administrador global que confirme que proporciona el permiso. 

![Permisos](./media/api-management-howto-aad/api-management-permissions-form.png)

Si un administrador no global intenta iniciar sesión antes de que un administrador global conceda los permisos correspondientes, el inicio de sesión no se puede realizar y aparece una pantalla de error.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Adición de un grupo externo de Azure Active Directory

Tras habilitar el acceso para usuarios en un directorio de Azure Active Directory, puede agregar grupos de Azure Active Directory a Administración de API para administrar más fácilmente la asociación de los desarrolladores del grupo a los productos deseados.

Para configurar un grupo externo de Azure Active Directory, antes debe configurarse la instancia de Azure Active Directory en la pestaña de identidades mediante el procedimiento descrito en la sección anterior. 

Los grupos externos de Azure Active Directory se agregan desde la pestaña **Groups** (Grupos) de la instancia de API Management.

![Grupos](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Seleccione la pestaña **Grupos** .
2. Haga clic en el botón **Add AAD group** (Agregar grupo de AAD).
3. Seleccione el grupo que desea agregar.
4. Presione el botón **Select** (Seleccionar).

Después de que se ha creado un grupo de Azure Active Directory, para revisar y configurar las propiedades de los grupos externos una vez que se han agregado, haga clic en el nombre del grupo en la pestaña **Groups** (Grupos).

Desde aquí puede editar el **Nombre** y la **Descripción** del grupo.
 
Los usuarios de la instancia de Azure Active Directory configurada pueden iniciar sesión en el portal para desarrolladores y ver y suscribirse a los grupos para los que tienen visibilidad mediante las instrucciones de la sección siguiente.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory

Para iniciar sesión en el portal para desarrolladores con la cuenta de Azure Active Directory configurada en las secciones anteriores, abra una nueva ventana del explorador mediante la **URL de inicio de sesión** de la configuración de la aplicación de Active Directory y haga clic en **Azure Active Directory**.

![Portal para desarrolladores][api-management-dev-portal-signin]

Escriba las credenciales de uno de los usuarios del directorio de Azure Active Directory y haga clic en **Iniciar sesión**.

![Iniciar sesión][api-management-aad-signin]

En caso de requerirse más información, puede que se le solicite un formulario de registro. Complete el formulario de registro y haga clic en **Suscribirse**.

![Registro][api-management-complete-registration]

Ahora el usuario ha iniciado sesión en el portal para desarrolladores para la instancia del servicio Administración de API.

![Registro completado][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
