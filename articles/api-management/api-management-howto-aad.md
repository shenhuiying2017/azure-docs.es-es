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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 45c8632f4e03c86cf4e32c6d1151977792f32add
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
> [!WARNING]
> La integración de Azure Active Directory solo está disponible en los niveles [Desarrollador, Estándar y Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory en Azure API Management
## <a name="overview"></a>Información general
En esta guía se muestra cómo habilitar el acceso al portal para desarrolladores para todos los usuarios desde Azure Active Directory. También se muestra cómo administrar los grupos de usuarios de Azure Active Directory mediante la adición de grupos externos que contienen los usuarios de un directorio de Azure Active Directory.

> Para completar los pasos descritos en esta guía, debe tener un directorio de Azure Active Directory en el que crear una aplicación.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Procedimiento para autorizar a las cuentas de desarrollador para que usen Azure Active Directory
Para comenzar, haga clic en **Portal para editores** en Azure Portal para el servicio API Management. De este modo, se abre el portal del publicador de API Management.

![Portal del publicador][api-management-management-console]

> Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].
> 
> 

Haga clic en **Seguridad** en el menú **API Management** de la izquierda y, después, en **Identidades externas**.

![Identidades externas][api-management-security-external-identities]

Haga clic en **Azure Active Directory**. Anote la **URL de redireccionamiento** y cambie a Azure Active Directory en el Portal de Azure clásico.

![Identidades externas][api-management-security-aad-new]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Azure Active Directory y, a continuación, elija **Agregar una aplicación que mi organización está desarrollando**.

![Agregar nueva aplicación de Azure Active Directory][api-management-new-aad-application-menu]

Escriba un nombre para la aplicación, seleccione **Aplicación web y/o API web**y haga clic en el botón para ir al paso siguiente.

![Nueva aplicación de Azure Active Directory][api-management-new-aad-application-1]

Para **URL de inicio de sesión**, escriba la dirección URL de inicio de sesión en el portal para desarrolladores. En este ejemplo, la **URL de inicio de sesión** es `https://aad03.portal.current.int-azure-api.net/signin`. 

Para la **URL de id. de aplicación**, especifique el dominio predeterminado o un dominio personalizado para Azure Active Directory y anexe a este una cadena única. En este ejemplo, el dominio predeterminado de **https://contoso5api.onmicrosoft.com** se usa con el sufijo **/api** especificado.

![Propiedades de la nueva aplicación de Azure Active Directory][api-management-new-aad-application-2]

Haga clic en el botón de verificación para guardar y crear la nueva aplicación y cambie a la pestaña **Configurar** para configurar la aplicación.

![Nueva aplicación de Azure Active Directory creada][api-management-new-aad-app-created]

Si se van a usar varios directorios de Azure Active Directory para la aplicación, haga clic en **Sí** para **La aplicación es multiempresa**. El valor predeterminado es **No**.

![La aplicación es multiempresa][api-management-aad-app-multi-tenant]

Copie la **URL de redireccionamiento** de la sección **Azure Active Directory** de la pestaña **Identidades externas** del portal del publicador y péguela en el cuadro de texto **URL de respuesta**. 

![URL de respuesta][api-management-aad-reply-url]

Desplácese a la parte inferior de la pestaña Configurar, seleccione la lista desplegable **Permisos de la aplicación** y active **Leer datos de directorio**.

![Permisos de la aplicación][api-management-aad-app-permissions]

Seleccione la lista desplegable **Permisos delegados** y active **Habilitar inicio de sesión y leer perfiles de usuarios**.

![Permisos delegados][api-management-aad-delegated-permissions]

> Para obtener más información sobre la aplicación y los permisos delegados, consulte [Acceso a la API Graph][Accessing the Graph API].
> 
> 

Copie el **Id. de cliente** en el Portapapeles.

![Id. de cliente][api-management-aad-app-client-id]

Vuelva al portal del publicador y pegue el **Id. de cliente** que ha copiado de la configuración de la aplicación de Azure Active Directory.

![Id. de cliente][api-management-client-id]

Vuelva a la configuración de Azure Active Directory, haga clic en la lista desplegable **Seleccionar duración** de la sección **Claves** y especifique un intervalo. En este ejemplo se usa el **año 1**.

![Clave][api-management-aad-key-before-save]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave. Copie la clave en el Portapapeles.

> Anote esta clave. Una vez que se cierre la ventana de configuración de Azure Active Directory, la clave no se puede mostrar de nuevo.
> 
> 

![Clave][api-management-aad-key-after-save]

Vuelva al portal del publicador y pegue la clave en el cuadro de texto **Secreto del cliente** .

![Secreto del cliente][api-management-client-secret]

**Inquilinos permitidos** especifica los directorios que tienen acceso a las API de la instancia del servicio Administración de API. Especifique los dominios de las instancias de Azure Active Directory a los que desea conceder acceso. Puede separar varios dominios mediante nuevas líneas, espacios o comas.

