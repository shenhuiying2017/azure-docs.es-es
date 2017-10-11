---
title: Directivas de dispositivos de acceso condicional de Azure Active Directory para servicios de Office 365 | Microsoft Docs
description: "Obtenga información sobre cómo aprovisionar directivas de dispositivos de acceso condicional para que los recursos corporativos sean más seguros, a la vez que mantiene el cumplimiento del usuario y el acceso a los servicios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 10a4a23af08c31ea107e196ae441fefd39c7cabc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Directivas de dispositivos de acceso condicional de Active Directory para servicios de Office 365

El acceso condicional necesita varios elementos para su funcionamiento. Implica un usuario con autenticación multifactor, un dispositivo autenticado y un dispositivo compatible, entre otros factores. Este artículo se centra principalmente en las condiciones basadas en los dispositivos que puede usar su organización para que pueda controlar el acceso a los servicios de Office 365. 

Los usuarios corporativos quieren tener acceso a servicios de Office 365 como Exchange y SharePoint Online en el trabajo o en la escuela desde sus dispositivos personales, mientras que usted quiere que el acceso sea seguro. Puede aprovisionar directivas de dispositivos de acceso condicional para que los recursos corporativos sean más seguros, a la vez que concede acceso a servicios para los usuarios que usen dispositivos compatibles. Puede establecer directivas de acceso condicional a Office 365 en el portal de acceso condicional de Microsoft Intune.

Azure Active Directory (Azure AD) aplica las directivas de acceso condicional para proteger el acceso a los servicios de Office 365. Puede crear una directiva de acceso condicional que impida que un usuario que tenga un dispositivo no compatible pueda obtener acceso a un servicio de Office 365. El usuario debe cumplir las directivas de dispositivos de la empresa para que se le conceda acceso al servicio. Como alternativa, puede crear una directiva que requiera que los usuarios inscriban sus dispositivos para obtener acceso a un servicio de Office 365. Las directivas se pueden aplicar a todos los usuarios de una organización, o bien se pueden limitar a algunos grupos de destino. Con el tiempo puede agregar más grupos de destino a una directiva.

Como requisito previo para la aplicación de directivas de dispositivos, los usuarios deben registrar sus dispositivos en el servicio de registro de dispositivos de Azure AD. Puede optar por activar la autenticación multifactor para los dispositivos registrados en el servicio de registro de dispositivos de Azure AD. Se recomienda el uso de la autenticación multifactor para el servicio de registro de dispositivos de Azure Active Directory. Al activar la autenticación multifactor, los usuarios que registren sus dispositivos en el servicio de registro de dispositivos de Azure AD deben someterse a la autenticación de segundo factor.

## <a name="how-does-a-conditional-access-policy-work"></a>¿Cómo funcionan las directivas de acceso condicional?

Cuando un usuario solicita acceso a un servicio de Office 365 desde una plataforma de dispositivos compatible, Azure AD autentica el usuario y el dispositivo. Azure AD concede acceso al servicio únicamente si el usuario cumple la directiva establecida para el servicio. Los usuarios cuyos dispositivos no estén inscritos reciben instrucciones en las que se explica cómo inscribirse y obtener compatibilidad para tener acceso a los servicios corporativos de Office 365. Los usuarios de dispositivos iOS y Android deben inscribir sus dispositivos mediante la aplicación del portal de empresa de Intune. Cuando un usuario inscribe un dispositivo, este se registra en Azure AD y se inscribe para el cumplimiento y la administración de dispositivos. Debe usar el servicio de registro de dispositivos de Azure AD junto con Microsoft Intune para habilitar la administración de dispositivos móviles para los servicios de Office 365. La inscripción de dispositivos es necesaria para que los usuarios tengan acceso a los servicios de Office 365 cuando se aplican directivas de dispositivos.

Cuando un usuario inscribe correctamente un dispositivo, este pasa a ser de confianza. Azure AD proporciona al usuario autenticado acceso de inicio de sesión único a las aplicaciones de empresa. Azure AD aplica una directiva de acceso condicional para conceder acceso a un servicio no solo la primera vez que el usuario solicita acceso, sino cada vez que el usuario renueva una solicitud de acceso. Al usuario se le deniega el acceso a los servicios cuando se cambian las credenciales de inicio de sesión, cuando pierda o le roben el dispositivo o cuando no se cumplan las condiciones de la directiva en el momento de solicitar una renovación.

## <a name="deployment-considerations"></a>Consideraciones de la implementación

Debe usar el servicio de registro de dispositivos de Azure AD para registrar dispositivos.

Si se van a autenticar usuarios locales, se necesita la versión 1.0 o versiones posteriores de Servicios de federación de Active Directory (AD FS). Se producirá un error en la autenticación multifactor para Workplace Join si el proveedor de identidades no es capaz de llevar a cabo la autenticación multifactor. Por ejemplo, no puede usar la autenticación multifactor con AD FS 2.0. Asegúrese de que AD FS local funcione con la autenticación multifactor y de que haya en vigor un método de autenticación multifactor válido antes de activar la autenticación multifactor para el servicio de registro de dispositivos de Azure AD. Por ejemplo, AD FS en Windows Server 2012 R2 tiene capacidades de autenticación multifactor. También debe establecer un método de autenticación válido adicional (autenticación multifactor) en el servidor de AD FS antes de activar la autenticación multifactor para el servicio de registro de dispositivos de Azure AD. Para obtener más información sobre los métodos de autenticación multifactor admitidos en AD FS, consulte [Configurar los métodos de autenticación adicional de AD FS](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs).

## <a name="next-steps"></a>Pasos siguientes

*   Para obtener respuesta a preguntas frecuentes, consulte [Preguntas más frecuentes acerca del acceso condicional de Azure Active Directory](active-directory-conditional-faqs.md).
