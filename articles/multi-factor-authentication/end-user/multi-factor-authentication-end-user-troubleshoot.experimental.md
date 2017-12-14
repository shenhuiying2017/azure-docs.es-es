---
title: "Solución de problemas de la comprobación en dos pasos | Microsoft Docs"
description: "Este documento ofrecerá a los usuarios información sobre qué hacer si se encuentran con un problema con Azure Multi-Factor Authentication"
services: multi-factor-authentication
keywords: "cliente de multi-factor authentication, problema de autenticación, identificador de correlación"
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: barlan
ms.reviewer: richagi
ms.custom: end-user
ms.openlocfilehash: 20a90aa36b727b18fb37aaf658da884b5997cd44
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-help-with-two-step-verification"></a>Obtener ayuda con la verificación en dos pasos
Este artículo responde a las preguntas más comunes que tienen las personas sobre la verificación en dos pasos. 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>¿Por qué es necesario realizar la verificación en dos pasos? ¿Puedo desactivarla?

La verificación en dos pasos es una característica de seguridad que la organización decidió usar para proteger las cuentas del usuario. Es más segura que solo una contraseña, porque se basa en dos formas de autenticación: algo que usted sabe y algo que usted tiene. Lo que sabe es la contraseña. Lo que tiene con usted es un teléfono o un dispositivo del que suele disponer. Si su cuenta está protegida mediante una verificación en dos pasos, ningún hacker malintencionado podrá iniciar sesión como si fuera usted, ni siquiera aunque tenga la contraseña. No puede hacerlo porque no tiene acceso a su teléfono. 

Microsoft ofrece la verificación en dos pasos, pero su organización decide usar la característica. No puede desentenderse si el soporte técnico de la empresa se lo requiere, de la misma manera que no puede dejar de usar una contraseña para proteger su cuenta. 

Si tiene la verificación en dos pasos activada para su cuenta de Microsoft personal y desea cambiar la configuración, lea [Acerca de la verificación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). 

## <a name="i-dont-have-my-phone-with-me-today"></a>No tengo mi teléfono conmigo hoy

Algunos días deja el teléfono en casa, pero todavía necesita iniciar sesión en el trabajo. Lo primero que debe intentar es iniciar sesión con un método de verificación diferente. Al registrarse para la verificación en dos pasos, ¿configuró más de un número de teléfono? Para intentar iniciar sesión con un método diferente, siga estos pasos:

1. Inicie sesión como lo haría normalmente.
2. Cuando se abre la página de verificación en dos pasos, elija **Usar otra opción de comprobación**.

   ![Comprobación distinta](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Seleccione la opción de verificación que desea utilizar. 
  - Si no tiene acceso a otros métodos, póngase en contacto con el soporte técnico de la empresa para obtener ayuda para iniciar sesión en su cuenta.
  - Si tiene acceso a otros métodos, continúe con la verificación en dos pasos.

Si no ve el vínculo **Usar otra opción de comprobación**, significa que no configuró métodos alternativos la primera vez que realizó el registro para la verificación en dos pasos. Póngase en contacto con el soporte técnico de la empresa para obtener ayuda para iniciar sesión en su cuenta. Una vez que ha iniciado sesión, asegúrese de [administrar la configuración](multi-factor-authentication-end-user-manage-settings.md) para agregar métodos de comprobación adicionales para la próxima vez. 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>Perdí mi teléfono o cambié de número
Hay dos formas de volver a tener acceso a su cuenta. La primera consiste en iniciar sesión con su número de teléfono de autenticación alternativo, en caso de tener uno configurado. La segunda consiste en pedir al soporte técnico de la empresa que borre la configuración.

Si el teléfono se ha perdido o lo han robado, también es aconsejable informar al soporte técnico de la empresa. Allí tendrán que restablecer las contraseñas de las aplicaciones y eliminar todos los dispositivos recordados. 

### <a name="use-an-alternate-phone-number"></a>Usar un número de teléfono alternativo
Si configura varias opciones de comprobación, como un número de teléfono secundario o una aplicación autenticadora de un dispositivo diferente, use cualquiera de ellas para iniciar sesión.

Para iniciar sesión con el número de teléfono alternativo, siga estos pasos:

1. Inicie sesión como lo haría normalmente.
2. Cuando se le pida que compruebe su cuenta una vez más, elija **Usar otra opción de comprobación**.
   
   ![Comprobación distinta](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. Seleccione el número de teléfono o el dispositivo al que tiene acceso.
4. Una vez de vuelta en su cuenta, [administre su configuración](multi-factor-authentication-end-user-manage-settings.md) para cambiar el número de teléfono de autenticación.

### <a name="clear-your-settings"></a>Borrar su configuración
Si no ha configurado un número de teléfono de autenticación secundario, debe ponerse en contacto con el soporte técnico de la empresa para que le ayude. Pídale que borre su configuración. De este modo, la próxima vez que inicie sesión, se le pedirá de nuevo que se [registre para la verificación en dos pasos](multi-factor-authentication-end-user-first-time.md).

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>No recibo ningún mensaje o llamada en el teléfono
Hay varios motivos por los que es posible que intente iniciar sesión y no reciba el mensaje o llamada de teléfono. Si ha recibido mensajes o llamadas en su teléfono anteriormente, es probable que el problema tenga que ver con el proveedor del teléfono, no con la cuenta. Asegúrese de que la señal es buena y, si intenta recibir un mensaje de texto, asegúrese de que puede recibir dichos mensajes. Pida a un amigo que le llame o envíese a sí mismo un mensaje de texto como prueba. 

Si ha esperado varios minutos la recepción de un mensaje o llamada, la forma más rápida de tener acceso a su cuenta es probar una opción diferente.

1. Seleccione **Usar otra opción de comprobación** en la página que está a la espera de su comprobación.
   
    ![Comprobación distinta](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Seleccione el número de teléfono o el método de entrega que desea usar.
   
    Si recibió varios códigos de comprobación, use el más reciente.

Si no tiene otro método configurado, póngase en contacto con el soporte técnico de la empresa y pídale que borre su configuración. La próxima vez que inicie sesión, se le pedirá de nuevo que [configure la autenticación multifactor](multi-factor-authentication-end-user-first-time.md).

Si suele tener retrasos debido a una mala calidad de la señal, recomendamos que use la [aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) en su smartphone. La aplicación puede generar códigos de seguridad aleatorios que usa para iniciar sesión, los cuales no requieren señal telefónica ni conexión a Internet.

## <a name="app-passwords-are-not-working"></a>Las contraseñas de la aplicación no funcionan
En primer lugar, asegúrese de haber escrito correctamente la contraseña de la aplicación. La contraseña de aplicación generada reemplaza a la contraseña normal, pero solo para aplicaciones de escritorio más antiguas que no admiten la verificación en dos pasos. Si sigue sin funcionar, intente iniciar sesión y [cree una nueva contraseña de aplicación](multi-factor-authentication-end-user-app-passwords.md).  Si aún no funciona, póngase en contacto con el soporte técnico de la empresa, pídale que [elimine sus contraseñas de aplicación existentes](../multi-factor-authentication-manage-users-and-devices.md) y luego puede crear otra.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>No encuentro una respuesta a mi problema.
Si probó estos pasos para solucionar los problemas pero estos no desaparecen, póngase en contacto con el soporte técnico de la empresa. Deberían poder ayudarle.

## <a name="related-topics"></a>Temas relacionados
* [Administración de la configuración de la comprobación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)  
* [Preguntas más frecuentes de la aplicación Microsoft Authenticator](microsoft-authenticator-app-faq.md)

