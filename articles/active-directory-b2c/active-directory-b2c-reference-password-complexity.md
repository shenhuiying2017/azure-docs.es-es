---
title: "Complejidad de contraseñas: Azure AD B2C | Microsoft Docs"
description: "Cómo configurar los requisitos de complejidad de contraseñas proporcionados por los consumidores en Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 3906c9fa1def206a8f0a7e155949097242728c2f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: configuración de los requisitos de complejidad para las contraseñas

> [!NOTE]
> **Esta característica se encuentra en versión preliminar.**  Póngase en contacto con [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) para que su inquilino de prueba tenga esta característica habilitada.  No pruebe esto en inquilinos de producción.

Azure Active Directory B2C (Azure AD B2C) admite el cambio de los requisitos de complejidad de las contraseñas proporcionadas por un usuario final al crear una cuenta.  De forma predeterminada, Azure AD B2C utiliza contraseñas `Strong`.  Azure AD B2C también admite opciones de configuración para controlar la complejidad de las contraseñas que los clientes pueden usar.

## <a name="when-password-rules-are-enforced"></a>Cuándo se aplican las reglas de contraseña

Al realizar el registro o el restablecimiento de contraseña, un usuario final tiene que proporcionar una contraseña que cumpla las reglas de complejidad.  Las reglas de complejidad de contraseña se aplican por directiva.  Es posible tener una directiva que exija un pin de cuatro dígitos durante el registro mientras otra directiva requiere una cadena de ocho caracteres al realizar el registro.  Por ejemplo, puede utilizar una directiva con complejidad de contraseña diferente para adultos y para niños.

La complejidad de la contraseña nunca se aplica durante el inicio de sesión.  Nunca se pide a los usuarios durante el inicio de sesión que cambien su contraseña porque no cumple los requisitos de complejidad actuales.

Estos son los tipos de directivas en los que se puede configurar la complejidad de la contraseña:

* Directiva de registro o de inicio de sesión
* Directiva de restablecimiento de contraseña
* Directiva personalizada ([Configuración de la complejidad de contraseñas en las directivas personalizadas](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Procedimientos para la configuración de la complejidad de contraseñas

1. Siga estos pasos para [ir a la configuración de Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Abra **Sign-up or sign-in policies** (Directivas de registro o de inicio de sesión).
1. Seleccione una directiva y haga clic en **Editar**.
1. Abra **Complejidad de la contraseña**.
1. Cambie la complejidad de contraseña para esta directiva a **Simple**, **Segura**, o **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparación

| Complejidad | Descripción |
| --- | --- |
| Simple | Una contraseña que tenga al menos de 8 a 64 caracteres. |
| Alta | Una contraseña que tenga al menos de 8 a 64 caracteres. Requiere 3 de 4 de minúsculas, mayúsculas, números o símbolos. |
| Personalizado | Esta opción proporciona el máximo control sobre las reglas de complejidad de contraseña.  Permite configurar una longitud personalizada.  También permite aceptar contraseñas compuestas solo por números (PIN). |

## <a name="options-available-under-custom"></a>Opciones disponibles para la personalización

### <a name="character-set"></a>Juego de caracteres

Permite aceptar solo dígitos (PIN) o el juego completo de caracteres.

* **Solo números** permite solamente dígitos (0-9) al escribir una contraseña.
* **Todos** permite cualquier letra, número o símbolo.

### <a name="length"></a>Length

Permite controlar los requisitos de longitud de la contraseña.

* **Longitud mínima** tiene que ser 4 como mínimo.
* **Longitud máxima** tiene que ser mayor o igual que la longitud mínima y como máximo puede ser 64 caracteres.

### <a name="character-classes"></a>Clase de caracteres

Permite controlar los distintos tipos de caracteres utilizados en la contraseña.

* **2 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos dos tipos de caracteres. Por ejemplo, un número y un carácter en minúscula.
* **3 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos tres tipos de caracteres. Por ejemplo, un número, un carácter en minúscula y un carácter en mayúscula.
* **4 de 4: carácter en minúscula, carácter en mayúscula, número (0-9), símbolo** garantiza que la contraseña contiene al menos todos tipos de caracteres.

    > [!NOTE]
    > Solicitar una contraseña de tipo **4 de 4** puede dar lugar a la frustración del usuario final. Algunos estudios han demostrado que este requisito no mejorar la entropía de contraseña. Consulte las [directrices para contraseñas del NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
