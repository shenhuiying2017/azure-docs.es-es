---
title: "Funcionamiento del autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Profundización del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 8799931f6233f0f18b56bea39e3cbcbbf51274e9
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Profundización del autoservicio de restablecimiento de contraseña de Azure AD

¿Cómo funciona el autoservicio de restablecimiento de contraseña (SSPR)? ¿Qué significa la opción en la interfaz? Siga leyendo para obtener más información acerca de SSPR de Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>¿Cómo funciona el portal de restablecimiento de contraseñas?

Cuando un usuario visita el portal de restablecimiento de contraseña, se inicia un flujo de trabajo para determinar:

   * ¿Cómo se debe localizar la página?
   * ¿Es válida la cuenta de usuario?
   * ¿A qué organización pertenece el usuario?
   * ¿Dónde se administra la contraseña del usuario?
   * ¿Tiene el usuario licencia para usar la característica?

Lea los pasos siguientes para obtener información sobre la lógica de la página de restablecimiento de contraseña:

1. El usuario selecciona el vínculo **No se puede tener acceso a la cuenta** o visita directamente [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Según la configuración regional del explorador, la experiencia se representa en el idioma correspondiente. La experiencia de restablecimiento de contraseña está localizada en los mismos idiomas que admite Office 365.
2. El usuario escribe un identificador de usuario y pasa un CAPTCHA.
3. Azure AD comprueba que el usuario pueda utilizar esta característica y, para ello, hace las siguientes comprobaciones:
   * Comprueba que el usuario tenga esta característica habilitada y una licencia de Azure AD asignada.
     * Si el usuario no tiene esta característica habilitada o una licencia asignada, se le solicita que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba que el usuario tiene los datos de comprobación correctos definidos en la cuenta según la directiva del administrador.
     * Si la directiva solo requiere una comprobación, significa que garantiza que el usuario tiene los datos correspondientes definidos para al menos una de las comprobaciones habilitadas por la directiva del administrador.
       * Si la comprobación del usuario no está configurada, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
     * Si la directiva requiere dos comprobaciones, esta garantiza que el usuario tiene los datos correspondientes definidos para al menos dos de las comprobaciones habilitadas por la directiva del administrador.
       * Si la comprobación del usuario no está configurada, se recomienda al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba si la contraseña del usuario se administra en un entorno local (federado o sincronizado con hash de contraseña).
     * Si la escritura diferida está implementada y la contraseña del usuario se administra en un entorno local, el usuario puede continuar con la autenticación y restablecer la contraseña.
     * Si la escritura diferida no está implementada y la contraseña del usuario se administra en un entorno local, se pide al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
4. Si se determina que el usuario puede restablecer correctamente la contraseña, se le guiará a través del proceso de restablecimiento.

## <a name="authentication-methods"></a>Métodos de autenticación

Si SSPR está habilitado, tiene que seleccionar al menos una de las opciones siguientes para los métodos de autenticación. En ocasiones, se hace referencia a estas opciones como "puertas". Le recomendamos encarecidamente que elija al menos dos métodos de autenticación para que los usuarios tengan más flexibilidad.

* Email
* Teléfono móvil
* Teléfono del trabajo
* Preguntas de seguridad

![Autenticación][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>¿Qué campos se usan en el directorio para los datos de autenticación?

* **Teléfono de la oficina**: corresponde al teléfono de la oficina.
    * Los usuarios no pueden establecer este campo. Debe definirlo un administrador.
* **Teléfono móvil**: corresponde al teléfono de autenticación (no visible públicamente) o al teléfono móvil (visible públicamente).
    * El servicio primero busca el teléfono de autenticación y, si no está, recurre al teléfono móvil.
* **Dirección de correo electrónico alternativa**: corresponde al correo electrónico de autenticación (no visible públicamente) o al correo electrónico alternativo.
    * El servicio primero busca el correo electrónico de autenticación y, después, recurre al correo electrónico alternativo.

De forma predeterminada, solo los atributos de la nube del teléfono de la oficina y del teléfono móvil se sincronizan con su directorio en la nube desde el directorio local para los datos de autenticación.

Los usuarios solo pueden restablecer su contraseña si tienen datos en los métodos de autenticación que el administrador haya habilitado y requiera.

Si los usuarios no quieren que su número de teléfono móvil sea visible en el directorio, pero quieren seguir usándolo para restablecer la contraseña, los administradores no deben rellenar este campo en el directorio. Los usuarios deberían rellenar el atributo **Teléfono de autenticación** mediante el [portal de registro del restablecimiento de contraseña](http://aka.ms/ssprsetup). Los administradores pueden ver esta información en el perfil del usuario, pero no se publica en ningún otro lugar.

### <a name="the-number-of-authentication-methods-required"></a>Número de métodos de autenticación requeridos

Esta opción determina el número mínimo de métodos de autenticación disponibles o puertas que un usuario debe pasar para restablecer o desbloquear su contraseña. Se puede establecer en uno o dos.

Los usuarios pueden elegir proporcionar más métodos de autenticación si el administrador los habilita.

Si un usuario no tiene los métodos necesarios mínimos registrados, verá una página de error que le redirigirá a solicitar que un administrador restablezca su contraseña.

#### <a name="change-authentication-methods"></a>Cambio de métodos de autenticación

¿Qué sucede si empieza con una directiva que solo tiene registrado un método de autenticación requerido para el restablecimiento o el desbloqueo y cambia a dos métodos?

| Número de métodos registrados | Número de métodos requeridos | Resultado |
| :---: | :---: | :---: |
| 1 o más | 1 | **Permite** restablecer o desbloquear |
| 1 | 2 | **No permite** restablecer o desbloquear |
| 2 o más | 2 | **Permite** restablecer o desbloquear |

Si cambia los tipos de métodos de autenticación que puede usar un usuario, es posible que impida sin darse cuenta que los usuarios puedan usar SSPR si no tienen la cantidad mínima de datos disponibles.

Ejemplo: 
1. La directiva original se configura con dos métodos de autenticación necesarios. Solo usa el número de teléfono de la oficina y las preguntas de seguridad. 
2. El administrador cambia la directiva para dejar de usar las preguntas de seguridad, pero permite el uso de un teléfono móvil y de un correo electrónico alternativo.
3. Los usuarios que no rellenen los campos de teléfono móvil y correo electrónico alternativo no podrán restablecer sus contraseñas.

### <a name="how-secure-are-my-security-questions"></a>¿Cuál es el nivel de seguridad de mis preguntas de seguridad?

Si usa preguntas de seguridad, le recomendamos que las use junto con otro método. Las preguntas de seguridad pueden ser menos seguras que otros métodos porque es posible que algunas personas sepan las respuestas de las preguntas de otro usuario.

> [!NOTE] 
> Las preguntas de seguridad se almacenan de manera privada y segura en un objeto de usuario del directorio y solo las pueden responder los usuarios durante el registro. No hay forma de que un administrador lea o modifique las preguntas o respuestas de un usuario.
>

### <a name="security-question-localization"></a>Localización de preguntas de seguridad

Todas las preguntas predefinidas que se indican a continuación están localizadas en el conjunto completo de idiomas de Office 365 y se basan en la configuración regional del explorador del usuario:

* ¿En qué ciudad conoció a su cónyuge o pareja?
* ¿En qué ciudad se conocieron sus padres?
* ¿En qué ciudad vive su hermano más próximo?
* ¿En qué ciudad nació su padre?
* ¿En qué ciudad tuvo su primer trabajo?
* ¿En qué ciudad nació su madre?
* ¿En qué ciudad estaba en la Nochevieja del año 2000?
* ¿Cuál es el apellido de su profesor favorito del instituto?
* ¿En qué universidad solicitó plaza pero no asistió?
* ¿Cómo se llama el lugar en el que celebró su primer matrimonio?
* ¿Cuál es el segundo apellido de su padre?
* ¿Cuál es su comida favorita?
* ¿Cuál es el nombre y apellido de su abuela materna?
* ¿Cuál es el segundo apellido de su madre?
* ¿Cuáles son el año y mes de nacimiento del mayor de sus hermanos? (por ejemplo, noviembre de 1985)
* ¿Cuál es el segundo apellido del mayor de sus hermanos?
* ¿Cuál es el nombre y apellido de su abuelo paterno?
* ¿Cuántos años se lleva con el menor de sus hermanos?
* ¿En qué escuela cursó el sexto curso?
* ¿Cuál era el nombre y apellido de su mejor amigo de la infancia?
* ¿Cuál era el nombre y apellido de su primera pareja?
* ¿Cuál era el nombre de su maestro de primaria favorito?
* ¿Cuál era la marca y modelo de su primer coche o moto?
* ¿Cómo se llamaba la primera escuela a la que asistió?
* ¿Cómo se llamaba el hospital en el que nació?
* ¿Cuál era la calle de su primera casa de la infancia?
* ¿Cuál era el nombre de su héroe de la infancia?
* ¿Cuál era el nombre de su peluche favorito?
* ¿Cuál era el nombre de su primera mascota?
* ¿Cuál era su apodo en la infancia?
* ¿Cuál era su deporte favorito en el instituto?
* ¿Cuál fue su primer trabajo?
* ¿Cuáles eran los cuatro últimos números de su teléfono de la infancia?
* Cuando era joven, ¿qué quería ser de mayor?
* ¿Cuál es la persona más famosa que ha conocido?

### <a name="custom-security-questions"></a>Preguntas de seguridad personalizadas

Las preguntas de seguridad personalizadas no se localizan para diferentes configuraciones regionales. Todas las preguntas personalizadas se muestran en el mismo idioma en el que se escriben en la interfaz de usuario administrativa, aunque la configuración regional del explorador del usuario sea diferente. Si necesita preguntas localizadas, debería usar las preguntas predefinidas.

La longitud máxima de una pregunta de seguridad personalizada es 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos de las preguntas de seguridad

* El límite mínimo de caracteres de las respuestas es de tres.
* El límite máximo de caracteres de las respuestas es de 40.
* Los usuarios no pueden responder a la misma pregunta más de una vez.
* Los usuarios no pueden proporcionar la misma respuesta a más de una pregunta.
* Se puede usar cualquier juego de caracteres para definir las preguntas y respuestas, incluidos los caracteres Unicode.
* El número de preguntas que se definen debe ser mayor o igual que el número de preguntas que fueron necesarias para registrarse.

## <a name="registration"></a>Registro

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir a los usuarios que se registren al iniciar sesión

Para habilitar esta opción, un usuario que tenga habilitado el restablecimiento de contraseña tiene que completar el registro de restablecimiento de contraseña si inicia sesión en aplicaciones mediante Azure AD. Aquí se incluye lo siguiente:

* Office 365
* Azure Portal
* Panel de acceso
* Aplicaciones federadas
* Aplicaciones personalizadas mediante Azure AD

Cuando se deshabilita la opción para requerir el registro, los usuarios todavía pueden registrar manualmente su información de contacto. Pueden visitar [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) o seleccionar el vínculo **Registrarme para restablecer la contraseña** en la pestaña **Perfil** en el Panel de acceso.

> [!NOTE]
> Para descartar el portal de registro de restablecimiento de contraseña, los usuarios pueden seleccionar **Cancelar** o cerrar la ventana. Sin embargo, se les pide que se registren cada vez que inician sesión hasta que completan el registro.
>
> Esto no interrumpe la conexión del usuario si ya ha iniciado sesión.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Establecer el número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación

Esta opción determina el período que transcurre entre el establecimiento y la reconfirmación de la información de autenticación, y solo está disponible si se habilita la opción **¿Desea exigir a los usuarios que se registren al iniciar sesión?**

Los valores válidos son desde 0 hasta 730 días. 0 significa que no se pide a los usuarios que vuelvan a confirmar su información de autenticación.

## <a name="notifications"></a>Notificaciones

### <a name="notify-users-on-password-resets"></a>¿Quiere notificar a los usuarios los restablecimientos de contraseña?

Si esta opción está establecida en **Sí**, el usuario que restablece la contraseña recibe un correo electrónico que le informa de que la contraseña se ha cambiado. El correo electrónico se envía a través del portal de SSPR a las direcciones de correo electrónico principal y alternativa registradas en Azure AD. A ningún otro usuario se le informa de este evento de restablecimiento.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificación a todos los administradores cuando otros administradores restablecen sus contraseñas

Si esta opción está establecida en **Sí**, *todos los administradores* reciben un correo electrónico en la dirección de correo electrónico principal que tienen registrada en Azure AD. En el correo electrónico se les notifica que otro administrador ha cambiado su contraseña mediante SSPR.

Ejemplo: hay cuatro administradores en un entorno. El administrador A restablece su contraseña mediante SSPR. Los administradores B, C y D reciben un correo electrónico que les alerta del restablecimiento de contraseña.

## <a name="on-premises-integration"></a>Integración local

Si instala, configura y habilita Azure AD Connect, tiene las opciones adicionales siguientes para integraciones locales. Si estas opciones están atenuadas, significa que la escritura diferida no se ha configurado correctamente. Para obtener más información, consulte [Configuración de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configure-password-writeback).

![Escritura diferida][Writeback]

Esta página proporciona un estado rápido del cliente de escritura diferida con los siguientes mensajes que se muestran según la configuración actual:

* El cliente de escritura diferida local está en funcionamiento.
* Azure AD está en línea y conectado al cliente de escritura diferida local. Sin embargo, parece que la versión instalada de Azure AD Connect no está actualizada. Considere la posibilidad de [actualizar Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) para asegurarse de que tiene las características de conectividad más recientes y las correcciones de errores importantes.
* Desafortunadamente, de momento no podemos comprobar el estado del cliente de escritura diferida local porque la versión instalada de Azure AD Connect no está actualizada. [Actualice Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) para poder comprobar su estado de conexión.
* Desafortunadamente, parece que no podemos conectarnos a su cliente de escritura diferida local ahora mismo. [Solucione problemas de Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar la conexión.
* Desafortunadamente, no podemos conectarnos a su cliente de escritura diferida local porque la escritura diferida de contraseñas no se ha configurado correctamente. [Configure la escritura diferida de contraseñas](active-directory-passwords-writeback.md#configure-password-writeback) para restaurar la conexión.
* Desafortunadamente, parece que no podemos conectarnos a su cliente de escritura diferida local ahora mismo. Esto puede deberse a problemas temporales por nuestra parte. Si el problema persiste, [solucione los problemas de Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar la conexión.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Escritura diferida de contraseñas en un directorio local

Este control determina si la escritura diferida de contraseñas está habilitada para este directorio. Si lo está, indica el estado del servicio de escritura diferida local. Esto es útil si desea deshabilitar temporalmente la escritura diferida de contraseñas sin tener que volver a configurar Azure AD Connect.

* Si el modificador se establece en **Sí**, se habilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña pueden restablecer sus contraseñas.
* Si el modificador se establece en **No**, se deshabilita la escritura diferida y los usuarios federados y sincronizados por hash de contraseña no pueden restablecer sus contraseñas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir a los usuarios desbloquear las cuentas sin restablecer la contraseña

Este control designa si los usuarios que visitan el portal de restablecimiento de contraseña tendrán la opción de desbloquear sus cuentas de Active Directory locales sin tener que restablecer su contraseña. De forma predeterminada, Azure AD desbloquea las cuentas cuando se realiza un restablecimiento de contraseña. Use esta opción para separar esas dos operaciones. 

* Si se establece en **Sí**, los usuarios tienen la opción de restablecer su contraseña y desbloquear la cuenta, o de desbloquear la cuenta sin tener que restablecer la contraseña.
* Si se establece en **No**, los usuarios solo pueden realizar una operación combinada de restablecimiento de contraseña y desbloqueo de cuenta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>¿Cómo funciona el restablecimiento de contraseña para usuarios B2B?
El restablecimiento y el cambio de contraseña son totalmente compatibles con todas las configuraciones negocio a negocio (B2B). Se admiten los tres casos siguientes para el restablecimiento de contraseña de usuario B2B:

   * **Usuarios de una organización asociada con un inquilino de Azure AD existente**: si la organización con la que se asocia tiene un inquilino de Azure AD existente, *respetamos todas las directivas de restablecimiento de contraseña habilitadas en dicho inquilino*. Para que el restablecimiento de contraseña funcione, la organización asociada solo tiene que asegurarse de que SSPR de Azure AD está habilitado. No hay ningún cargo adicional para los clientes de Office 365 y puede habilitarse siguiendo los pasos descritos en nuestra guía [Introducción a la administración de contraseñas](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
   * **Usuarios que se registran mediante el** registro de autoservicio: si la organización con la que se asocia usó la característica de [registro de autoservicio](active-directory-self-service-signup.md) para acceder a un inquilino, le permitimos el restablecimiento de contraseña con el correo electrónico que registró.
   * **Usuarios B2B**: los nuevos usuarios B2B creados mediante las nuevas [funcionalidades B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) también podrán restablecer sus contraseñas con el correo electrónico que registraron durante el proceso de invitación.

Para probar este escenario, vaya a http://passwordreset.microsoftonline.com con uno de estos usuarios asociados. Si tienen un correo electrónico alternativo o de autenticación definido, el restablecimiento de contraseña funcionará según lo esperado.

> [!NOTE]
> Las cuentas de Microsoft a las que se ha concedido acceso de invitado para su inquilino de Azure AD, como las de Hotmail.com, Outlook.com u otras direcciones de correo electrónico personales, no pueden usar SSPR de Azure AD. Para restablecer su contraseña, se debe usar la información que se encuentra en el artículo [Cuando no puedes iniciar sesión en tu cuenta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>pasos siguientes

En los siguientes artículos se proporciona información adicional sobre el restablecimiento de contraseña con Azure AD:

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Métodos de autenticación de Azure AD disponibles y cantidad requerida"
[Writeback]: ./media/active-directory-passwords-how-it-works/troubleshoot-writeback-running.png "Información de la configuración de la escritura diferida de contraseñas con integración local y solución de problemas"
