---
title: Opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory | Microsoft Docs
description: Aprenda a usar opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: cec377ad92e1491413f9d3ebc953abb154b83f01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory
En la actualidad Azure Active Directory (Azure AD) es compatible con miles de aplicaciones previamente integradas de la galería de aplicaciones de Azure Active Directory. Entre ellas se incluyen más de 500 aplicaciones que admiten el inicio de sesión único mediante el protocolo SAML 2.0. Cuando un usuario se autentica en una aplicación a través de Azure AD con SAML, Azure AD envía un token a la aplicación (mediante HTTP POST). Después, la aplicación valida y usa el token para que el usuario inicie sesión en lugar de solicitar un nombre de usuario y una contraseña. Estos tokens SAML se firman con el certificado único que se genera en Azure AD y mediante algoritmos estándar específicos.

Azure AD usa algunos de los valores predeterminados para las aplicaciones de la galería. Los valores predeterminados se configuran según los requisitos de la aplicación.

Azure AD admite la configuración avanzada de firma de certificado. Para seleccionar estas opciones, active primero la casilla **SAML advance certificate signing settings** (Configuración avanzada de firma de certificados SAML):

![Mostrar configuración avanzada de firma de certificado](./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png)

Una vez que se activa esta casilla, puede configurar las opciones de firma de certificado y el algoritmo correspondiente.

## <a name="certificate-signing-options"></a>Opciones de firma de certificado

Azure AD admite tres opciones avanzadas de firma de certificado:

* **Firmar aserción SAML** Esta opción predeterminada se establece para la mayoría de las aplicaciones de la galería. Si se selecciona esta opción, Azure AD como IdP firma la aserción SAML y el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable **Algoritmo de firma**.

* **Firmar respuesta SAML** Si se selecciona esta opción, Azure AD como IdP firma la respuesta SAML con el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable **Algoritmo de firma**.

* **Firmar respuesta y aserción SAML** Si se selecciona esta opción, Azure AD como IdP firma la respuesta SAML con el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable **Algoritmo de firma**.

    ![Opciones de firma de certificado](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algoritmos de firma de certificado

Azure AD admite dos algoritmos de firmas para firmar la respuesta SAML:

* **SHA-256**. Azure AD utiliza este algoritmo predeterminado para firmar la respuesta SAML. Es el algoritmo más reciente y se considera más seguro que SHA-1. La mayoría de las aplicaciones admiten el algoritmo SHA-256. Si una aplicación solo admite SHA-1 como algoritmo de firma, puede cambiarlo. En caso contrario, se recomienda utilizar el algoritmo SHA-256 para firmar la respuesta SAML.

    ![Algoritmo de firma de certificado SHA-256](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png)

* **SHA-1**. Es el algoritmo antiguo y se considera como menos seguro que SHA-256. Si la aplicación admite solo este algoritmo de firma, puede seleccionar esta opción en la lista desplegable **Algoritmo de firma**. Azure AD firma entonces la respuesta SAML con el algoritmo SHA-1.

    ![Algoritmo de firma de certificado SHA-1](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Pasos siguientes
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la galería de aplicaciones de Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Solución de problemas del inicio de sesión único basado en SAML](develop/active-directory-saml-debugging.md)


