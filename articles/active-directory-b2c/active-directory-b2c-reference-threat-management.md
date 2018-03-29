---
title: 'Azure Active Directory B2C: administración de amenazas | Microsoft Docs'
description: Obtenga información sobre las técnicas de detección y mitigación de los ataques por denegación de servicio y los ataques a contraseñas en Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.openlocfilehash: 5ab699b0dccd772ec905699d94dedaca0eefcdad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: administración de amenazas

La administración de amenazas incluye la planeación de la protección frente a ataques contra el sistema y las redes. Los ataques por denegación de servicio pueden hacer que los recursos no estén disponibles para los usuarios previstos. Los ataques a contraseñas pueden dar lugar a un acceso no autorizado a los recursos. Azure Active Directory B2C (Azure AD B2C) tiene características integradas que le pueden ayudar a proteger los datos frente a estas amenazas de varias maneras.

## <a name="denial-of-service-attacks"></a>Ataques por denegación de servicio

Azure AD B2C usa técnicas de detección y mitigación, como las cookies SYN y los límites de velocidad y conexión, para proteger los recursos subyacentes frente a los ataques por denegación de servicio.

## <a name="password-attacks"></a>Ataques a contraseñas

Azure AD B2C también dispone de técnicas de mitigación para los ataques a contraseñas. La mitigación incluye ataques a contraseñas por fuerza bruta y ataques de diccionario. Las contraseñas establecidas por los usuarios deben tener una complejidad razonable. Mediante el uso de diversas señales, Azure AD B2C analiza la integridad de las solicitudes. Azure AD B2C está diseñado para diferenciar de forma inteligente los usuarios previstos frente a los hackers y las redes de robots (botnets). Azure AD B2C proporciona una estrategia sofisticada para bloquear cuentas en función de las contraseñas usadas ante la posibilidad de un ataque.

Para obtener más información, visite el [Centro de confianza de Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement).
