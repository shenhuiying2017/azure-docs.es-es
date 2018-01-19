---
title: "Descripción del manifiesto de aplicación de Azure Active Directory | Microsoft Docs"
description: "Cobertura detallada del manifiesto de aplicación de Azure Active Directory, que representa la configuración de identidad de una aplicación en un inquilino de Azure AD, y se usa para facilitar la autorización de OAuth, la experiencia de consentimiento y mucho más."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
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
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Manifiesto de aplicación de Azure Active Directory
Las aplicaciones que se integran en Azure AD deben registrarse en un inquilino de Azure AD. Esta aplicación se puede configurar con el manifiesto de la aplicación (en la hoja de Azure AD) en [Azure Portal](https://portal.azure.com).

## <a name="manifest-reference"></a>Referencia de manifiesto

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Clave  |Tipo de valor |Valor de ejemplo  |DESCRIPCIÓN  |
|---------|---------|---------|---------|
|appID     |  Cadena de identificador       |""|  El identificador único de la aplicación que Azure AD asigna a una aplicación.|
|appRoles     |    Tipo de matriz     |[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]|La colección de roles que una aplicación puede declarar. Dichos roles se pueden asignar a usuarios, grupos o entidades de servicio.|
|availableToOtherTenants|boolean|true|Si este valor se establece en true, la aplicación está disponible para otros inquilinos.  Si se establece en false, la aplicación solo está disponible para el inquilino en que está registrada.  Para más información, consulte [Inicio de sesión de cualquier usuario de Azure Active Directory (AD) mediante el patrón de aplicación multiempresa](active-directory-devhowto-multi-tenant-overview.md). |
|DisplayName     |cadena         |MyRegisteredApp         |El nombre para mostrar de la aplicación. |
|errorURL     |cadena         |http:<i></i>//MyRegisteredAppError         |La dirección URL para los errores detectados en una aplicación. |
|groupMembershipClaims     |    cadena     |    1     |   Una máscara de bits que configura la notificación de grupo emitida en el token de acceso de usuario o de OAuth 2.0 que la aplicación espera. Los valores de la máscara de bits son: 0: No, 1: Grupos de seguridad y roles de Azure AD, 2: Reservado y 4: Reservado. Si la máscara de bits se establece en 7, se obtendrán todos los grupos de seguridad, grupos de distribución y los roles de directorio de Azure AD a los que pertenezca el usuario que inició sesión.      |
|optionalClaims     |  cadena       |     null    |    Las notificaciones opcionales que el servicio de token de seguridad devuelve en el token para esta aplicación específica.     |
|acceptMappedClaims    |      boolean   | true        |    Si este valor se establece en true, permite que una aplicación use la asignación de notificaciones sin especificar ninguna clave de firma personalizada.|
|página principal     |  cadena       |http:<i></i>//MyRegistererdApp         |    La dirección URL a la página principal de la aplicación.     |
|identifierUris     |  Matriz de cadena       | http:<i></i>//MyRegistererdApp        |   Los identificadores URI definidos por el usuario que identifican de manera única una aplicación web en su inquilino de Azure AD o en un dominio personalizado comprobado si la aplicación tiene varios inquilinos.      |
|keyCredentials     |   Tipo de matriz      |   [{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]      |   Esta propiedad contiene referencias a las credenciales asignadas por la aplicación, secretos compartidos basados en cadena y certificados X.509.  Estas credenciales entran en juego cuando se solicitan tokens de acceso (cuando la aplicación actúa como cliente, en lugar de como recurso).     |
|knownClientApplications     |     Tipo de matriz    |    [guid]     |     El valor se utiliza para el consentimiento de la unión si dispone de una solución que contenga dos partes, una aplicación cliente y una aplicación de API de web personalizada. Si especifica el valor de appID de la aplicación cliente en este valor, el usuario solo tendrá que dar su consentimiento una vez a la aplicación cliente. Azure AD sabrá que dar el consentimiento al cliente significa que se consiente implícitamente la API web y aprovisionará automáticamente entidades de servicio para el cliente y la API web a la vez (tanto el cliente como la aplicación de API web deben estar registrados en el mismo inquilino).|
|logoutUrl     |   cadena      |     http:<i></i>//MyRegisteredAppLogout    |   La dirección URL de cierre de sesión de la aplicación.      |
|oauth2AllowImplicitFlow     |   boolean      |  false       |       Especifica si esta aplicación web puede solicitar tokens de flujo implícitos de OAuth 2.0. El valor predeterminado es false. Se utiliza para las aplicaciones que usan el explorador, como las aplicaciones de página única de Javascript. |
|oauth2AllowUrlPathMatching     |   boolean      |  false       |   Especifica si, como parte de las solicitudes de token de OAuth 2.0, Azure AD permitirá que las rutas del identificador URI de redirección coincidan con el valor de replyUrls de la aplicaciones. El valor predeterminado es false.      |
|oauth2Permissions     | Tipo de matriz         |      [{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]   |  La colección de ámbitos de permiso de OAuth 2.0 que la aplicación de API (recurso) web expone a las aplicaciones cliente. Estos ámbitos de permisos pueden concederse a las aplicaciones cliente durante el consentimiento. |
|oauth2RequiredPostResponse     | boolean        |    false     |      Especifica si, como parte de las solicitudes de tokens de OAuth 2.0, Azure AD permitirá solicitudes POST, frente a las solicitudes GET. El valor predeterminado es false, que especifica solo se permitirán solicitudes GET.   
|objectId     | Cadena de identificador        |     ""    |    El identificador único de la aplicación en el directorio.  No es el identificador que se usa para identificar la aplicación en cualquier transacción del protocolo.  Se usa para hacer referencia al objeto en las consultas del directorio.|
|passwordCredentials     | Tipo de matriz        |   [{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]      |    Consulte la descripción de la propiedad keyCredentials.     |
|publicClient     |  boolean       |      false   | Especifica si una aplicación es un cliente público (como una aplicación instalada que se ejecuta en un dispositivo móvil). El valor predeterminado es false.        |
|supportsConvergence     |  boolean       |   false      | Esta propiedad no se debe editar.  Acepte el valor predeterminado.        |
|replyUrls     |  Matriz de cadena       |   http:<i></i>//localhost     |  Esta propiedad multivalor contiene la lista de valores de redirect_uri registrados que Azure AD aceptará como destinos cuando se devuelven tokens. |
|requiredResourceAccess     |     Tipo de matriz    |    [{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}]     |   Especifica los recursos a los que debe tener acceso esta aplicación y el conjunto de ámbitos de permisos y roles de aplicación de OAuth que necesita en cada uno de esos recursos. Esta configuración previa del acceso necesario a recursos dirige la experiencia de consentimiento.|
|resourceAppId     |    Cadena de identificador     |  ""      |   El identificador único del recurso al que la aplicación requiere acceso. Este valor debe ser igual que el valor de appId declarado en la aplicación del recurso de destino.     |
|resourceAccess     |  Tipo de matriz       | Vea el valor de ejemplo de la propiedad requiredResourceAccess.        |   La lista de ámbitos de permiso y roles de aplicación de OAuth2.0 que requiere la aplicación del recurso especificado (contiene los valores de identificador y tipo de los recursos especificados)        |
|samlMetadataUrl|cadena|http:<i></i>//MyRegisteredAppSAMLMetadata|La dirección URL de los metadatos de SAML para la aplicación.| 

## <a name="next-steps"></a>pasos siguientes
* Para más información acerca de la relación entre los objetos de aplicación y de entidad de servicio de una aplicación, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory (Azure AD)][AAD-APP-OBJECTS].
* Para ver definiciones de algunos de los conceptos fundamentales para el desarrollador de Azure Active Directory (AD), consulte [Guía del desarrollador de Azure Active Directory][AAD-DEVELOPER-GLOSSARY].

Use la siguiente sección de comentarios para especificar opiniones que ayuden a afinar y dar forma al contenido.

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

