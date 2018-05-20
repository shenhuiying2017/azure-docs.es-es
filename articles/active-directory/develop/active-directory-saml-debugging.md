---
title: Depuración del inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory | Microsoft Docs
description: 'Obtenga información acerca de cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory

Cuando se depura una integración de la aplicación basada en SAML, a menudo resulta útil usar una herramienta como [Fiddler](http://www.telerik.com/fiddler) para ver la solicitud SAML y la respuesta de SAML que contiene el token SAML que se emitió a una aplicación. 

![Fiddler][1]

Frecuentemente, los problemas están relacionados con una configuración incorrecta de Azure Active Directory o de la aplicación. Dependiendo de dónde vea el error, tendrá que revisar la solicitud SAML o la respuesta:

- Aparece un error en la página de inicio de sesión de mi empresa [vínculos a la sección]
- Aparece un error en la página de la aplicación después de iniciar sesión [vínculos a la sección]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Aparece un error en la página de inicio de sesión de mi empresa.

Puede encontrar una relación directa entre el código de error y los pasos para su resolución en [Problemas al iniciar sesión en una aplicación de la galería configurada para inicio de sesión único federado](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Si aparece un error en la página de inicio de sesión de la empresa, necesita el mensaje de error y la solicitud SAML para depurar el problema.

### <a name="how-can-i-get-the-error--message"></a>¿Cómo se puede obtener el mensaje de error?

Para obtener el mensaje de error, mire en la esquina inferior derecha de la página. Verá un error que incluye:

- Un identificador de correlación
- Una marca de tiempo
- Un mensaje

![Error][2] 

 
El identificador de correlación y la marca de tiempo forman un identificador único que se puede usar para buscar los registros de back-end asociados con el error de inicio de sesión. Estos valores son importantes cuando se crea un caso de soporte técnico con Microsoft ya que ayudan a los ingenieros a proporcionar una solución más rápida al problema.

El mensaje indica la causa principal del problema de inicio de sesión. 


### <a name="how-can-i-review-the-saml-request"></a>¿Cómo puedo revisar la solicitud SAML?

La solicitud SAML que envía la aplicación a Azure Active Directory suele ser la última respuesta HTTP 200 de [https://login.microsoftonline.com](https://login.microsoftonline.com).
 
Con Fiddler, puede ver el token SAML seleccionando esta línea y, posteriormente, seleccionando la pestaña **Inspectors > WebForms** (Inspectores > WebForms) en el panel derecho. Haga clic con el botón derecho en el valor **SAMLRequest** y seleccione **Send to TextWizard** (Enviar a TextWizard). Después, seleccione **From Base64** (De Base64) en el menú **Transform** (Transformar) para descodificar el token y ver su contenido. 

Además, también puede copiar el valor de SAMLrequest y utilizar otro descodificador de Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Con la solicitud SAML descodificada, revise lo siguiente:

1. El **destino** de la solicitud SAML se corresponde con la dirección URL del servicio de inicio de sesión único de SAML que se obtiene de Azure Active Directory.
 
2. El **emisor** de la solicitud SAML es el mismo **identificador** que ha configurado para la aplicación en Azure Active Directory. Azure AD usa al emisor para buscar una aplicación en el directorio.

3. **AssertionConsumerServiceURL** es la dirección en la que la aplicación espera recibir el token SAML de Azure Active Directory. Puede configurar este valor en Azure Active Directory pero no es obligatorio si forma parte de la solicitud SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Aparece un error en la página de la aplicación después de iniciar sesión

En este escenario, la aplicación no acepta la respuesta que emite Azure AD. Es importante que compruebe la respuesta de Azure AD que contiene el token SAML con el proveedor de la aplicación para averiguar lo que falta o es incorrecto. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>¿Cómo puedo obtener y revisar la respuesta SAML?

Normalmente, la respuesta de Azure AD que contiene el token SAML es la que se produce después de un redireccionamiento HTTP 302 desde https://login.microsoftonline.com, y se envía a la **URL de respuesta** configurada de la aplicación. 

Para ver el token SAML, seleccione esta línea y, luego, seleccione la pestaña **Inspectors > WebForms** (Inspectores > WebForms) en el panel derecho. Desde ahí, haga clic con el botón derecho en el valor **SAMLResponse** y seleccione **Send to TextWizard** (Enviar a TextWizard). Después, seleccione **From Base64** (De Base64) en el menú **Transform** (Transformar) para descodificar el token y ver su contenido. 

También puede copiar el valor de **SAMLrequest** y utilizar otro descodificador de Base64.

Visite la guía de solución de problemas [Error en la página de la aplicación después de iniciar sesión](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) para más información sobre lo que puede que falte o que sea incorrecto en la respuesta SAML.

Para más información sobre cómo revisar la respuesta SAML visite el artículo [Protocolo SAML de inicio de sesión único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](../active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
