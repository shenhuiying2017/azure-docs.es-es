---
title: "Azure Active Directory B2C: restablecimiento de contraseña de autoservicio | Microsoft Docs"
description: "Tema en el que se demuestra cómo configurar el autoservicio de restablecimiento de contraseña para los consumidores en Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 0508868e3b00c5771cc26038a3dd71fde6625a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: configurar el autoservicio de restablecimiento de contraseña para los consumidores
Con la característica de autoservicio de restablecimiento de contraseña, los consumidores que se registraron para obtener cuentas locales pueden restablecer sus contraseñas ellos mismos. De esta manera se reduce significativamente la carga del personal de soporte técnico, especialmente si la aplicación tiene millones de consumidores que la usan de forma periódica. Actualmente, solo se admite como método de recuperación el uso de una dirección de correo electrónico comprobada. Agregaremos métodos de recuperación adicionales (número de teléfono comprobado, preguntas de seguridad, etc.) en el futuro.

> [!NOTE]
> Este artículo se aplica a la operación de autoservicio de restablecimiento de contraseña empleada en el contexto de una directiva de inicio de sesión. Si necesita invocar directivas de restablecimiento de contraseña totalmente personalizables desde su aplicación, consulte [este artículo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

De forma predeterminada, el directorio no tendrá activado el autoservicio de restablecimiento de contraseña. Para activarlo, siga estos pasos:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) como administrador de la suscripción. Esta cuenta es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para crear el directorio.
2. Vaya a la extensión de Active Directory en la barra de navegación del lado izquierdo.
3. Busque su directorio en la pestaña **Directorio** y haga clic en él.
4. Haga clic en la pestaña **Configure** .
5. Baje hasta la sección **Políticas para restablecer la contraseña del usuario** y cambie el valor de la opción **Usuarios habilitados para restablecer la contraseña** a **SÍ**. Observe que la opción **Dirección de correo electrónico alternativa** está activada; déjela así.
   
    ![Restablecimiento de la contraseña de autoservicio](./media/active-directory-b2c-reference-sspr/sspr.png)
6. Haga clic en **Guardar** en la parte inferior de la página. ¡Y ya está!

Para probar, use la característica "Ejecutar ahora" en cualquier directiva de inicio de sesión que tenga cuentas locales como proveedor de identidades. En la página de inicio de sesión de la cuenta local (donde escribe la dirección de correo electrónico y la contraseña, o bien el nombre de usuario y la contraseña), haga clic en **¿No se puede tener acceso a la cuenta?** para comprobar la experiencia del consumidor.

> [!NOTE]
> Las páginas del autoservicio de restablecimiento de contraseña se pueden personalizar con la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md).
> 
> 

