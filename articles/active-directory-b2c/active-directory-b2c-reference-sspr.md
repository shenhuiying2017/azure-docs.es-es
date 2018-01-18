---
title: "Azure Active Directory B2C: restablecimiento de contraseña de autoservicio | Microsoft Docs"
description: "Tema en el que se demuestra cómo configurar el autoservicio de restablecimiento de contraseña para los consumidores en Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: c0a10869477647820828b22b6291522be255844d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: configurar el autoservicio de restablecimiento de contraseña para los consumidores
Con la característica de autoservicio de restablecimiento de contraseña, los consumidores que se registraron para obtener cuentas locales pueden restablecer sus contraseñas ellos mismos. De esta manera se reduce significativamente la carga del personal de soporte técnico, especialmente si la aplicación tiene millones de consumidores que la usan de forma periódica. Actualmente, solo se admite como método de recuperación el uso de una dirección de correo electrónico comprobada. Agregaremos métodos de recuperación adicionales (número de teléfono comprobado, preguntas de seguridad, etc.) en el futuro.

> [!NOTE]
> Este artículo se aplica a la operación de autoservicio de restablecimiento de contraseña empleada en el contexto de una directiva de inicio de sesión. Si necesita invocar directivas de restablecimiento de contraseña totalmente personalizables desde su aplicación, consulte [este artículo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

De forma predeterminada, el directorio no tendrá activado el autoservicio de restablecimiento de contraseña. Para activarlo, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de la suscripción. Esta cuenta es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para crear el directorio.
2. Abra Active Directory (en la barra de navegación del lado izquierdo).
3. Seleccione **Propiedades**.
4. Desplácese hacia abajo hasta la sección **Se habilitó el restablecimiento de contraseña del autoservicio** y establézcala en **Todo**. 
5. Haga clic en **Guardar** en la parte superior de la página. ¡Y ya está!

Para probar, use la característica "Ejecutar ahora" en cualquier directiva de inicio de sesión que tenga cuentas locales como proveedor de identidades. En la página de inicio de sesión de la cuenta local (donde escribe la dirección de correo electrónico y la contraseña, o bien el nombre de usuario y la contraseña), haga clic en **¿No se puede tener acceso a la cuenta?** para comprobar la experiencia del consumidor.

> [!NOTE]
> Las páginas del autoservicio de restablecimiento de contraseña se pueden personalizar con la [característica de personalización de marca de empresa](../active-directory/customize-branding.md).
> 
> 

