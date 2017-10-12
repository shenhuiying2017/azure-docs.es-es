---
title: Aplicaciones, permisos y consentimiento en Azure Active Directory | Microsoft Docs
description: "Azure AD Connect integrará sus directorios locales con Azure Active Directory. Esto le permite proporcionar una identidad común para las aplicaciones de Office 365, Azure y SaaS integradas con Azure AD."
keywords: "introducción a Azure AD, aplicaciones, qué es Azure AD Connect, instalación de active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6f6baf5e1538fb280a899065c64ca5688473c04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Aplicaciones, permisos y consentimiento en Azure Active Directory
Dentro de Azure Active Directory puede agregar aplicaciones a su directorio.  Las aplicaciones pueden variar según el tipo de aplicación.  Para ver las aplicaciones en el portal clásico, seleccione un directorio y elija las aplicaciones.

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo.

## <a name="types-of-apps"></a>Tipos de aplicaciones

1. **Aplicaciones de inquilino único** </br>
    - **Aplicaciones de inquilino único**: a menudo denominadas también aplicaciones de línea de negocio (LOB). Este es el caso en el que alguien de la organización desarrolla su propia aplicación y quiere que otros usuarios de esta puedan iniciar sesión en ella.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Aplicaciones de proxy de aplicación**: al exponer una aplicación local con el proxy de aplicación de Azure AD, se registra una aplicación de inquilino único en su inquilino (además del servicio de proxy de aplicación). Esta aplicación es la que representará la aplicación local en todas las interacciones en la nube (por ejemplo, en la autenticación). (Proxy de aplicación requiere Azure AD Basic o superior).


2. **Aplicaciones multiinquilino**
    - **Aplicaciones multiinquilino a las que otras personas puedan dar su consentimiento**: son parecidas a las "aplicaciones de inquilino único que desarrolla la organización". La diferencia principal (además de la lógica de la propia aplicación) es que los usuarios de otros inquilinos pueden también dar su consentimiento e iniciar sesión en la aplicación.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Aplicaciones multiinquilino que desarrollan otros, a las que Contoso puede dar consentimiento**. (O "aplicaciones consentidas", para abreviar). Esto es el otro lado de las "aplicaciones multiinquilino que desarrolla su organización". Cuando otra organización desarrolla una aplicación multiinquilino, los usuarios de su organización pueden dar consentimiento a la aplicación e iniciar sesión en ella.
    - **Aplicaciones propias de Microsoft**: las aplicaciones que representan los servicios de Microsoft. El consentimiento se basa en el hecho de que debe registrarse para acceder al servicio. Algunas veces existe una lógica y una experiencia de usuario para determinadas aplicaciones propias que se usa a menudo a la hora de establecer directivas relacionadas con el acceso a la aplicación.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Aplicaciones integradas previamente**: aplicaciones disponibles en la Galería de aplicaciones de Azure AD que puede agregar a su directorio para proporcionar un inicio de sesión único (y en algunos casos, aprovisionamiento) en aplicaciones SaaS populares.
    - **Inicio de sesión único de Azure AD**: inicio de sesión único "real" para las aplicaciones que se pueden integrar con Azure AD, mediante un protocolo de inicio de sesión compatible como SAML 2.0 o OpenID Connect. El asistente le guiará a través del proceso de configuración.
    - **Inicio de sesión único con contraseña**: Azure AD almacena de forma segura las credenciales del usuario para la aplicación, y la extensión del explorador de acceso de la aplicación de Azure AD "inserta" estas credenciales en el formulario de inicio de sesión. A este proceso también se le conoce como "almacenamiento de contraseñas".

## <a name="permissions"></a>Permisos

