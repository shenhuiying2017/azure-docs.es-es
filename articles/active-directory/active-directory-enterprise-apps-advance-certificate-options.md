---
title: Opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory | Microsoft Docs
description: Aprenda a usar opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: es-es
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opciones avanzadas de firma de certificados en el token SAML para aplicaciones previamente integradas en Azure Active Directory
Hoy en día, Azure Active Directory admite miles de aplicaciones previamente integradas en la galería de aplicaciones de Azure AD, de las cuales más de 500 admiten el inicio de sesión único mediante el protocolo SAML 2.0. Cuando un usuario se autentica en una aplicación a través de Azure AD con SAML, Azure AD envía un token a la aplicación (mediante HTTP POST). A continuación, la aplicación valida y usa el token para que el usuario inicie sesión en lugar de solicitar un nombre de usuario y una contraseña. Estos tokens SAML se firman con el certificado único que se genera en Azure AD. Este token SAML se firma con los algoritmos estándares concretos.

Azure Active Directory usa algunos de los valores predeterminados para las aplicaciones de la galería. Los valores predeterminados se configuran según el requisito de la aplicación.

Azure Active Directory admite la configuración avanzada de la firma de certificados. Para seleccionar estas opciones, active primero la casilla **SAML advance certificate signing settings** (Configuración avanzada de firma de certificados SAML) como se muestra a continuación.

![Opciones de firma de certificado][1]

Una vez que se activa esta casilla, puede configurar los valores de **Certificate signing options** (Opciones de firma de certificado) y **Certificate signing algorithm** (Algoritmo de firma de certificado).

## <a name="certificate-signing-options"></a>Opciones de firma de certificado

Estos son los tres tipos de opciones de firma de certificado que Azure AD admite.

1. **Firmar aserción SAML**: se trata de la opción predeterminada establecida para la mayoría de las aplicaciones de la galería. Si se selecciona esta opción, Azure AD como punto de distribución de emisión firma las aserciones SAML con el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable a continuación.

2. **Firmar respuesta SAML**: si se selecciona esta opción, Azure AD como punto de distribución de emisión firma la respuesta SAML con el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable a continuación.

3. **Firmar respuesta y aserción SAML**: si se selecciona esta opción, Azure AD como punto de distribución de emisión firma todo el token SAML con el certificado X509 de la aplicación. También utiliza el algoritmo de firma, que se selecciona en la lista desplegable a continuación.

    ![Opciones de firma de certificado][4]

## <a name="certificate-signing-algorithm"></a>Algoritmo de firma de certificado

Azure Active Directory admite dos tipos de algoritmo de firma para firmar la respuesta SAML.

1. SHA256: es el algoritmo predeterminado que Azure Active Directory utiliza para firmar la respuesta SAML. Es el más reciente y se trata como más seguro que SHA1. La mayor parte de las aplicaciones admiten el algoritmo SHA256. Si la aplicación solo admite Sha1 como algoritmo de firma, puede cambiarlo. En caso contrario, se recomienda utilizar el algoritmo SHA256 para firmar la respuesta SAML.

    ![Algoritmo de firma de certificado SHA256][3]

2. SHA1: es el algoritmo antiguo y no se trata como seguro. Si la aplicación admite solo este algoritmo de firma, puede seleccionar esta opción en la lista desplegable. Con esta opción, Azure AD firma la respuesta SAML con el algoritmo Sha1.

    ![Algoritmo de firma de certificado SHA1][2]

## <a name="next-steps"></a>Pasos siguientes
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](active-directory-saas-custom-apps.md)
* [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
