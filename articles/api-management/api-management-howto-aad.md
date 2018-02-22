---
title: "Autorización de las cuentas de desarrollador mediante Azure Active Directory en Azure API Management | Microsoft Docs"
description: Aprenda a autorizar a los usuarios mediante Azure Active Directory en API Management.
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
ms.openlocfilehash: d89257cba70fb82d56fb1beef8a8efe66a8af02d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorización de las cuentas de desarrollador mediante Azure Active Directory en Azure API Management

En este artículo se muestra cómo habilitar el acceso al portal para desarrolladores para usuarios de Azure Active Directory. También se muestra cómo administrar grupos de usuarios de Azure AD mediante la adición de grupos externos que contienen a los usuarios.

> [!NOTE]
> La integración de Azure AD solo está disponible en los niveles [Desarrollador, Estándar y Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>requisitos previos

- Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
- Importe y publique una instancia de Azure API Management. Para más información, consulte [Importación y publicación](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorización de las cuentas de desarrollador con Azure AD

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). 
2. Seleccionar ![arrow](./media/api-management-howto-aad/arrow.png).
3. En el cuadro de búsqueda, escriba **api**.
4. Seleccione **Servicios API Management**.
5. Seleccione su instancia de servicio API Management.
6. En **Seguridad**, seleccione **Identidades**.

7. Haga clic en **+ Agregar** en la parte superior.

    Aparece el panel **Agregar proveedor de identidades** a la derecha.
8. En **Tipo de proveedor**, seleccione **Azure Active Directory**.

    En el panel aparecen controles que le permiten escribir otra información necesaria. Los controles incluyen **Id. de cliente** y **Secreto de cliente**. (Más adelante en el artículo obtendrá información sobre estos controles).
9. Anote el contenido de **Dirección URL de redireccionamiento**.
    
   ![Pasos para agregar un proveedor de identidades en Azure Portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. En el explorador, abra una pestaña diferente. 
11. Vaya a [Azure Portal](https://portal.azure.com).
12. Seleccionar ![arrow](./media/api-management-howto-aad/arrow.png).
13. Escriba **active**. Aparece el panel **Azure Active Directory**.
14. Seleccione **Azure Active Directory**.
15. En **Administrar**, seleccione **Registros de aplicaciones**.
16. Seleccione **Nuevo registro de aplicaciones**.

    ![Selecciones para crear un nuevo registro de aplicación](./media/api-management-howto-aad/api-management-with-aad002.png)

    Aparece el panel **Crear** a la derecha. Aquí es donde especifica información pertinente de la aplicación de Azure AD.
17. Escriba un nombre para la aplicación.
18. Para el tipo de aplicación, seleccione **Aplicación web o API**.
19. Como dirección URL de inicio de sesión, escriba la del portal para desarrolladores. En este ejemplo, la dirección URL de inicio de sesión es: https://apimwithaad.portal.azure-api.net/signin.
20. Haga clic en **Crear** para crear la aplicación.
21. Para buscar la aplicación, seleccione **Registros de aplicación** y busque por el nombre.

    ![Cuadro donde buscar una aplicación](./media/api-management-howto-aad/find-your-app.png)
22. Después de haber registrado la aplicación, vaya a **Dirección URL de respuesta** y asegúrese de que la **dirección URL de redireccionamiento** esté establecida en el valor obtenido en el paso 9. 
23. Si quiere configurar la aplicación (por ejemplo, cambiar **la dirección URL del identificador de aplicación**) seleccione **Propiedades**.

    ![Apertura del panel "Propiedades"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Si se van a usar varias instancias de Azure AD para esta aplicación, seleccione **Sí** en **Multiinquilino**. El valor predeterminado es **No**.
24. Para establecer permisos de aplicación, seleccione**Permisos necesarios**.
25. Seleccione la aplicación y active las casillas **Leer datos de directorio** y **Iniciar sesión y leer el perfil del usuario**.

    ![Casillas de permisos](./media/api-management-howto-aad/api-management-with-aad005.png)

    Para más información sobre los permisos de la aplicación y los permisos delegados, consulte [Acceso a la API Graph][Accessing the Graph API].
26. En el panel izquierdo, copie el valor de **Id. de aplicación**.

    ![Valor de Id. de aplicación](./media/api-management-howto-aad/application-id.png)
27. Vuelva a cambiar a la aplicación de API Management. 

    En la ventana **Add identity provider** (Agregar proveedor de identidades), pegue el valor de **Application ID** (Id. de aplicación) en el cuadro **Client ID** (Id. de cliente).
28. Cambie de nuevo a la configuración de Azure AD y seleccione **Claves**.
29. Cree una nueva clave y especifique un nombre y una duración. 
30. Seleccione **Guardar**. Se genera la clave.

    Copie la clave en el Portapapeles.

    ![Selecciones para la creación de una clave](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anote esta clave. Después de cerrar el panel de configuración de Azure AD, la clave no se puede mostrar de nuevo.
    > 
    > 
31. Vuelva a cambiar a la aplicación de API Management. 

    En la ventana **Add identity provider** (Agregar proveedor de identidades), pegue la clave en el cuadro de texto **Client secret** (Secreto de cliente).
32. La ventana **Add identity provider** (Agregar proveedor de identidades) también contiene el cuadro de texto **Allowed Tenants** (Inquilinos permitidos). Ahí, especifique los dominios de las instancias de Azure AD a las que quiere conceder acceso a las API de la instancia de servicio API Management. Puede separar varios dominios mediante nuevas líneas, espacios o comas.

    Puede especificar varios dominios en la sección **Allowed Tenants** (Inquilinos permitidos). Para que un usuario pueda iniciar sesión desde otro dominio distinto al dominio original donde se registró la aplicación, un administrador global de ese otro dominio debe conceder antes a la aplicación permiso de acceso a los datos del directorio. Para conceder permiso, el administrador global debe:
    
    a. Ir a `https://<URL of your developer portal>/aadadminconsent` (por ejemplo, https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Escribir el nombre de dominio del inquilino de Azure AD al que desea dar acceso.
    
    c. Seleccione **Submit** (Enviar). 
    
    En el ejemplo siguiente, un administrador global de miaoaad.onmicrosoft.com está intentando conceder permiso a este portal para desarrolladores en particular. 

33. Después de especificar la configuración deseada, seleccione **Agregar**.

    ![Botón Agregar del panel Agregar proveedor de identidades](./media/api-management-howto-aad/api-management-with-aad007.png)

Después de guardar los cambios, los usuarios de la instancia de Azure AD especificada pueden iniciar sesión en el portal para desarrolladores siguiendo los pasos de [Inicio de sesión en el portal para desarrolladores con una cuenta de Azure AD](#log_in_to_dev_portal).

![Especificación del nombre de un inquilino de Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

En la siguiente pantalla, se pide al administrador global que confirme la concesión del permiso. 

![Confirmación de la asignación de permisos](./media/api-management-howto-aad/api-management-permissions-form.png)

Si un administrador no global intenta iniciar sesión antes de que un administrador global conceda los permisos, el inicio de sesión no se puede realizar y aparece una pantalla de error.

## <a name="add-an-external-azure-ad-group"></a>Incorporación de un grupo externo de Azure AD

Después de permitir el acceso para los usuarios en una instancia de Azure AD, puede agregar grupos de Azure AD en API Management. Luego, puede administrar de manera más fácil la asociación de los desarrolladores del grupo con los productos deseados.

Para configurar un grupo externo de Azure AD, primero debe configurar la instancia de Azure AD en la pestaña **Identities** (Identidades) mediante el procedimiento de la sección anterior. 

Los grupos externos de Azure AD se agregan desde la pestaña **Grupos** de la instancia de API Management.

1. Seleccione la pestaña **Grupos** .
2. Seleccione el botón **Add AAD group** (Agregar grupo de AAD).
   ![Botón "Add AAD group" (Agregar grupo de AAD)](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Seleccione el grupo que quiere agregar.
4. Presione el botón **Select** (Seleccionar).

Después de agregar un grupo externo de Azure AD, puede revisar y configurar sus propiedades. Seleccione el nombre del grupo en la pestaña **Groups** (Grupos). Desde aquí, puede editar la información de **nombre** y **descripción** del grupo.
 
Los usuarios de la instancia de Azure AD configurada ya pueden iniciar sesión en el portal para desarrolladores. Pueden ver los grupos para los que tengan visibilidad y suscribirse a ellos.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Inicio de sesión en el portal para desarrolladores con una cuenta de Azure AD

Para iniciar sesión en el portal para desarrolladores mediante una cuenta de Azure AD que ha configurado en las secciones anteriores:

1. Abra una nueva ventana del explorador mediante la dirección URL de inicio de sesión de la configuración de aplicación de Active Directory y seleccione **Azure Active Directory**.

   ![Página de inicio de sesión][api-management-dev-portal-signin]

2. Escriba las credenciales de uno de los usuarios de Azure AD y seleccione **Iniciar sesión**.

   ![Inicio de sesión con el nombre de usuario y la contraseña][api-management-aad-signin]

3. En caso de requerirse más información, puede que se le solicite un formulario de registro. Complete el formulario de registro y seleccione **Suscribirse**.

   ![Botón Suscribirse del formulario de registro][api-management-complete-registration]

Ahora ya inició sesión en el portal para desarrolladores de la instancia del servicio API Management.

![Portal para desarrolladores una vez completado el registro][api-management-registration-complete]

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

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
