---
title: "Solución de problemas de la comprobación en dos pasos | Microsoft Docs"
description: "Este documento ofrecerá a los usuarios información sobre qué hacer si se encuentran con un problema con Azure Multi-Factor Authentication"
services: multi-factor-authentication
keywords: "cliente de multi-factor authentication, problema de autenticación, identificador de correlación"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: kgremban
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 2eab76ba42955616ae4d6909c1568f03da0c1819
ms.lasthandoff: 12/22/2016

---
# <a name="having-trouble-with-two-step-verification"></a>¿Tiene problemas con la comprobación en dos pasos?
En este artículo se habla de algunos problemas que puede experimentar con la comprobación en dos pasos. Si el problema que tiene no se incluye aquí, proporcione comentarios detallados en la sección de comentarios para que podamos mejorar.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Perdí el teléfono o me lo robaron
Hay dos formas de volver a tener acceso a su cuenta. La primera consiste en iniciar sesión con su número de teléfono de autenticación alternativo, en caso de tener uno configurado. La segunda consiste en pedir al administrador que borre la configuración.

Si perdió el teléfono o se lo robaron, también recomendamos que solicite al administrador que restablezca las contraseñas de la aplicación y que borre todos los dispositivos recordados. Si el administrador no está seguro de cómo hacerlo, diríjale a este artículo: [Administrar usuarios y dispositivos](../multi-factor-authentication-manage-users-and-devices.md).

### <a name="use-an-alternate-phone-number"></a>Usar un número de teléfono alternativo
Si ha configurado varias opciones de comprobación, incluidos un número de teléfono secundario o una aplicación autenticadora de un dispositivo diferente, puede usar una ellas para iniciar sesión.

Para iniciar sesión con el número de teléfono alternativo, siga estos pasos:

1. Inicie sesión como lo haría normalmente.
2. Cuando se le pida que compruebe su cuenta una vez más, elija **Usar otra opción de comprobación**.
   
    ![Comprobación distinta](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. Seleccione el número de teléfono al que tiene acceso.
   
    ![Teléfono alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. Una vez de vuelta en su cuenta, [administre su configuración](multi-factor-authentication-end-user-manage-settings.md) para cambiar el número de teléfono de autenticación.

> [!IMPORTANT]
> Es importante configurar un número de teléfono de autenticación secundario. Si su número de teléfono principal y la aplicación móvil se encuentran en el mismo teléfono, necesita una tercera opción si pierde el teléfono o se lo roban.   

### <a name="clear-your-settings"></a>Borrar su configuración
Si no ha configurado un número de teléfono de autenticación secundario, deberá ponerse en contacto con el administrador para que le ayude. Pídale que borre su configuración. De este modo, la próxima vez que inicie sesión, se le pedirá de nuevo que [configure su cuenta](multi-factor-authentication-end-user-first-time.md).

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>No recibo ningún mensaje o llamada en el teléfono
Hay varios motivos por los que es posible que intente iniciar sesión y no reciba el mensaje o llamada de teléfono. Si ha recibido mensajes o llamadas en su teléfono anteriormente, es probable que se trate de un problema con el proveedor del teléfono y no de un problema con la cuenta. Asegúrese de que la señal es buena y, si intenta recibir un mensaje de texto, asegúrese de que el teléfono y el plan de servicio admiten mensajes de texto.

Si ha esperado varios minutos la recepción de un mensaje o llamada, la forma más rápida de tener acceso a su cuenta es probar una opción diferente.

1. Seleccione **Usar otra opción de comprobación** en la página que está a la espera de su comprobación.
   
    ![Comprobación distinta](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Seleccione el número de teléfono o el método de entrega que desea usar.
   
    Si recibió varios códigos de comprobación, solo funcionará el más reciente.

Si no tiene otro método configurado, póngase en contacto con el administrador y pídale que borre su configuración. La próxima vez que inicie sesión, se le pedirá de nuevo que [configure la autenticación multifactor](multi-factor-authentication-end-user-first-time.md).

Si suele tener retrasos debido a una mala calidad de la señal, recomendamos que use la [aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) en su smartphone. La aplicación puede generar códigos de seguridad aleatorios que usa para iniciar sesión, los cuales no requieren señal telefónica ni conexión a Internet.

## <a name="app-passwords-are-not-working"></a>Las contraseñas de la aplicación no funcionan
En primer lugar, asegúrese de haber escrito correctamente la contraseña de la aplicación.  Si sigue sin funcionar, vuelva a intentar el inicio de sesión y [cree una contraseña de aplicación nueva](multi-factor-authentication-end-user-app-passwords.md).  Si esto no funciona, póngase en contacto con el administrador y pídale que [elimine sus contraseñas de aplicación existentes](../multi-factor-authentication-manage-users-and-devices.md) y luego puede crear una nueva.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>No encuentro una respuesta a mi problema.
Si ha probado estos pasos para solucionar los problemas y estos persisten, póngase en contacto con el administrador o con la persona responsable de la configuración de la autenticación multifactor. Deberían poder ayudarle.

Además, puede publicar una pregunta en los [foros de Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o [ponerse en contacto con el soporte técnico](https://support.microsoft.com/contactus) y responderemos a su problema lo antes posible.

Si se comunica con el soporte técnico, incluya la información siguiente:

* **Id. de usuario**: cuál es la dirección de correo electrónico con la que ha intentado iniciar sesión.
* **Descripción general del error**: ¿qué mensaje de error ve exactamente?  Si no hay ningún mensaje de error, describe detalladamente el comportamiento inesperado observado.
* **Página** : en qué página estaba cuando se generó el error (incluir la dirección URL).
* **Código de error** : el código de error específico que ha recibido.
* **Id. de sesión**: el id. de sesión específico que ha recibido.
* **Id. de correlación**: el código de id. de correlación generado cuando el usuario vio el error.
* **Marca de tiempo** : en qué fecha y a qué hora exactamente se ha generado el error (incluir la zona horaria).

Gran parte de esta información puede encontrarse en la página de inicio de sesión. Si no comprueba su inicio de sesión a tiempo, seleccione **Ver detalles**.

![Detalles del error de inicio de sesión](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Incluir esta información nos ayudará a solucionar su problema lo antes posible.

## <a name="related-topics"></a>Temas relacionados
* [Administración de la configuración de la comprobación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)  
* [Preguntas más frecuentes de la aplicación Microsoft Authenticator](microsoft-authenticator-app-faq.md)


