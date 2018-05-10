---
title: Guía de publicación técnica de aplicaciones SaaS de Azure Marketplace
description: Guía paso a paso y listas de comprobación de publicación para la publicación de aplicaciones SaaS en Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: eb6db45ca0fcb6879aeaeaaf70715691cac438b0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>Guía de publicación técnica de aplicaciones SaaS

Bienvenido a la Guía de publicación técnica de aplicaciones SaaS de Azure Marketplace. Esta guía está diseñada para ayudar a los candidatos y a los anunciantes existentes a mostrar sus aplicaciones y servicios en Azure Marketplace mediante la oferta de aplicaciones SaaS.  
Quiere utilizar la oferta de aplicaciones SaaS cuando se implementa la solución en su propia suscripción de Azure y los clientes inician sesión mediante una interfaz que usted diseña y administra para probar la aplicación. Para ello, se utiliza [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) para aprovechar el entorno de prueba existente. En otras palabras, es una evaluación gratuita dirigida por el cliente y hospedada por un asociado. Es fundamental para mostrar la solución de forma que los compradores de la nube tengan la oportunidad de experimentar la solución de forma independiente y sin costo alguno, por lo que este tipo de oferta proporciona una experiencia de prueba que se ajusta a la forma en que los clientes buscan soluciones en la nube.  

Para obtener información general de todas las demás ofertas de Marketplace, consulte la [Guía del anunciante de Marketplace](https://aka.ms/sellerguide).

## <a name="saas-application-technical-guidance"></a>Guía técnica de aplicaciones SaaS
Los requisitos técnicos para las aplicaciones SaaS son simples. Los anunciantes solo tienen que integrarse con Azure AD para su publicación.  La integración de Azure AD con aplicaciones está bien documentada y Microsoft proporciona varios SDK y recursos para lograr esto.  

Para empezar, se recomienda contar con una suscripción dedicada a la publicación en Azure Marketplace, lo que permitirá aislar el trabajo de otras iniciativas. Además, si aún no está instalado, le recomendamos que disponga de las siguientes herramientas como parte de su entorno de desarrollo: 
- [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.0.0)  
- [Herramientas de desarrollo de Azure (revisar qué está disponible)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>Recursos
Las siguientes listas proporcionan vínculos a los mejores recursos de Azure AD para comenzar: 

**Documentación**

- [Guía del desarrollador de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

- [Integración con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

- [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

- [Hoja de ruta de Azure: seguridad e identidad](https://azure.microsoft.com/roadmap/?category=security-identity)

**Vídeos**

- [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticación de Azure Active Directory con Vittorio Bertocci)

- [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 1 de 2)

- [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 2 de 2)

- [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Creación de aplicaciones con Microsoft Azure Active Directory)

- [Vídeos de Microsoft Azure sobre Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**Aprendizaje**  
- [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965) (Serie de contenido de Microsoft Azure para profesionales de IT: Azure Active Directory)

**Actualizaciones de servicios de Azure Active Directory**  
- [Actualizaciones de servicios de Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Para obtener soporte técnico, puede usar los siguientes recursos:
- [Foros de MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [Stackoverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Galería de Azure Active Directory
Además de tener su aplicación mostrada en Azure Marketplace/AppSource, también puede tener su aplicación en la galería de aplicaciones de Azure AD, que forma parte de la AppStore de Azure Marketplace. Los clientes que usan Azure AD como proveedor de identidades pueden encontrar publicados aquí diferentes conectores de aplicaciones SaaS. Los administradores de TI pueden agregar los conectores desde la galería de aplicaciones, y los configuran y usan para poder realizar el inicio de sesión único (SSO) y el aprovisionamiento. Azure AD admite todos los principales protocolos de federación de inicio de sesión único, como SAML 2.0, OpenID Connect, OAuth y WS-Fed.  

Cuando haya comprobado que la integración de aplicaciones funciona con Azure AD, envíe la solicitud de acceso en nuestro portal de red de aplicaciones. Si tiene una cuenta de Office 365, utilícela para iniciar sesión en este portal. Si no tiene una cuenta de Office 365, puede usar su cuenta Microsoft (como Outlook o Hotmail) para iniciar sesión.

## <a name="program-benefits"></a>Beneficios del programa
- Proporciona la mejor experiencia de inicio de sesión único a los clientes.
- Permite una configuración simple y mínima de la aplicación.
- Los clientes pueden buscar la aplicación y encontrarla en la galería.
- Cualquier cliente puede usar esta integración con independencia de la versión de la SKU de Azure AD (Gratis, Básica o Premium).
- Facilita un tutorial de configuración paso a paso para nuestros clientes mutuos.
- Permite al usuario habilitar el aprovisionamiento de usuarios para la misma aplicación si se usa SCIM.

## <a name="prerequisites"></a>requisitos previos
Para agregar una aplicación a la galería de Azure AD, la aplicación primero debe implementar uno de los protocolos de federación que admite Azure AD. Lea los términos y condiciones de la galería de aplicaciones de Azure AD ubicada en [Información legal de Microsoft Azure](https://azure.microsoft.com/support/legal/).  

A continuación se describe información adicional de los requisitos previos en función del protocolo que se esté utilizando:

**OpenID Connect**: cree la aplicación multiinquilino en Azure AD e implemente la plataforma de consentimiento para la aplicación. Envíe la solicitud de inicio de sesión a un punto de conexión común para que cualquier cliente pueda proporcionar su consentimiento a la aplicación. Puede controlar el acceso de usuario del cliente en función del identificador del inquilino y el UPN del usuario que se recibieron en el token.  
**SAML 2.0 o WS-Fed**: la aplicación debe tener la funcionalidad de realizar la integración de SSO de SAML/WS-Fed en modo SP o IdP.
