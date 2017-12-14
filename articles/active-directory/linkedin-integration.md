---
title: "Configuración de la integración de LinkedIn en Azure AD | Microsoft Docs"
description: "Explica cómo habilitar o deshabilitar la integración de LinkedIn para las aplicaciones de Microsoft en Azure Active Directory."
services: active-directory
author: jeffgilb
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 15c161542d6826e6aeb5f708a0d9c3fa1f1885e3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Habilitación de la integración de LinkedIn en Azure Active Directory
Habilitar la integración de LinkedIn permite a los usuarios acceder a los datos públicos de LinkedIn y, si así lo eligen, a la red personal de LinkedIn desde las aplicaciones de Microsoft. Los usuarios pueden elegir independientemente si conectan la cuenta profesional a la cuenta de LinkedIn.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>Integración de LinkedIn desde la perspectiva de los usuarios finales
Cuando los usuarios finales de la organización se conectan su cuenta de LinkedIn a su cuenta de trabajo, pueden identificar mejor a las personas con las que trabajan dentro y fuera de la organización. Conectar las dos cuentas permite el uso de las conexiones de LinkedIn y los datos del perfil en aplicaciones de Microsoft de la organización, pero siempre se puede descartar mediante la anulación del permiso de compartir esos datos de LinkedIn. La integración también usa la información pública del perfil y los usuarios pueden elegir si desean compartir su perfil público y la información de su red con las aplicaciones de Microsoft a través de la configuración de privacidad de LinkedIn.

>[!NOTE]
> Habilitar la integración de LinkedIn en Azure AD permite el acceso de aplicaciones y servicios a parte de información de los usuarios finales. Consulte la [Declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement/) para más información sobre las consideraciones de privacidad al habilitar la integración de LinkedIn en Azure AD. 

## <a name="enable-linkedin-integration"></a>Habilitación de la integración de LinkedIn
La integración de LinkedIn para empresas está habilitada de forma predeterminada en Azure AD. Por lo tanto, cuando esta característica está disponible para su inquilino, todos los usuarios de su organización verán los perfiles y las características de LinkedIn. Habilitar la integración de LinkedIn permite a los usuarios de la organización usar características de LinkedIn en los servicios de Microsoft, como ver el perfil al recibir un correo electrónico en Outlook. Deshabilitar esta característica impide el acceso a características de LinkedIn e interrumpe las conexiones de la cuenta de usuario y los datos de uso compartido entre LinkedIn y la organización a través de los servicios de Microsoft.

> [!IMPORTANT]
> Esta característica no está disponible para los inquilinos go-local, sovereign y government. Además, las actualizaciones de servicio de Azure AD, como las funcionalidades de integración de LinkedIn, no se implementan en todos los inquilinos de Azure al mismo tiempo. No se puede habilitar la integración de LinkedIn con Azure AD hasta que esta funcionalidad se haya incorporado a su inquilino de Azure.

1. Inicie sesión en el [Centro de administración de Azure Active Directory](https://aad.portal.azure.com/) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Usuarios y grupos**.
3. En la hoja **Usuarios y grupos**, seleccione **Configuración de usuario**.
4. En **Integración de LinkedIn**, seleccione Sí o No para habilitar o deshabilitar la integración de LinkedIn.
   ![Habilitación de la integración de LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Más información 
* [Información y características de LinkedIn en las aplicaciones de Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ayuda de LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Pasos siguientes
Puede usar el vínculo siguiente para habilitar o deshabilitar la integración de LinkedIn con Azure AD desde Azure Portal:

[Configuración de la integración con LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 