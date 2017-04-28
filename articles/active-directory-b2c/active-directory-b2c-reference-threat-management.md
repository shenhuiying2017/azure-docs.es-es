---
title: "Administración de amenazas de Azure B2C | Microsoft Docs"
description: "Detección de ataques DOS y ataques a contraseñas, y técnicas de mitigación de Azure B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: Administración de amenazas
La administración de amenazas incluye la protección frente a ataques contra el sistema y las redes. Los ataques de denegación de servicio (DOS) pueden afectar a la disponibilidad e impedir que un recurso esté disponible para el usuario. Los ataques a contraseñas pueden dar lugar a un acceso no autorizado a los recursos. Microsoft Azure Active Directory B2C tiene características integradas para proteger los datos frente a estas amenazas de varias maneras. 

## <a name="denial-of-service-attack"></a>Ataque de denegación de servicio

Azure AD B2C usa técnicas de mitigación y detección, como las cookies SYN o los límites de velocidad y conexión, para proteger los recursos subyacentes frente a estos ataques.  

## <a name="password-attacks"></a>Ataques a contraseñas

Azure AD B2C también dispone de mitigaciones para los ataques a contraseñas.  Esta técnica incluye ataques a contraseñas por fuerza bruta y ataques de diccionario.  Las contraseñas establecidas por los usuarios tienen que ser de una complejidad razonable.  Azure AD B2C analiza la integridad de las solicitudes de forma inteligente para diferenciar los usuarios normales de hackers y botnets, mediante el uso de diversas señales. B2C proporciona una estrategia sofisticada para bloquear cuentas en función de las contraseñas utilizadas ante la posibilidad de un ataque.

[Más información sobre la administración de amenazas de Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement)
