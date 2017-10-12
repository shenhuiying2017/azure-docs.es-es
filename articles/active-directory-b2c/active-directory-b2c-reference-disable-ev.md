---
title: "Azure Active Directory B2C: Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores | Microsoft Docs"
description: "En este tema se muestra cómo deshabilitar la comprobación de correos electrónicos durante la suscripción de consumidores en Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
ms.openlocfilehash: d8e44a8aade60d21734477d60bccc2bd5194436e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores
Cuando se habilita, Azure Active Directory (Azure AD) B2B proporciona al consumidor la posibilidad de iniciar sesión en las aplicaciones ofreciendo una dirección de correo electrónico y creando una cuenta local. Azure AD B2C garantiza direcciones de correo electrónico válidas exigiendo que los consumidores puedan comprobarlas durante el proceso de suscripción. También impide que un proceso automatizado malintencionado genere cuentas falsas para aplicaciones.

Algunos desarrolladores de aplicaciones prefieren pasar por alto la comprobación de correos electrónicos durante el proceso de suscripción y, en su lugar, son los consumidores los que tienen que comprobar la dirección de correo electrónico más tarde. Para ello, Azure AD B2C puede configurarse para deshabilitar la comprobación de correo electrónico. Si lo hace, crea un proceso de inicio de sesión más eficaz y ofrece a los desarrolladores la flexibilidad para diferenciar los consumidores que han comprobado su dirección de correo electrónico de los que no lo ha hecho.

De forma predeterminada, las directivas de inicio de sesión tienen activada la comprobación de correos electrónicos. Siga estos pasos para desactivarla:

1. [Siga estos pasos para ir a la hoja de características de B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Haga clic en **Sign-up policies** (Directivas de registro) o **Sign-up or sign-in policies** (Directivas de inicio de sesión o registro) dependiendo de lo que haya configurado para el inicio de sesión.
3. Haga clic en la directiva (por ejemplo, "B2C_1_SiUp") para abrirla. Haga clic en **Editar** en la parte superior de la hoja.
4. Haga clic en **Page UI Customization** (Personalización de la IU de la página).
5. Haga clic en **Página de suscripción de cuenta local**.
6. Haga clic en **Dirección de correo electrónico** en la columna **Nombre** bajo la sección **Atributos de suscripción**.
7. Establezca la opción **Requerir comprobación** en **No**.
8. Haga clic en **Aceptar** en la parte inferior hasta llegar a la hoja **Editar directiva**.
9. Haga clic en **Guardar** en la parte superior de la hoja. ¡Y ya está!

> [!NOTE]
> Deshabilitar la comprobación de correos electrónicos en el proceso de registro puede provocar que se reciban correos no deseados. Si deshabilita el valor predeterminado, se recomienda agregar su propio sistema de comprobación.
> 
> 

Siempre estamos abiertos a todo tipo de comentarios y sugerencias. Si tiene dificultades para completar este tema o tiene recomendaciones para mejorar este contenido, agradecemos que escriba sus comentarios en la parte inferior de la página. Si tiene solicitudes de características, agréguelas a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).