---
title: "Introducción al servidor web de ASP.NET de Azure AD v2: prueba | Microsoft Docs"
description: "Implementación de inicio de sesión de Microsoft en una solución ASP.NET con una aplicación basada en un explorador web tradicional mediante el estándar OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 77030e3e6375682c416d99ef22d754b908ad484d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
## <a name="test-your-code"></a>Prueba del código

Presione `F5` para ejecutar el proyecto en Visual Studio. El explorador se abrirá y le dirigirá a *http://localhost:{port}* donde verá el botón *Iniciar sesión en Microsoft*. Continúe y haga clic en él para iniciar sesión.

Cuando esté listo para realizar la prueba, use una cuenta profesional o educativa (Azure Active Directory) o una cuenta personal (live.com, outlook.com) para iniciar sesión. 

![Ventana Iniciar sesión en Microsoft del explorador](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Ventana Iniciar sesión en Microsoft del explorador](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Resultados esperados
Después de iniciar sesión, se redirige al usuario a la página principal del sitio web correspondiente a la dirección URL HTTPS especificada en la información de registro de la aplicación en el Portal de registro de aplicaciones de Microsoft. Esta página muestra ahora *Hola, {usuario}* y un vínculo para cerrar sesión, así como un vínculo para ver las notificaciones del usuario (que es un vínculo al controlador de autorización creado anteriormente).

### <a name="see-users-claims"></a>Ver notificaciones del usuario
Seleccione el hipervínculo para ver la notificaciones del usuario. Esto le lleva hasta el controlador y una vista que solo está disponible para los usuarios autenticados.

#### <a name="expected-results"></a>Resultados esperados
 Debe ver una tabla que contiene las propiedades básicas del usuario que ha iniciado sesión:

| Propiedad | Valor | Descripción|
|---|---|---|
| Nombre | {Nombre completo del usuario} | Nombre y apellido del usuario
|Nombre de usuario | <span>user@domain.com</span>| Nombre de usuario utilizado para identificar al usuario
| Asunto| {Firmante}|Cadena que identifica de forma única el inicio de sesión de usuario en la web|
| Id. de inquilino| {Guid}| *guid* que representa de forma única la organización de Azure Active Directory del usuario.|

Además, verá una tabla con todas las notificaciones de usuario incluidas en la solicitud de autenticación. Para obtener una lista de todas las notificaciones de un token de identificador y una explicación, vea este [artículo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Probar el acceso a un método que tiene un atributo *[Authorize]* (opcional)
En este paso, probará el acceso al controlador autenticado como usuario anónimo:<br/>
Seleccione el vínculo al cierre de sesión del usuario y complete el proceso de cierre de sesión.<br/>
Ya en el explorador, escriba http://localhost:{port}/authenticated para acceder al controlador que está protegido con el atributo `[Authorize]`.

#### <a name="expected-results"></a>Resultados esperados
Debe recibir el mensaje que le pide que se autentique para ver la vista.

## <a name="additional-information"></a>Información adicional

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteger todo el sitio web
Para proteger todo el sitio web, agregue `AuthorizeAttribute` a `GlobalFilters` en el método `Global.asax` `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Restricción para que los usuarios de una sola organización sean los únicos que pueden iniciar sesión en la aplicación**

> De manera predeterminada, las cuentas personales (como outlook.com, live.com y otras), así como las cuentas profesionales y educativas de cualquier compañía u organización que se haya integrado con Azure Active Directory pueden iniciar sesión en la aplicación. 

> Si quiere que la aplicación solo acepte inicios de sesión de una organización de Azure Active Directory, reemplace el parámetro `Tenant` en el archivo *web.config* de `Common` por el nombre del inquilino de la organización; por ejemplo, *contoso.onmicrosoft.com*. Después, cambie el argumento `ValidateIssuer` de la *Clase de inicio OWIN* a `true`.

> Para solo permitir usuarios de una lista de organizaciones específicas, establezca `ValidateIssuer` en true y use el parámetro `ValidIssuers` para especificar una lista de las organizaciones.

> Otra posibilidad es implementar un método personalizado para validar los emisores con el parámetro IssuerValidator. Para más información sobre `TokenValidationParameters`, consulte [este](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) artículo de MSDN.


