---
title: "Configuración de MDM y directivas de grupo | Microsoft Docs"
description: "Proporciona información sobre la configuración de directiva de grupo y la administración de dispositivos móviles (MDM) que debe usarse en dispositivos de empresa. Estas directivas se aplican al dispositivo completo del usuario."
services: active-directory
keywords: "¿cuál es la configuración de directiva de grupo y MDM para Enterprise State Roaming, Enterprise State Roaming, nube de windows"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 71dd5281a618fe7367eab3e97daac069f77ab491
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="group-policy-and-mdm-settings"></a>Configuración de MDM y directivas de grupo
Use esta configuración de directiva de grupo y de dispositivos móviles (MDM) de solo en dispositivos de empresa, dado que estas directivas se aplican en todo el dispositivo del usuario. Aplicar una directiva MDM para deshabilitar la sincronización de configuración para un dispositivo de usuario personal ejercerá un impacto negativo en el uso de ese dispositivo. Además, otras cuentas de usuario en el dispositivo también se verán afectadas por la directiva.

Las empresas que desean administrar la movilidad para sus dispositivos personales (no administrados) pueden usar el Portal de Azure para habilitar o deshabilitar la movilidad, en lugar de usar la directiva de grupo o MDM.
Las tablas siguientes describen la configuración de la directiva disponible.

## <a name="mdm-settings"></a>Configuración de MDM
La configuración de directiva MDM se aplica a Windows 10 y Windows 10 Mobile.  El soporte para Windows 10 Mobile existe solo para la itinerancia basada en cuentas de Microsoft a través de la cuenta OneDrive del usuario.  Consulte la sección "Dispositivos y puntos de conexión" para obtener detalles sobre qué dispositivos son compatibles para la sincronización basad en Azure AD.

| Nombre | Description |
| --- | --- |
| Permitir la conexión con una cuenta de Microsoft |Permite a los usuarios autenticarse con una cuenta de Microsoft en el dispositivo. |
| Permitir la sincronización de mi configuración |Permite a los usuarios usar un perfil itinerante para la configuración de Windows y los datos de la aplicación; si deshabilita esta directiva se deshabilitará la sincronización, así como las copias de seguridad en dispositivos móviles |

## <a name="group-policy-settings"></a>Configuración de directiva de grupo
La configuración de directiva de grupo se aplica a dispositivos de Windows 10 que están unidos a un dominio de Active Directory. La tabla incluye también la configuración heredada que aparecerá para administrar la configuración de sincronización, pero que no funciona para Enterprise State Roaming para Windows 10. Esto se indica con "No utilizar" en la descripción.

| Nombre | Description |
| --- | --- |
| Cuentas: bloquear cuentas Microsoft |Esta configuración de directiva impide a los usuarios agregar nuevas cuentas de Microsoft en este equipo. |
| No sincronizar |Impide a los usuarios usar un perfil itinerante para los datos de la aplicación y la configuración de Windows. |
| No sincronizar personalización |Deshabilita la sincronización del grupo Temas. |
| No sincronizar la configuración del explorador |Deshabilita la sincronización del grupo Internet Explorer. |
| No sincronizar contraseñas |Deshabilita la sincronización del grupo Contraseñas. |
| No sincronizar otra configuración de Windows |Deshabilita la sincronización del grupo Otra configuración de Windows. |
| No sincronizar la personalización del escritorio |No se usa; no tiene efecto. |
| No sincronizar con conexiones de uso medido |Deshabilita la movilidad en conexiones limitadas, como 3G móvil. |
| No sincronizar aplicaciones |No se usa; no tiene efecto. |
| No sincronizar la configuración de aplicaciones |Deshabilita la movilidad de datos de la aplicación. |
| No sincronizar la configuración de inicio |No se usa; no tiene efecto. |

## <a name="related-topics"></a>Temas relacionados
* [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Solución de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

