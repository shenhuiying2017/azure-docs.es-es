---
title: "Depuración del inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory | Microsoft Docs"
description: "Obtenga información acerca de cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory  "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 7aa7ca90f9098f30565524470ca23783e97195e0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory
Cuando se depura una integración de la aplicación basada en SAML, a menudo resulta útil usar una herramienta como [Fiddler](http://www.telerik.com/fiddler) para ver la solicitud SAML, la respuesta de SAML y el token SAML real que se emite a la aplicación. Mediante el examen del token SAML, puede asegurarse de que todos los atributos necesarios, el nombre de usuario del SAML Subject y el URI del emisor se logren del modo esperado.

![][1]

Normalmente, la respuesta de Azure AD que contiene el token SAML es la que se produce después de un redireccionamiento HTTP 302 desde https://login.windows.net, y se envía a la **URL de respuesta** configurada de la aplicación. 

Para ver el token SAML, seleccione esta línea y, luego, seleccione la pestaña **Inspectors > WebForms** (Inspectores > WebForms) en el panel derecho. Desde ahí, haga clic con el botón derecho en el valor **SAMLResponse** y seleccione **Send to TextWizard** (Enviar a TextWizard). Después, seleccione **From Base64** (De Base64) en el menú **Transform** (Transformar) para descodificar el token y ver su contenido.

**Nota**: Para ver el contenido de esta solicitud HTTP, es posible que Fiddler le solicite configurar el cifrado del tráfico HTTPS, que tendrá que llevar a cabo.

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](../active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png