---
title: "Profundización: SSPR de Azure AD | Microsoft Docs"
description: "Profundización del autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: cbb33988f5db47e35db406c037caa9133616d191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Profundización del autoservicio de restablecimiento de contraseña de Azure AD

Funcionamiento de SSPR ¿Qué significa la opción en la interfaz? Siga leyendo para más información acerca del autoservicio de restablecimiento de contraseña de Azure AD.

## <a name="how-does-the-password-reset-portal-work"></a>Funcionamiento del portal de restablecimiento de contraseñas

Cuando un usuario navega al portal de restablecimiento de contraseñas, se inicia un flujo de trabajo para determinar:

   * ¿Cómo se debe localizar la página?
   * ¿Es válida la cuenta de usuario?
   * ¿A qué organización pertenece el usuario?
   * ¿Dónde se administra la contraseña del usuario?
   * ¿Tiene el usuario licencia para usar la característica?


Lea los pasos siguientes para obtener información sobre la lógica de la página de restablecimiento de contraseña.

1. El usuario hace clic en el vínculo ¿No puede acceder a su cuenta? o va directamente a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2. Según la configuración regional del explorador la experiencia se representa en el idioma adecuado. La experiencia de restablecimiento de contraseña se localiza en los idiomas que admite Office 365.
3. El usuario escribe un identificador de usuario y pasa un captcha.
4. Azure AD comprueba si el usuario es capaz de utilizar esta característica; para ello, hace lo siguiente:
   * Comprueba que el usuario tiene esta característica habilitada y una licencia de Azure AD asignada.
     * Si el usuario no tiene esta característica habilitada o una licencia asignada, se solicita al usuario que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba que el usuario tiene los datos de comprobación correctos definidos en la cuenta según la directiva del administrador.
     * Si la directiva requiere solo una comprobación, se garantiza que el usuario tiene los datos correspondientes definidos para al menos una de las comprobaciones habilitadas por la directiva del administrador.
       * Si el usuario no está configurado, se le recomienda que se ponga en contacto con el administrador para restablecer la contraseña.
     * Si la directiva requiere dos comprobaciones, se garantiza que el usuario tiene los datos correspondientes definidos para al menos dos de las comprobaciones habilitadas por la directiva del administrador.
       * Si el usuario no está configurado, se le recomienda que se ponga en contacto con el administrador para restablecer la contraseña.
   * Comprueba si la contraseña del usuario se administra de forma local (federada o sincronizada con hash de contraseña).
     * Si la escritura diferida está implementada y la contraseña del usuario se administra de forma local, se le permite continuar con la autenticación y restablecer la contraseña.
     * Si la escritura diferida de contraseñas no está implementada y la contraseña del usuario se administra de forma local, se le pide que se ponga en contacto con el administrador para restablecer la contraseña.
5. Si se determina que el usuario puede restablecer correctamente la contraseña, se le guiará a través del proceso de restablecimiento.

## <a name="authentication-methods"></a>Métodos de autenticación

Si Autoservicio de restablecimiento de contraseña (SSPR) está habilitado, tiene que seleccionar al menos una de las opciones siguientes para los métodos de autenticación. Se recomienda encarecidamente elegir al menos dos métodos de autenticación para que los usuarios tengan más flexibilidad.

* Email
* Teléfono móvil
* Teléfono del trabajo
* Preguntas de seguridad

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Qué campos se usan en el directorio para los datos de autenticación

* Teléfono de la oficina corresponde al teléfono de la oficina
    * Los usuarios no pueden establecer este campo, lo debe definir un administrador
* Teléfono móvil corresponde al teléfono de autenticación (no visible públicamente) o teléfono móvil (visible públicamente)
    * El servicio busca primero el teléfono de autenticación y, a continuación, revierte al teléfono móvil si no está presente
* Dirección de correo electrónico alternativa corresponde al correo electrónico de autenticación (no visible públicamente) o al correo electrónico alternativo
    * El servicio busca primero el correo electrónico de autenticación y, después, revierte al correo electrónico alternativo

De forma predeterminada, solo los atributos de la nube Teléfono de la oficina y Teléfono móvil se sincronizan con su directorio en la nube desde el directorio local para los datos de autenticación.

