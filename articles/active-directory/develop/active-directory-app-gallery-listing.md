---
title: "Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory"
description: "Cómo mostrar una aplicación que admite el inicio de sesión único en la galería de Azure Active Directory | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
translationtype: Human Translation
ms.sourcegitcommit: 28712a9898bf2449de53d21fd87f1ce53da71ff8
ms.openlocfilehash: df60c4737954e79ff860601412dffa58d4dbb471
ms.lasthandoff: 02/11/2017


---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory
Para anunciar una aplicación compatible con inicio de sesión único con Azure Active Directory en la [galería de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), la aplicación primero debe implementar uno de los siguientes modos de integración:

* **OpenID Connect** : integración directa con Azure AD mediante OpenID Connect para autenticación y la API de consentimiento de Azure AD para configuración. Si acaba de iniciar una integración y la aplicación no es compatible con SAML, este es el modo recomendado.
* **SAML** : la aplicación ya tiene la capacidad de configurar proveedores de identidad de terceros mediante el protocolo SAML.

A continuación se enumeran los requisitos para cada modo.

## <a name="openid-connect-integration"></a>Integración mediante OpenID Connect
Para integrar la aplicación con Azure AD, siga la [instrucciones para desarrolladores](active-directory-authentication-scenarios.md). Después, complete la siguiente información y envíela a waadpartners@microsoft.com.

* Proporcione credenciales para una cuenta o inquilino de prueba con la aplicación que el equipo de Azure AD puede usar para probar la integración.  
* Proporcione instrucciones sobre cómo el equipo de Azure AD puede iniciar sesión y conectarse a una instancia de Azure AD para la aplicación mediante el [marco de consentimiento de Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework). 
* Proporcione las instrucciones que sean necesarias para que el equipo de Azure AD pruebe el inicio de sesión único con la aplicación. 
* Proporcione la información siguiente:

> Nombre de la empresa:
> 
> Sitio web de la empresa:
> 
> Nombre de la aplicación:
> 
> Descripción de la aplicación (límite de&256; caracteres):
> 
> Sitio web de la aplicación (informativo):
> 
> Sitio web de soporte de técnico de la aplicación o información de contacto:
> 
> Identificador de aplicación de la aplicación, tal y como se muestra en los detalles de la aplicación en https://portal.azure.com:
> 
> Dirección URL de  suscripción a una aplicación donde van los clientes para suscribirse o adquirir la aplicación:
> 
> Elija hasta tres categorías donde desea que se anuncie la aplicación (para ver las categorías disponibles, consulte Azure Active Directory Marketplace):
> 
> Adjunte un icono pequeño de la aplicación (archivo PNG, 45 px por 45 px, color de fondo sólido):
> 
> Adjunte un icono grande de la aplicación (archivo PNG, 215 px por 215 px, color de fondo sólido):
> 
> Adjunte el logotipo de la aplicación (archivo PNG, 150 px por 122 px, color de fondo transparente):
> 
> 

## <a name="saml-integration"></a>Integración SAML
Cualquier aplicación compatible con SAML 2.0 se puede integrar directamente con un inquilino de Azure AD mediante [estas instrucciones para agregar una aplicación personalizada](../active-directory-saas-custom-apps.md). Una vez que haya probado que la integración de la aplicación funciona con Azure AD, envíe la información siguiente a <mailto:waadpartners@microsoft.com>.

* Proporcione credenciales para una cuenta o inquilino de prueba con la aplicación que el equipo de Azure AD puede usar para probar la integración.  
* Proporcione los valores de dirección URL de inicio de sesión de SAML, de dirección URL del emisor (identificador de entidad) y de dirección URL de respuesta (servicio de consumidor de aserciones) para la aplicación, tal y como se describe [aquí](../active-directory-saas-custom-apps.md). Si normalmente proporciona estos valores como parte de un archivo de metadatos SAML, envíelos también.
* Proporcione una breve descripción de cómo configurar Azure AD como proveedor de identidades en la aplicación mediante SAML 2.0. Si la aplicación admite la configuración de Azure AD como proveedor de identidades a través de un portal administrativo de autoservicio, asegúrese de que las credenciales proporcionadas anteriormente incluyen la capacidad para configurar esto.
* Proporcione la información siguiente:

> Nombre de la empresa:
> 
> Sitio web de la empresa:
> 
> Nombre de la aplicación:
> 
> Descripción de la aplicación (límite de&256; caracteres):
> 
> Sitio web de la aplicación (informativo):
> 
> Sitio web de soporte de técnico de la aplicación o información de contacto:
> 
> Dirección URL de  suscripción a una aplicación donde van los clientes para suscribirse o adquirir la aplicación:
> 
> Elija hasta tres categorías donde quiere que se anuncie la aplicación (para ver las categorías disponibles, vea [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Adjunte un icono pequeño de la aplicación (archivo PNG, 45 px por 45 px, color de fondo sólido):
> 
> Adjunte un icono grande de la aplicación (archivo PNG, 215 px por 215 px, color de fondo sólido):
> 
> Adjunte el logotipo de la aplicación (archivo PNG, 150 px por 122 px, color de fondo transparente):
> 
> 