Cuando se registra una aplicación, el usuario que realiza el registro (es decir, el desarrollador) define los permisos y recursos a los que la aplicación necesita acceder. (Los recursos, por sí mismos, se definen como otras aplicaciones). Por ejemplo, alguien que está compilando una aplicación de lector de correo electrónico, puede indicar que su aplicación requiere el permiso de "Acceso a los buzones como el usuario que inició sesión" en el recurso "Office 365 Exchange Online":
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Para que una aplicación (el cliente) solicite un permiso determinado desde otra aplicación (el recurso), el desarrollador de la aplicación del recurso debe definir los permisos que existen. En nuestro ejemplo, Microsoft, el propietario de la aplicación del recurso "Office 365 Exchange Online", ha definido un permiso denominado "Acceso a los buzones como el usuario que inició sesión".

Al definir los permisos, el desarrollador de la aplicación debe definir si se puede dar consentimiento al permiso o si se requiere el consentimiento del administrador. Esto permite a los desarrolladores que los usuarios puedan dar consentimiento a sus propias aplicaciones solicitando únicamente permisos con bajo nivel de confidencialidad, pero requiere que los administradores den el consentimiento para permisos de mayor nivel. Por ejemplo, se ha definido la aplicación del recurso "Azure Active Directory" para que los usuarios pueden dar consentimiento a las aplicaciones, solicitando permisos limitados de solo lectura.  Sin embargo, se requiere el consentimiento del administrador para conseguir permisos completos de lectura y para todos los permisos de escritura.

Puesto que no se autentican clientes nativos, una aplicación que se define como una aplicación de cliente nativo solo puede solicitar permisos delegados. Esto significa que siempre debe haber un usuario real implicado en la obtención de un token. Las aplicaciones web y API web (clientes confidenciales), siempre se deben autenticar con Azure AD al acceder a un token. Esto significa que también tienen la posibilidad de solicitar permisos de solo aplicación. Por ejemplo, si un servicio back-end necesita autenticarse en otro servicio back-end. Las aplicaciones que solicitan permisos de solo aplicación requieren siempre el consentimiento del administrador.

En resumen:



- Una aplicación (cliente) indica los permisos que necesita para otras aplicaciones (recursos).
- Una aplicación (recurso) indica qué permisos se exponen a otras aplicaciones (clientes).
- Un permiso puede ser un permiso de solo aplicación o un permiso delegado.
- Un permiso delegado se puede marcar para indicar que "permite el consentimiento del usuario" o que "requiere el consentimiento del administrador".
- Una aplicación puede comportarse como un cliente (declarando que requiere permisos a un recurso), como un recurso (declarando los permisos que expone) o como ambos.

## <a name="controls"></a>Controles

La siguiente es una lista de los diferentes controles de administración disponibles para este comportamiento. Se puede obtener acceso a los controles de administración en el portal clásico desde la opción de configuración del directorio.

![](media/active-directory-apps-permissions-consent/apps7.png)

En Azure Portal en **Administrar**, **Configuración de usuario**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Puede controlar si los usuarios pueden dar consentimiento a aplicaciones:

En el portal clásico, seleccione **Users may give applications permissions to access their data**(Los usuarios pueden otorgar permisos para la aplicación para tener acceso a los datos).
![](media/active-directory-apps-permissions-consent/apps8.png)

En Azure Portal, seleccione **Los usuarios pueden permitir que las aplicaciones accedan a sus datos**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Puede controlar si los usuarios pueden registrar sus propias aplicaciones de línea de negocio de inquilino único: en el portal clásico, seleccione **Los usuarios pueden agregar aplicaciones integradas**.
![](media/active-directory-apps-permissions-consent/apps9.png)

En Azure Portal, seleccione **Los usuarios pueden permitir que las aplicaciones accedan a sus datos**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Incluso si permite que los usuarios registren las aplicaciones de línea de negocio de inquilino único, hay límites en lo que se puede registrar.  
>Por ejemplo, los desarrolladores que no son administradores de directorios.
>
>- Los usuarios no pueden hacer que una aplicación de inquilino único se convierta en una aplicación multiinquilino.
>- Al registrar aplicaciones de línea de negocio de inquilino único, los usuarios no pueden solicitar permisos de solo aplicación para otras aplicaciones.
>- Al registrar aplicaciones de línea de negocio de inquilino único, los usuarios no pueden solicitar permisos delegados para otras aplicaciones si esos permisos requieren el consentimiento del administrador.
>- Los usuarios no pueden realizar cambios en las aplicaciones de las que no son propietarios.



- Puede controlar si los usuarios pueden por sí mismos agregar aplicaciones previamente integradas que utilizan el inicio de sesión único con contraseña (proceso también conocido como "almacenamiento de contraseña") ![](media/active-directory-apps-permissions-consent/apps10.png)



- Puede controlar en qué condiciones se puede tener acceso a las aplicaciones (es decir, un acceso condicional). Tenga en cuenta que esto se aplica a la aplicación cliente y a la aplicación de recurso. Por tanto, supongamos que establece una directiva de acceso condicional que indica que solo se puede acceder a la aplicación "Office 365 Exchange Online" desde máquinas que sean compatibles.  Esta directiva también se activará si un usuario intenta usar una aplicación cliente que solicita permisos para Exchange Online.



- De esta forma, tiene visibilidad para ver a qué aplicaciones se le ha dado consentimiento y cuáles se están usando.

1.  Cuando un usuario da su consentimiento a una aplicación, se crea un objeto ServicePrincipal en el inquilino. La creación de ServicePrincipal se incluye en el informe de auditoría.
2.  Los informes de actividad de inicio de sesión del usuario le indican en qué aplicación está iniciando sesión el usuario. 

## <a name="example"></a>Ejemplo

Por ejemplo, tomemos la aplicación "FabrikamMail para Office 365", en la que ha observado que los usuarios de su inquilino están iniciando sesión. "FabrikamMail" es una aplicación de lector de correo electrónico para Android, publicada por "Fabrikam, Inc.". Esta aplicación puede incluirse en la categoría de "aplicaciones multiinquilino que desarrollan otros, a las que Contoso puede dar consentimiento".

Si se permite que los usuarios pueden dar consentimiento, estos recibirán una solicitud de consentimiento la primera vez que inicien sesión: ![](media/active-directory-apps-permissions-consent/apps14.png)

"Acceso a los buzones" es la cadena de consentimiento del usuario para el permiso "Acceso a los buzones como el usuario que inició sesión" expuesto por "Office 365 Exchange Online" (es decir, Exchange).

Puede ver los permisos. Para ello, busque el objeto ServicePrincipal de Exchange (el recurso), que se agregó al suscribirse a Office 365. Puede pensar en el objeto ServicePrincipal como una "instancia" de la aplicación en su inquilino que se usa para registrar las diferentes opciones y configuraciones.  Se puede ver mediante `Get-AzureADServicePrincipal` en PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

El consentimiento se inicia cuando el usuario hace clic en "Aceptar". En primer lugar, se crea un objeto ServicePrincipal para "FabrikamMail para Office 365" en el inquilino. El objeto ServicePrincipal tiene un aspecto parecido a este:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

El consentimiento a una aplicación creará un vínculo Oauth2PermissionGrant entre lo siguiente:
  
- el objeto de usuario
- el objeto ServicePrincipalName de las aplicaciones cliente (SPN)
- el objeto ServicePrincipalName de las aplicaciones de recursos (SPN)
- los permisos de la aplicación de recursos.  

En el caso de FabrikamMail, se parece a esto:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** es el identificador de objeto de la entidad de servicio de FabrikamMail (que se acaba de crear), **PrincipalId** es el identificador de objeto del usuario (el usuario que ha dado consentimiento), **ResourceId** es el identificador de objeto de la entidad de servicio de Exchange, y Scope es el permiso de Exchange para el que se ha dado consentimiento.

Si no se permite a los usuarios dar consentimiento, verá una pantalla que indica que se necesita permiso.

