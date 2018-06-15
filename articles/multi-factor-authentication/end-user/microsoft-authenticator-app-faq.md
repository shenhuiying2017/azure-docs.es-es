---
title: Ayuda y soporte técnico para la aplicación Microsoft Authenticator | Microsoft Docs
description: Proporciona una lista de las preguntas y respuestas más frecuentes relacionadas con la aplicación Microsoft Authentication y Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: e9c152fddbfcd603a84caf2c168ec4f12368dcfa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795874"
---
# <a name="microsoft-authenticator-app-faq"></a>Preguntas más frecuentes de la aplicación Microsoft Authenticator

En este artículo se proporcionan respuestas a preguntas habituales sobre la aplicación Microsoft Authenticator. Si no ve una respuesta a su pregunta, vaya al [foro de la aplicación Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). También puede revisar otras preguntas frecuentes sobre una característica específica de la aplicación: la de [inicio de sesión con el teléfono](microsoft-authenticator-app-phone-signin-faq.md).

La aplicación Microsoft Authenticator reemplaza la aplicación Azure Authenticator y es la aplicación recomendada cuando se utiliza Azure Multi-Factor Authentication. La aplicación Microsoft Authenticator está disponible para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) y [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-data-does-the-authenticator-store-on-my-behalf-and-how-can-i-delete-it"></a>¿Qué datos almacena Authenticator en mi nombre y cómo puedo eliminarlo?

Microsoft Authenticator almacena la información de la cuenta creada al agregar una cuenta. Cuando se usa Authenticator, se crea un registro de diagnóstico para fines de depuración y se almacenan datos útiles para ayudar a Microsoft a diagnosticar cualquier problema imprevisto. Puede acceder a los datos del registro si abre **Ayuda** > **Enviar registros** > **Ver registros**.

Puede eliminar los datos si elimina el icono de la cuenta. Al eliminar el icono de la cuenta también se elimina toda la información de la cuenta que la aplicación utiliza, incluidos los registros. 

Para más información sobre cómo Microsoft usa los datos, visite https://servicetrust.microsoft.com/ViewPage/PrivacyGettingStarted

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>¿Para qué sirven los códigos de la aplicación? ¿Por qué el número es regresivo?

Cuando se abre la aplicación Microsoft Authenticator, se ven las cuentas que ha agregado y un número de seis u ocho dígitos junto a cada una de ellas. Es posible que vea un temporizador de 30 segundos que cuenta de manera regresiva.

Estos códigos se usan cuando inicia sesión en la cuenta. Después de escribir el nombre de usuario y la contraseña, puede que se le pida que especifique un código de verificación. Abra la aplicación Microsoft Authenticator y copie el código que se mostrando. Introduzca ese código en la página de inicio de sesión para finalizar.

El motivo por el que los códigos cambian cada 30 segundos es para que nunca se utilice el mismo código dos veces. No es lo mismo que una contraseña, que se supone que debe recordar. La idea es que solo un usuario con acceso a su teléfono conoce el código de verificación.

Los códigos no requieren Internet ni datos, por lo que no tiene que preocuparse por tener el servicio de teléfono para iniciar sesión. Cuando se cierra la aplicación, esta no sigue ejecutándose en segundo plano ni agota la batería. Puede cerrar la aplicación e ignorarla hasta la próxima vez que inicie sesión.  

### <a name="i-only-get-notifications-when-i-have-the-app-open-if-the-app-isnt-open-i-dont-get-any-notifications"></a>Solo recibo notificaciones cuando tengo la aplicación abierta. Si la aplicación no está abierta, no recibo ninguna notificación.

Si recibe notificaciones, pero no realizan ningún ruido ni viran vibrar a pesar de que el timbre esté activado, primero compruebe la configuración de la aplicación. Habilite la aplicación para que use un sonido o vibre con sus notificaciones.

Si no recibe las notificaciones, compruebe los siguientes casos:

- ¿El teléfono se encuentra en modo silencioso o no molestar? Ese modo puede evitar que las aplicaciones envíen notificaciones.
- ¿Puede recibir notificaciones de otras aplicaciones? De lo contrario, puede haber un problema con las conexiones de red en su teléfono o el canal de notificaciones de Android o Apple. Puede solucionar el primer caso en la configuración del teléfono, pero puede que necesite hablar con su proveedor de servicios para obtener ayuda con el segundo caso.
- ¿Puede recibir notificaciones de algunas cuentas en la aplicación, pero no de otras? Si es así, quite la cuenta problemática de la aplicación y agréguela de nuevo para habilitar las notificaciones push.

Si ha probado estas sugerencias de solución de problemas, pero sigue teniéndolos, envíenos sus registros para el diagnóstico. Vaya a la configuración de la aplicación y seleccione **Ayuda y comentarios**, y **Enviar registros**. A continuación, vaya al [foro de la aplicación Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) y háganos saber cuál es el problema y qué pasos ha probado.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Ya uso la aplicación Microsoft Authenticator con los códigos de comprobación. ¿Cómo cambio a las notificaciones push de un solo clic?
La aprobación de un inicio de sesión mediante notificaciones push solo está disponible para las cuentas personales, profesionales y educativas de Microsoft. No está disponible para cuentas de terceros, como Google o Facebook. Si tiene una cuenta profesional o educativa de Microsoft, su organización puede deshabilitar esta opción.

Si usa una cuenta de Microsoft como cuenta personal y quiere cambiar a las notificaciones push, deberá volver a agregar su cuenta. Vuelva a registrar el dispositivo con su cuenta y configure las notificaciones de inserción.  

Si usa Microsoft Authenticator para su cuenta profesional o educativa, su organización decide si debe permitir notificaciones de un solo clic.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>¿Las notificaciones push de un solo clic funcionan con cuentas que no sean de Microsoft?
No, las notificaciones de inserción solo funcionan con cuentas de Microsoft y de Azure Active Directory. Si su trabajo o centro educativo utiliza cuentas de Azure AD, se puede deshabilitar esta característica.  

### <a name="i-got-a-new-device-or-restored-my-device-from-a-backup-how-do-i-set-up-my-accounts-in-the-microsoft-authenticator-app-again"></a>Obtuve un dispositivo nuevo o restauré mi dispositivo desde una copia de seguridad. ¿Cómo configuro mis cuentas en la aplicación Microsoft Authenticator de nuevo?
Si ejecuta un dispositivo iOS, ha activado **Copia de seguridad automática** y ha creado una copia de seguridad de las cuentas en su dispositivo antiguo, puede usar esa copia de seguridad para recuperar las credenciales de cuenta en el dispositivo nuevo. Para más información, consulte el artículo [Copia seguridad y recuperación de las credenciales de cuenta con la aplicación Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md). 

### <a name="i-lost-my-device-or-moved-on-to-a-new-device-how-do-i-make-sure-notifications-dont-continue-to-go-to-my-old-device"></a>He perdido mi dispositivo o lo cambié por uno nuevo. ¿Cómo puedo asegurarme de que las notificaciones no siguen yendo a mi dispositivo antiguo?  
Al agregar la aplicación Microsoft Authenticator al nuevo dispositivo iOS no se elimina automáticamente la aplicación del antiguo. Eliminar la aplicación del dispositivo antiguo no es suficiente. Debe eliminar la aplicación de dispositivo antiguo e indicar Microsoft o su organización que olvida lo olvide y anule el registro de su cuenta.
- **Para eliminar la aplicación de un dispositivo con una cuenta Microsoft personal.** Vaya al área de verificación en dos pasos de la página de [seguridad de la cuenta](https://account.microsoft.com/security)  y elija desactivar la verificación del dispositivo antiguo.  
- **Para quitar la aplicación de un dispositivo con una cuenta Microsoft profesional o educativa.** Vaya al área de verificación en dos pasos de la página [MyApps](https://myapps.microsoft.com/) o el portal personalizado de la organización y elija desactivar la verificación del dispositivo antiguo. 



### <a name="how-do-i-remove-an-account-from-the-app"></a>¿Cómo se quita una cuenta de la aplicación?
* iOS: desde la pantalla principal, deslice el dedo hacia la izquierda en un icono de cuenta. Seleccione **Eliminar**.
* Windows Phone: en la pantalla principal, seleccione el botón de menú y luego **Editar cuentas**. Pulse la **X** junto al nombre de la cuenta.
* Android: en la pantalla principal, seleccione el botón de menú y luego **Editar cuentas**. Pulse la **X** junto al nombre de la cuenta.

Si tiene un dispositivo que está registrado en su organización, puede que deba realizar un paso adicional para quitar la cuenta. En estos dispositivos, la aplicación Microsoft Authenticator se registra automáticamente como administrador del dispositivo. Si quiere desinstalar totalmente la aplicación, primero debe anular el registro de la aplicación en la configuración de la aplicación.

### <a name="why-does-the-app-request-so-many-permissions"></a>¿Por qué la aplicación solicita tantos permisos?
Esta es una lista completa de permisos que se pueden solicitar y una explicación de cómo se utilizan en la aplicación. Los permisos específicos que se verá dependerán del tipo de teléfono que tenga.

* **Cámara**: se usa para digitalizar códigos QR al agregar una cuenta Microsoft profesional, educativa o de otro tipo.
* **Contactos y teléfono**: simplifica el proceso mediante la búsqueda de cuentas existentes en el teléfono al iniciar sesión con la cuenta Microsoft personal.
* **SMS**: sirve para asegurarse de que el número de teléfono coincide con el número registrado. Al iniciar sesión con la cuenta Microsoft personal por primera vez.  Enviamos un mensaje de texto al teléfono donde descargó la aplicación con un código de verificación de 6-8 caracteres. En lugar de pedirle que busque este código y que lo escriba en la aplicación, lo buscamos en el mensaje de texto por usted.
* **Extensión a otras aplicaciones**: la notificación que verifica su identidad también aparece en cualquier otra aplicación en ejecución.
* **Recibir datos de Internet**: este permiso es necesario para enviar notificaciones.
* **Impedir que el teléfono entre en modo de suspensión**: si registra el dispositivo con su organización, la organización puede cambiar esta directiva en su teléfono.
* **Controlar la vibración**: puede elegir si quiere que el dispositivo vibre cada vez que se recibe una notificación para comprobar su identidad.
* **Usar hardware de huella digital**: algunas cuentas profesionales o educativas requieran un PIN adicional cada vez que comprueba su identidad. PARA facilitar el proceso, se permite el uso de la huella digital en lugar de escribir el PIN.
* **Ver conexiones de red**: cuando se agrega una cuenta de Microsoft, la aplicación requiere conexión de red o a Internet.
* **Leer el contenido del almacenamiento**: este permiso solo se usa al notificar un problema técnico a través de la configuración de la aplicación. Parte de la información de su almacenamiento se recopila para diagnosticar el problema.
* **Acceso completo a la red**: este permiso es necesario para enviar notificaciones para comprobar tu identidad.
* **Ejecutar al inicio**: si reinicia el teléfono, este permiso garantiza que continuará recibiendo notificaciones para comprobar su identidad.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>¿Por qué la aplicación Microsoft Authenticator permite aprobar una solicitud sin desbloquear el servicio?

No tiene que desbloquear el dispositivo para aprobar solicitudes de comprobación porque todo lo que necesita demostrar es que lleva el teléfono encima. La verificación en dos pasos realiza dos comprobaciones: una comprobación de algo que sabe y una comprobación de algo que tiene. Lo que sabe es la contraseña. Lo que tiene es el teléfono (configurado con la aplicación Microsoft Authenticator y registrado como una prueba de MFA). Por tanto, al tener el teléfono y aprobar la solicitud, se cumplen los criterios del segundo factor de la autenticación.

### <a name="what-does-the-lock-icon-in-the-account-list-mean"></a>¿Qué significa el icono de candado en la lista de cuentas?

El icono de candado indica que el dispositivo está registrado en Azure AD y registrado en la cuenta. El registro de dispositivos para iOS tiene lugar durante la inscripción de Microsoft Intune.

## <a name="next-steps"></a>Pasos siguientes

### <a name="contact-us"></a>Ponerse en contacto con nosotros
Si no encontró aquí la respuesta a su pregunta, nos gustaría ayudarle. Acceda al [foro de la aplicación Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) para publicar la pregunta y obtener ayuda de la comunidad, para dejar un comentario en la página.


### <a name="related-topics"></a>Temas relacionados
* [Acerca de la comprobación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) de cuentas de Microsoft
* [Problemas con la verificación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md) de la cuenta profesional o educativa
* [Uso de Microsoft Authenticator para iniciar sesión desde el teléfono](microsoft-authenticator-app-phone-signin-faq.md)
