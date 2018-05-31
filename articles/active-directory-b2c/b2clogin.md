---
title: Uso de b2clogin.com| Microsoft Docs
description: Más información sobre el uso de b2clogin.com en lugar de login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660429"
---
#<a name="using-b2clogincom"></a>Uso de b2clogin.com

>[!IMPORTANT]
>Esta característica está en versión preliminar pública. 
>

Ahora tiene la opción de usar el servicio Azure AD B2C con `<YourTenantName>.b2clogin.com` en lugar de usar `login.microsoftonline.com`.  Esto tiene muchas ventajas:
* Ya no compartirá el mismo límite de tamaño de encabezado de cookies con los demás productos de Microsoft.
* Puede quitar todas las referencias a Microsoft en la dirección URL (puede reemplazar `<YourTenantName>.onmicrosoft.com` por el identificador de inquilino).

 Para aprovechar las ventajas de b2clogin.com, necesita configurar algunas de las siguientes opciones:

1. En **Ejecutar punto de conexión ahora**, asegúrese de que utiliza `<YourTenantName>.b2clogin.com` en lugar de `login.microsoftonline.com`.
2. Si utiliza MSAL, debe establecer `validateauthority=false`.  Esto es porque el emisor del token se convierte en `<YourTenantName>.b2clogin.com`.