![Inquilinos permitidos][api-management-client-allowed-tenants]


Una vez especificada la configuración deseada, haga clic en **Guardar**.

![Save][api-management-client-allowed-tenants-save]

Después de guardar los cambios, los usuarios de la instancia de Azure Active Directory especificada pueden iniciar sesión en el portal para desarrolladores mediante los pasos descritos en [Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory][Log in to the Developer portal using an Azure Active Directory account].

En la sección **Inquilinos permitidos** pueden especificarse varios dominios. Para que un usuario pueda iniciar sesión desde otro dominio distinto al dominio original donde se registró la aplicación, un administrador global de ese otro dominio deberá conceder antes a la aplicación permiso de acceso a los datos del directorio. Para conceder permiso, el administrador global debe ir a `https://<URL of your developer portal>/aadadminconsent` (por ejemplo, https://contoso.portal.azure-api.net/aadadminconsent), escribir el nombre de dominio del inquilino de Active Directory al que desea conceder acceso y hacer clic en Enviar. En el ejemplo siguiente, un administrador global de `miaoaad.onmicrosoft.com` está intentando conceder permiso a este portal para desarrolladores en particular. 

![Permisos][api-management-aad-consent]

En la siguiente pantalla, se pedirá al administrador global que confirme que proporciona el permiso. 

![Permisos][api-management-permissions-form]

> Si un administrador no global intenta iniciar sesión antes de que un administrador global conceda los permisos correspondientes, el inicio de sesión no se puede realizar y aparece una pantalla de error.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Adición de un grupo externo de Azure Active Directory
Tras habilitar el acceso para usuarios en un directorio de Azure Active Directory, puede agregar grupos de Azure Active Directory a Administración de API para administrar más fácilmente la asociación de los desarrolladores del grupo a los productos deseados.

> Para configurar un grupo externo de Azure Active Directory, antes debe configurarse la instancia de Azure Active Directory en la pestaña de identidades mediante el procedimiento descrito en la sección anterior. 
> 
> 

Los grupos externos de Azure Active Directory se agregan desde la pestaña **Visibilidad** del producto para el que desea conceder acceso al grupo. Haga clic en **Productos**y, a continuación, en el nombre del producto deseado.

![Configure product][api-management-configure-product]

Cambie a la pestaña **Visibilidad** y haga clic en **Agregar grupos de Azure Active Directory**.

![Agregar grupos][api-management-add-groups]

Seleccione el **Inquilino de Azure Active Directory** en la lista desplegable y, a continuación, escriba el nombre del grupo que desee en el cuadro de texto **Grupos para agregar**.

![Seleccionar grupo][api-management-select-group]

El nombre del grupo aparecerá en la lista **Grupos** de Azure Active Directory, como se muestra en el ejemplo siguiente.

![Lista de grupos de Azure Active Directory][api-management-aad-groups-list]

Haga clic en **Agregar** para validar el nombre del grupo y agregar el grupo. En este ejemplo, se agrega el grupo externo **Contoso 5 Developers**. 

![Group added][api-management-aad-group-added]

Haga clic en **Guardar** para guardar la nueva selección de grupo.

Una vez configurado un grupo de Azure Active Directory de un producto, este aparecerá en la pestaña **Visibilidad** y podrá activarse para el resto de productos de la instancia del servicio API Management.

Para revisar y configurar las propiedades de los grupos externos una vez que se han agregado, haga clic en el nombre del grupo en la pestaña **Grupos**.

![Administrar grupos][api-management-groups]

Desde aquí puede editar el **Nombre** y la **Descripción** del grupo.

![Editar grupo][api-management-edit-group]

Los usuarios de la instancia de Azure Active Directory configurada pueden iniciar sesión en el portal para desarrolladores y ver y suscribirse a los grupos para los que tienen visibilidad mediante las instrucciones de la sección siguiente.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Inicio de sesión en el portal para desarrolladores con una cuenta de Azure Active Directory
Para iniciar sesión en el portal para desarrolladores con la cuenta de Azure Active Directory configurada en las secciones anteriores, abra una nueva ventana del explorador mediante la **URL de inicio de sesión** de la configuración de la aplicación de Active Directory y haga clic en **Azure Active Directory**.

![Portal para desarrolladores][api-management-dev-portal-signin]

Escriba las credenciales de uno de los usuarios del directorio de Azure Active Directory y haga clic en **Iniciar sesión**.

![Iniciar sesión][api-management-aad-signin]

En caso de requerirse más información, puede que se le solicite un formulario de registro. Complete el formulario de registro y haga clic en **Suscribirse**.

![Registro][api-management-complete-registration]

Ahora el usuario ha iniciado sesión en el portal para desarrolladores para la instancia del servicio Administración de API.

![Registro completado][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
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
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