Los usuarios solo pueden restablecer su contraseña si tienen datos en los métodos de autenticación que el administrador haya habilitado y requiera.

Si los usuarios no desea que su número de teléfono móvil esté visible en el directorio, pero desean usarlo para el restablecimiento de la contraseña, los administradores no deben rellenarlo en el directorio y el usuario debe rellenar su atributo **Teléfono de autenticación** a través del [portal de registro de restablecimiento de contraseña](http://aka.ms/ssprsetup). Los administradores pueden ver esta información en el perfil del usuario, pero no se publica en ningún otro lugar. Si una cuenta de administrador de Azure registra su número de teléfono de autenticación, este se rellena en el campo de teléfono móvil y está visible.

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticación requeridos

Esta opción determina el número mínimo de métodos de autenticación disponibles que un usuario debe superar para restablecer o desbloquear su contraseña, y se puede establecer en 1 o 2.

Los usuarios pueden elegir proporcionar más métodos de autenticación si el administrador los administra.

Si un usuario no tiene los métodos necesarios mínimos registrados, verá una página de error que le insta al solicitar un administrador que restablezca su contraseña.

### <a name="how-secure-are-my-security-questions"></a>Nivel de protección de las cuestiones de seguridad

Si usa preguntas de seguridad, se recomienda que se usen con otro método, ya que pueden ser menos seguras que otros métodos puesto que algunas personas pueden conocer las respuestas a las preguntas de otros usuarios.

> [!NOTE] 
> Las preguntas de seguridad se almacenan de manera privada y segura en un objeto de usuario del directorio y solo las pueden responder los usuarios durante el registro. No hay forma de que un administrador lea o modifique las preguntas o respuestas de un usuario.
>

### <a name="security-question-localization"></a>Localización de preguntas de seguridad

Todas las preguntas predefinidas que se indican a continuación están localizadas en el conjunto completo de idiomas de Office 365 en función de la configuración regional del explorador del usuario.

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

Las preguntas de seguridad personalizadas no se localizan para diferentes configuraciones regionales. Todas las preguntas personalizadas se muestran en el mismo idioma en que se escriben en la interfaz de usuario administrativa, aunque la configuración regional del explorador del usuario sea diferente. Si necesita preguntas localizadas, use las preguntas predefinidas.

La longitud máxima de una pregunta de seguridad personalizada es 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos de las preguntas de seguridad

* El límite mínimo de caracteres de las respuestas es de 3 caracteres
* El límite máximo de caracteres de las respuestas es de 40 caracteres
* Los usuarios no pueden responder a la misma pregunta más de una vez
* Los usuarios no pueden proporcionar la misma respuesta a más de una pregunta
* Se puede usar cualquier juego de caracteres para definir las preguntas y respuestas, incluidos los caracteres Unicode
* El número de preguntas que se definen debe ser mayor o igual que el número de preguntas necesarias para registrarse

## <a name="registration"></a>Registro

### <a name="require-users-to-register-when-signing-in"></a>Exigir a los usuarios que se registren al iniciar sesión

La habilitación de esta opción requiere que un usuario que tenga habilitado el restablecimiento de la contraseña complete el registro de restablecimiento de la contraseña si inicia sesión en aplicaciones mediante Azure AD para iniciarla desde los siguientes lugares:

* Office 365
* Azure Portal
* Panel de acceso
* Aplicaciones federadas
* Aplicaciones personalizadas mediante Azure AD

La deshabilitación de esta característica permitirá a los usuarios registrar manualmente su información de contacto de dos formas diferentes: visitando [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) o haciendo clic en el vínculo de **registro para el restablecimiento de contraseña**, que se encuentra en la pestaña del perfil del panel de acceso.

> [!NOTE]
> Para descartar el Portal de registro de restablecimiento de contraseña, los usuarios deben hace clic en Cancelar o cerrar la ventana, algo que se les solicita cada vez que inician sesión hasta que completan el registro.
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Número de días que pasan hasta que se pide a los usuarios que vuelvan a confirmar su información de autenticación

Esta opción determina el período que transcurre entre el establecimiento y la confirmación de la información de autenticación, y solo está disponible si se habilita la opción **¿Desea exigir a los usuarios que se registren al iniciar sesión?**

Los valores válidos son 0 - 730 días, 0 significa que no se pide a los usuarios que vuelvan a confirmar su información de autenticación

## <a name="notifications"></a>Notificaciones

### <a name="notify-users-on-password-resets"></a>¿Quiere notificar a los usuarios los restablecimientos de contraseña?

Si esta opción está establecida en Sí, el usuario que restablece la contraseña recibe un correo electrónico que le informa de que la contraseña se ha cambiado a través del portal de SSPR a sus direcciones de correo electrónico principal y alternativa en el archivo de Azure AD. A ningún otro usuario se le informa de este evento de restablecimiento.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificación a todos los administradores cuando otros administradores restablecen sus contraseñas

Si esta opción está establecida en Sí, **todos los administradores** reciben un correo electrónico en su dirección de correo electrónico principal del archivo de Azure AD en el que se les notifica que otro administrador ha cambiado su contraseña mediante SSPR.

Ejemplo: hay cuatro administradores en un entorno. El administrador "A" restablece su contraseña mediante SSPR. Los administradores B, C y D reciben un correo electrónico que les alertan de que esto ocurre.

## <a name="on-premises-integration"></a>Integración local

Si ha instalado, configurado y habilitado Azure AD Connect, tendrá más opciones para las integraciones locales.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Escritura diferida de contraseñas en un directorio local

Controla si la escritura diferida de contraseñas está habilitada en este directorio y, si lo está, indica el estado del servicio de escritura diferida local. Esto es útil si desea deshabilitar temporalmente la escritura diferida de contraseñas sin volver a configurar Azure AD Connect.

* Si el modificador se establece en Sí, se habilitará la escritura diferida y los usuarios federados y sincronizados por hash de contraseña podrán restablecer sus contraseñas.
* Si el modificador se establece en No, se deshabilitará la escritura diferida y los usuarios federados y sincronizados por hash de contraseña no podrán restablecer sus contraseñas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir a los usuarios desbloquear las cuentas sin restablecer la contraseña

Designa si los usuarios que visitan el portal de restablecimiento de contraseña tendrán la opción de desbloquear sus cuentas de Active Directory locales sin restablecer su contraseña. De forma predeterminada, Azure AD siempre desbloqueará las cuentas al realizar un restablecimiento de contraseña; esta opción le permite separar esas dos operaciones. 

* Si se establece en "sí", los usuarios tendrán la opción de restablecer su contraseña y desbloquear la cuenta, o de desbloquear la cuenta sin restablecer la contraseña.
* Si se establece en "no", los usuarios solo podrán realizar una operación combinada de restablecimiento de contraseña y desbloqueo de cuenta.

## <a name="network-requirements"></a>Requisitos de red

### <a name="firewall-rules"></a>Reglas de firewall

[Lista de direcciones IP y direcciones URL de Microsoft Office](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

En el caso de Azure AD Connect, versiones 1.1.443.0 y posteriores, se necesita acceso HTTPS saliente al siguiente
* passwordreset.microsoftonline.com
* servicebus.windows.net

Para obtener un acceso más detallado, [aquí](https://www.microsoft.com/download/details.aspx?id=41653) puede encontrar la lista actualizada de intervalos IP de centro de datos de Microsoft Azure Datacenter que se actualiza todos los miércoles y que entra en vigor el lunes siguiente.

### <a name="idle-connection-timeouts"></a>Tiempos de espera de conexiones inactivas

La herramienta Azure AD Connect envía pings o keepalives periódicos a los puntos de conexión de ServiceBus para garantizar que las conexiones se mantienen activas. Si la herramienta detecta que se están terminando demasiadas conexiones, automáticamente aumentará la frecuencia de envío de pings al punto de conexión. Los "intervalos de envío de pings" más bajos se reducen a 1 ping cada 60 segundos; sin embargo, se recomienda encarecidamente que los proxy o firewalls permitan que la conexiones inactivas persistan durante un mínimo de 2-3 minutos. *En el caso de las versiones anteriores, se sugiere una persistencia de 4 minutos, o más.

## <a name="active-directory-permissions"></a>Permisos de Active Directory

La cuenta especificada en la utilidad de Azure AD Connect debe tener derechos extendidos de restablecimiento de contraseña, cambio de contraseña, permisos de escritura en lockoutTime y permisos de escritura en pwdLastSet en el objeto raíz de **cada dominio** de ese bosque **O** en las unidades organizativas del usuario que desea que estén en el ámbito de SSPR.

Si no está seguro de cuál es la cuenta a la que se hace referencia en el párrafo anterior, abra la interfaz de usuario de la configuración de Azure Active Directory Connect y haga clic en la opción Ver la configuración actual. La cuenta a la que necesita agregar permiso se enumera en "Directorios sincronizados"

El establecimiento de estos permisos permite que la cuenta de servicio de agente de administración de cada bosque administre las contraseñas en nombre de las cuentas de usuario de dicho bosque. **Si no asigna estos permisos, aunque la escritura diferida parezca estar configurada correctamente, los usuarios encuentran errores al intentar administrar sus contraseñas locales desde la nube.**

> [!NOTE]
> Estos permisos pueden tardar una hora, o más, en replicarse en todos los objetos del directorio.
>

Para configurar los permisos adecuados para que se realice la escritura diferida de contraseñas

1. Abra Usuarios y equipos de Active Directory con una cuenta que tenga los permisos de administración de dominios adecuados
2. En el menú Ver, asegúrese de que la opción Características avanzadas está activada
3. En el panel izquierdo, haga clic con el botón derecho en el objeto que representa la raíz del dominio y elija Propiedades
    * Haga clic en la pestaña Seguridad
    * Luego, haga clic en Opciones avanzadas.
4. En la pestaña Permisos, haga clic en Agregar
5. Elija la cuenta a la que se van a aplicar los permisos (en el programa de instalación de Azure AD Connect)
6. En la lista desplegable Se aplica a, seleccione Descendent User objects (Objetos de usuario descendiente)
7. En permisos, active las casillas para los siguientes elementos
    * Contraseña sin expiración
    * Restablecimiento de contraseña
    * Cambiar contraseña
    * Escribir lockoutTime
    * Escribir pwdLastSet
8. Haga clic en Aplicar o Aceptar para aplicar los cambios y salir de los cuadros de diálogo abiertos.

## <a name="how-does-password-reset-work-for-b2b-users"></a>¿Cómo funciona el restablecimiento de contraseña para usuarios B2B?
El restablecimiento y cambio de contraseña son totalmente compatibles con todas las configuraciones de B2B.  Lea la información que encontrará a continuación para conocer los tres casos de B2B explícitos que admiten el restablecimiento de contraseña.

1. **Usuarios de una organización asociada con un inquilino de Azure AD existente**: si la organización con la que se asocia tiene un inquilino de Azure AD existente, **respetamos todas las directivas de restablecimiento de contraseña habilitadas en dicho inquilino**. Para que el restablecimiento de contraseña funcione, la organización del asociado simplemente necesita asegurarse de que la función SSPR de Azure AD esté habilitada, lo que no supone un cargo adicional para los clientes de Office 365. Se puede habilitar siguiendo los pasos descritos en nuestra guía [Introducción a la administración de contraseñas](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Usuarios registrados mediante el [registro de autoservicio](active-directory-self-service-signup.md)**: si la organización con la que se asocia usaba la característica de [registro de autoservicio](active-directory-self-service-signup.md) para acceder a un inquilino, les permitimos el restablecimiento con el correo electrónico que registraron.
3. **Usuarios de B2B**: los nuevos usuarios de B2B creados mediante las nuevas [funcionalidades de B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) también podrán restablecer sus contraseñas con el correo electrónico que registraron durante el proceso de invitación.

Para probarlo, vaya a http://passwordreset.microsoftonline.com con uno de estos usuarios asociados. Siempre que tengan un correo electrónico alternativo o un correo electrónico de autenticación definido, el restablecimiento de contraseña funcionará según lo esperado.

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planeamiento e implementación de SSPR para los usuarios con las directrices que aquí se proporcionan
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas
* [**Escritura diferida de contraseñas**](active-directory-passwords-writeback.md): cómo funciona la escritura diferida de contraseñas con el directorio local
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Informes**](active-directory-passwords-reporting.md): informes para detectar si los usuarios acceden a la funcionalidad de SSPR que especifican el momento y el lugar del acceso
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR

