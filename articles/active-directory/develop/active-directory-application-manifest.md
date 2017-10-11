---
title: "Descripción del manifiesto de aplicación de Azure Active Directory | Microsoft Docs"
description: "Cobertura detallada del manifiesto de aplicación de Azure Active Directory, que representa la configuración de identidad de una aplicación en un inquilino de Azure AD, y se usa para facilitar la autorización de OAuth, la experiencia de consentimiento y mucho más."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Descripción del manifiesto de aplicación de Azure Active Directory
Las aplicaciones que se integran con Azure Active Directory (AD) deben estar registradas con un inquilino de Azure AD y proporcionar una configuración persistente de identidad para la aplicación. Esta configuración se consulta en tiempo de ejecución, lo que habilita los escenarios que permiten una aplicación para externalizar y negociar la autenticación/autorización mediante Azure AD. Para más información sobre el modelo de aplicación de Azure AD, consulte el artículo [Integración de aplicaciones con Azure Active Directory][ADD-UPD-RMV-APP].

## <a name="updating-an-applications-identity-configuration"></a>Actualización de la configuración de identidad de una aplicación
Hay en realidad varias opciones disponibles para actualizar las propiedades de la configuración de identidad de una aplicación, que varían en función de las capacidades y los niveles de dificultad, incluidas las siguientes:

* La **interfaz de usuario de [Azure Portal][AZURE-PORTAL]** permite actualizar las propiedades más comunes de una aplicación. Esta es la forma más rápida y menos propensa a errores de actualizar las propiedades de la aplicación pero no le otorga acceso completo a todas las propiedades, como sí lo hacen los dos métodos siguientes.
* Para escenarios más avanzados donde necesita actualizar las propiedades que no se exponen en el Portal de Azure clásico, puede modificar el **manifiesto de aplicación**. Este es el punto importante de este artículo y se explica con más detalle a partir de la siguiente sección.
* También es posible **escribir una aplicación que use la [API Graph][GRAPH-API]** para actualizarla, lo que requiere un mayor esfuerzo. Esto puede ser una opción atractiva, no obstante, si está escribiendo software de administración o si necesita actualizar las propiedades de la aplicación periódicamente de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Uso del manifiesto de aplicación para actualizar la configuración de identidad de una aplicación
A través de [Azure Portal][AZURE-PORTAL], puede administrar la configuración de la identidad de la aplicación al actualizar el manifiesto de aplicación mediante el editor de manifiestos en línea. También puede descargar y cargar el manifiesto de aplicación como un archivo JSON. No se almacena ningún archivo real en el directorio. El manifiesto de aplicación es simplemente una operación HTTP GET en la entidad de aplicación de API Graph de Azure AD y la carga es una operación HTTP PATCH en la entidad de aplicación.

Como consecuencia, para conocer el formato y las propiedades del manifiesto de aplicación, será preciso que haga referencia a la documentación de la [entidad de aplicación][APPLICATION-ENTITY] de la API Graph. Ejemplos de actualizaciones que se pueden realizar mediante la carga del manifiesto de aplicación:

* **Declarar los ámbitos de permiso (oauth2Permissions)** que expone la API web. Consulte el tema sobre cómo exponer las API web a otras aplicaciones en [Integración de aplicaciones con Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] para más información sobre la implementación de la suplantación de usuario mediante el ámbito de permisos delegado oauth2Permissions. Como ya se ha mencionó, las propiedades de la entidad de aplicación están documentadas en el artículo [Entity and Complex Type][APPLICATION-ENTITY] reference (Referencia de entidades y tipos complejos) de API Graph, incluida la propiedad oauth2Permissions, que es una colección del tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Declarar los roles de aplicación (appRoles) que expone la aplicación**. La propiedad appRole de la entidad de aplicación es una colección de tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Para ver un ejemplo de implementación, consulte el artículo [Role based access control in cloud applications using Azure AD][RBAC-CLOUD-APPS-AZUREAD] (Control de acceso basado en rol en aplicaciones en la nube mediante Azure AD).
* **Declarar las aplicaciones cliente conocidas (knownClientApplications)**, que permiten enlazar de forma lógica el consentimiento de las aplicaciones cliente especificadas a la API de recursos o web.
* **Solicitar a Azure AD que emita una notificación de pertenencias a grupos** para el usuario que inició sesión (groupMembershipClaims).  Esto también se puede configurar para emitir notificaciones sobre pertenencias a los roles de directorio del usuario. Para ver un ejemplo de implementación, consulte el artículo [Authorization in Cloud Applications using AD Groups][AAD-GROUPS-FOR-AUTHORIZATION] (Autorización en aplicaciones en la nube mediante grupos de AD).
* **Permitir que la aplicación admita flujos de concesión implícita de OAuth 2.0** (oauth2AllowImplicitFlow). Este tipo de flujo de concesión se utiliza con páginas web JavaScript integradas o aplicaciones de página única (SPA). Para más información sobre la concesión de autorización implícita, consulte [Descripción del flujo de concesión implícita de OAuth2 de Azure Active Directory (AD)][IMPLICIT-GRANT].
* **Habilitar el uso de certificados X509 como clave secreta** (keyCredentials). Para ver ejemplos de implementación, consulte los artículos [Build service and daemon apps in Office 365][O365-SERVICE-DAEMON-APPS] (Compilación de aplicaciones de servicio y de demonio en Office 365) y [Developer’s guide to auth with Azure Resource Manager API][DEV-GUIDE-TO-AUTH-WITH-ARM] (Guía del desarrollador para la autenticación con la API de Azure Resource Manager).
* **Agregar un nuevo URI de id. de aplicación** para su aplicación (identifierURIs[]). Los URI de id. de aplicación se usan para identificar de forma exclusiva una aplicación dentro de su inquilino de Azure AD (o entre varios inquilinos de Azure AD, para escenarios de múltiples inquilinos cuando se califican mediante un dominio personalizado verificado). Se emplean cuando se solicitan permisos para una aplicación de recursos o cuando se obtiene un token de acceso para una aplicación de recursos. Al actualizar este elemento, se realiza la misma actualización en la colección servicePrincipalNames[] de la entidad de servicio correspondiente, que se encuentra en el inquilino principal de la aplicación.

El manifiesto de aplicación también proporciona una forma adecuada de realizar un seguimiento del estado del registro de la aplicación. Como está disponible en formato JSON, la representación del archivo se puede proteger en el control de código fuente, junto con el código fuente de la aplicación.

## <a name="step-by-step-example"></a>Ejemplo paso a paso
Ahora vamos a recorrer los pasos necesarios para actualizar la configuración de identidad de la aplicación mediante el manifiesto de aplicación: Destacamos uno de los ejemplos anteriores, que muestra cómo declarar un nuevo ámbito de permiso en una aplicación de recurso:

1. Inicie sesión en [Azure Portal][AZURE-PORTAL].
2. Después de haberse autenticado, elija el inquilino de Azure AD; para ello, selecciónelo en la esquina superior derecha de la página.
3. Seleccione la extensión **Azure Active Directory** en el panel de navegación izquierdo y haga clic en **Registros de aplicaciones**.
4. Busque la aplicación que desea actualizar en la lista y haga clic en ella.
5. En la página de la aplicación, haga clic en **Manifiesto** para abrir el editor de manifiestos en línea. 
6. Puede editar directamente el manifiesto mediante este editor. Tenga en cuenta que el manifiesto sigue el esquema de la [Entidad de aplicación][APPLICATION-ENTITY] como se ha mencionado anteriormente: por ejemplo, suponiendo que desea implementar o exponer un permiso nuevo llamado "Employees.Read.All" en nuestra aplicación (API) de recursos, simplemente agregaría un elemento nuevo o adicional a la colección oauth2Permissions, es decir:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    La entrada debe ser única y, por tanto, debe generar un nuevo identificador único global (GUID) para la propiedad `"id"` . En este caso, debido a que especificamos `"type": "User"`, este permiso puede ser autorizado por cualquier cuenta autenticada por el inquilino de Azure AD en la que se registre la aplicación de recurso o de API. Esto otorga a la aplicación cliente permiso para acceder a ella en nombre de la cuenta. Las cadenas de descripción y nombre para mostrar se usan durante el consentimiento y para su presentación en Azure Portal.
6. Cuando haya terminado de actualizar el manifiesto, haga clic en **Guardar** para guardarlo.  
   
Ahora que se guarda el manifiesto, puede dar acceso a una aplicación cliente registrada al nuevo permiso que agregamos anteriormente. Esta vez puede usar la interfaz de usuario web de Azure Portal en lugar de modificar el manifiesto de la aplicación cliente:  

1. Primero, vaya a la hoja **Configuración** de la aplicación cliente a la que quiere agregar acceso a la nueva API, haga clic en **Permisos necesarios** y elija **Seleccionar una API**.
2. A continuación, aparecerá la lista de aplicaciones (API) de recursos registrado en el inquilino. Haga clic en la aplicación de recursos para seleccionarla, o escriba el nombre de la aplicación en el cuadro de búsqueda. Cuando encuentre la aplicación, haga clic en **Seleccionar**.  
3. Se le remitirá a la página **Seleccionar permisos**, donde aparece la lista de Permisos de la aplicación y Permisos delegados disponibles para la aplicación de recursos. Seleccione el nuevo permiso para poder agregarlo a la lista de permisos solicitados del cliente. Este permiso nuevo se almacenará en la configuración de identidad de la aplicación cliente, en la propiedad de colección "requiredResourceAccess".


Eso es todo. Ahora las aplicaciones se ejecutarán con su nueva configuración de identidad.

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de la relación entre los objetos de aplicación y entidad de servicio de una aplicación, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory][AAD-APP-OBJECTS].
* Para ver definiciones de algunos de los conceptos fundamentales para el desarrollador de Azure Active Directory (AD), consulte [Guía del desarrollador de Azure Active Directory][AAD-DEVELOPER-GLOSSARY].

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a refinar y remodelar nuestro contenido.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

