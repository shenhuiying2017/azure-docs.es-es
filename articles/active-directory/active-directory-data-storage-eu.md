---
title: Dónde almacena Azure AD los datos de identidad de los clientes europeos | Microsoft Docs
description: Obtenga información sobre dónde almacena Microsoft Azure Active Directory los datos relacionados con la identidad de los clientes europeos.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 19dc163dbb6dd296a417f5c313a36c7f7c9e50d7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305690"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Dónde almacena Microsoft Azure Active Directory (Azure AD) los datos de identidad de los clientes europeos
Azure AD facilita la administración de las identidades de los usuarios y la creación de directivas de acceso basadas en inteligencia para proteger los recursos de su organización. Los datos de identidad se almacenan en una ubicación que se basa la dirección que su organización proporcionó al suscribirse al servicio. Por ejemplo, al suscribirse a Office 365 o Azure. Para obtener información específica sobre dónde se almacenan los datos de identidad, puede consultar la sección [Where is your data located?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) (Dónde se encuentran sus datos) de Microsoft Trust Center.

Si bien la mayoría de los datos de identidad europeos relacionados con Azure AD permanecen en centros de datos europeos, hay cinco atributos relacionados con el usuario que normalmente se almacenan en los centros de datos de Estados Unidos. Estos atributos son GivenName, Surname, userPrincipalName, Domain y PasswordHash. El atributo PasswordHash puede ser una excepción y no almacenarse en EE. UU. si alguien utiliza un método de autenticación federada local que evita que el valor de PasswordHash se sincronice con Azure AD. Además, hay algunos datos operativos específicos del servicio, que se necesitan para el funcionamiento normal de Azure AD, que se almacenan en Estados Unidos y no incluyen ningún dato personal.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Datos almacenados fuera de los centros de datos europeos para los clientes europeos

La mayoría de los datos de identidad europeos relacionados con Azure AD, para organizaciones con direcciones basadas en Europa, se mantiene en centros de datos europeos. Entre los datos de Azure AD que no se almacenan en centros de datos europeos, se incluyen:

- **Atributos relacionados con la identidad**

    Los siguientes atributos relacionadas con la identidad se replicarán en Estados Unidos:

    - GivenName
    - Surname
    - userPrincipalName
    - Dominio
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Autenticación multifactor (MFA) de Microsoft Azure y restablecimiento de contraseña de autoservicio (SSPR) de Azure AD**
    
    MFA almacena todos los datos de usuario en reposo en centros de datos europeos. Sin embargo, algunos datos específicos del servicio de MFA se almacenan EE. UU., incluyendo:
    
    - Los datos de autenticación en dos fases y los datos personales relacionados podrían almacenarse en EE. UU si utiliza MFA o SSPR.
        - Si la autenticación en dos fases usa llamadas telefónicas o SMS, puede realizarse a través de operadores estadounidenses.
        - Las notificaciones push que usen la aplicación Microsoft Authenticator requieren notificaciones del servicio de notificaciones del fabricante (Apple o Google), que podría estar fuera de Europa.
        - Los códigos OATH se validan siempre en EE. UU. 
    - Algunos registros de MFA y SSPR se almacenan en EE. UU. durante 30 días, independientemente del tipo de autenticación.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C almacena todos los datos de usuario en reposo en centros de datos europeos. Sin embargo, los registros operativos (de los que se han quitado los datos personales) permanecen en la ubicación desde donde la persona obtiene acceso a los servicios. Por ejemplo, si un usuario de B2C accede al servicio en Estados Unidos, los registros operativos permanecen en EE. UU. Además, todos los datos de configuración de directivas que no contengan datos personales se almacenan solo en EE. UU. Para obtener más información sobre las configuraciones de directivas, consulte el artículo [Azure Active Directory B2C: directivas integradas](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B almacena todos los datos de usuario en reposo en centros de datos europeos. Sin embargo, B2B almacena sus metadatos de carácter no personal en tablas en centros de datos de EE. UU. Estas tablas incluyen campos como redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl e InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS almacena los datos de usuario en la misma ubicación que la instancia de Azure Virtual Network que seleccionó el cliente. Por lo tanto, si la red está fuera de Europa, los datos se replican y almacenan fuera de Europa.

- **Servicios y aplicaciones integrados en Azure AD**

    Los servicios y las aplicaciones que se integran en Azure AD tienen acceso a datos de identidad. Evalúe cada servicio y aplicación para conocer la manera en que cada servicio y aplicación específico procesa los datos de identidad, y si cumple los requisitos de almacenamiento de datos de la empresa.

    Para obtener más información sobre la residencia de los datos de los servicios de Microsoft, consulte la sección [Where is your data located?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) (Dónde se encuentran sus datos) de Microsoft Trust Center.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cualquiera de las características y funcionalidades que se han descrito anteriormente, consulte estos artículos.
- [Introducción a Azure Active Directory](get-started-azure-ad.md)
- [¿Qué es Multi-Factor Authentication?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Autoservicio de restablecimiento de contraseña de Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [¿Qué es Azure Active Directory B2C?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [¿Qué es la colaboración de Azure AD B2B?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
