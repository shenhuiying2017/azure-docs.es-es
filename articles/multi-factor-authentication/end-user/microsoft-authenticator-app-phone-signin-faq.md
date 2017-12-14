---
title: "Inicio de sesión en Microsoft Authenticator con el teléfono: cuentas de Azure y Microsoft | Microsoft Docs"
description: "Use el teléfono para iniciar sesión en su cuenta de Microsoft en lugar de escribir la contraseña. Este artículo responde a las preguntas más frecuentes acerca de esta característica."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Inicie sesión con el teléfono, no la contraseña

La aplicación Microsoft Authenticator le ayuda a proteger las cuentas mediante una verificación en dos pasos después de escribir la contraseña. Pero ¿sabía que puede reemplazar completamente la contraseña de su cuenta personal de Microsoft?

Esta característica está disponible en dispositivos iOS y Android, y funciona con cuentas personales de Microsoft.

## <a name="how-it-works"></a>Cómo funciona

Muchos de los usuarios usan la aplicación Microsoft Authenticator para la verificación en dos pasos al iniciar sesión en la cuenta de Microsoft. Escriba la contraseña, vaya a la aplicación para aprobar una notificación u obtener un código de verificación. Con el inicio de sesión de teléfono, omitirá la contraseña y realizará toda la verificación de identidad con el teléfono. Dado que el inicio de sesión en el teléfono es un tipo de verificación en dos pasos, sigue teniendo que proporcionar una información que conozca y algo que tenga para verificar su identidad. El teléfono sigue siendo el objeto que tiene y su PIN o su clave biométrica es la información que conoce.

## <a name="how-to-get-started"></a>Primeros pasos

Para iniciar sesión su cuenta personal de Microsoft con el teléfono, siga estos pasos:

1. Habilite el inicio de sesión con el teléfono para su cuenta.

  - Si aún no tiene la aplicación de Microsoft Authenticator, instale y agregue su cuenta personal de Microsoft según los pasos de la [página de Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Las cuentas recién agregadas se habilitan automáticamente, por lo que estará listo para continuar.

  - Si ya utiliza Microsoft Authenticator para la verificación en dos pasos, seleccione su cuenta desde la página de inicio de la aplicación y seleccione **Habilitar inicio de sesión en el teléfono** en el menú desplegable.

  >[!NOTE]
  >Para proteger su cuenta, necesitamos un PIN o bloqueo biométrico en el dispositivo. Si mantiene el teléfono desbloqueado, la aplicación le pide que configure un bloqueo antes de habilitar el inicio de sesión en el teléfono.

3. La mayoría de las páginas donde normalmente escribiría la contraseña de su cuenta de Microsoft tiene un vínculo que dice **Use una aplicación en su lugar**. Seleccione este vínculo para iniciar sesión con su teléfono.

4. Microsoft le enviará una notificación al teléfono. Acéptela para iniciar sesión en su cuenta.   

## <a name="faq"></a>P+F

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>¿Cómo es que iniciar sesión con el teléfono sea más seguro que escribir una contraseña?  

Hoy en día mayoría de los usuarios inician sesión en sitios web o aplicaciones con nombre de usuario y contraseña.  Desafortunadamente, las contraseñas a menudo se pierden, las roban o las adivinan los piratas informáticos. Al configurar la aplicación Microsoft Authenticator para iniciar sesión, se genera una clave en el teléfono que desbloquea la cuenta. Protegemos esta clave con el PIN o datos biométricos que ya utilice en el teléfono.  Cuando inicia sesión con su teléfono, esta clave se usa para demostrar su identidad de forma segura con dos factores: el propio teléfono y su capacidad para desbloquearlo. 

La clave que se usa es similar a las de Windows Hello y las especificaciones de FIDO Alliance UAF. Sus datos biométricos solo se usan para proteger la clave de forma local y nunca se envían ni se almacenan en la nube. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>¿Dónde puedo usar mi teléfono para reemplazar la contraseña y dónde sería necesaria la contraseña todavía?  

A día de hoy, la característica de inicio de sesión con el teléfono solo funciona con aplicaciones web y servicios que usan la tecnología de cuenta personal de Microsoft, las aplicaciones de iOS o Android que usan una cuenta personal de Microsoft y las aplicaciones de Windows 10 que usan una cuenta personal de Microsoft. Cuando inicia sesión en uno de estos sitios web o aplicaciones, en la página donde normalmente se escribe la contraseña hay un vínculo que dice **Use una aplicación en su lugar**. 

En este momento el inicio de sesión con el teléfono no puede usarse para desbloquear una máquina con Windows, XBOX o cualquier versión de escritorio de las aplicaciones de Microsoft, como las aplicaciones de Office.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>¿Reemplaza esto la verificación en dos pasos? ¿Debo apagarla?   

A veces. Estamos trabajando para ampliar el alcance del inicio de sesión con el teléfono, pero por ahora aún hay lugares del ecosistema de Microsoft que no son compatibles. En esos lugares se usa todavía la verificación en dos pasos para el inicio de sesión seguro. Por esa razón, no debería desactivar la comprobación en dos pasos para su cuenta.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>De acuerdo, si mantengo activa la comprobación en dos pasos para mi cuenta, ¿tendré que aprobar dos notificaciones?

No, para nada. El inicio de sesión en su cuenta de Microsoft con el teléfono cuenta como verificación en dos pasos. En lugar de escribir la contraseña y aprobar una notificación, demuestra su identidad al saber desbloquear el teléfono y, después, aprueba una notificación. No le enviaremos una segunda notificación para que la apruebe.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>¿Qué ocurre si pierdo mi teléfono o no lo tengo conmigo?, ¿cómo puedo acceder a mi cuenta?  

Siempre puede hacer clic en **Use su contraseña en su lugar** de la página de inicio de sesión para volver a utilizar la contraseña. Tenga en cuenta que, si usa la comprobación en dos pasos, seguirá siendo necesario un segundo método para verificar el inicio de sesión. Por eso es muy recomendable asegurarse de que tiene información de seguridad adicional actualizada en su cuenta. Puede administrar la información de seguridad en https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>¿Cómo dejo de usar esta característica y vuelvo a escribir mi contraseña?

Haga clic en **Use su contraseña en su lugar** al iniciar sesión. Su elección más reciente se recuerda y se ofrece de forma predeterminada la próxima vez que inicie sesión. Si alguna vez quiere volver a iniciar sesión con el teléfono, haga clic en **Use una aplicación en su lugar**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>¿Puedo usar la aplicación para iniciar sesión todas mis cuentas de Microsoft?   
Por ahora, esta funcionalidad solo está disponible para cuentas personales de Microsoft. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>¿Puedo iniciar sesión en mi PC con el teléfono?  
Para el PC, se recomienda iniciar sesión con Windows Hello de Windows 10 con la cara, la huella digital o un PIN.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>¿Puedo iniciar sesión con mi Windows Phone?  
En este momento, no estamos desarrollando esta funcionalidad para Microsoft Authenticator en Windows Phone. 

## <a name="next-steps"></a>Pasos siguientes
Si no se ha descargado la aplicación Microsoft Authenticator, échela un vistazo. La aplicación está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) y el inicio de sesión con el teléfono está disponible en la aplicación Microsoft Authenticator para [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si tiene alguna pregunta sobre la aplicación en general, eche un vistazo a [Preguntas más frecuentes de la aplicación Microsoft Authenticator](microsoft-authenticator-app-faq.md)
