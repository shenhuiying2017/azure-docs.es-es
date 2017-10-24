---
title: "Preguntas más frecuentes: SSPR de Azure AD | Microsoft Docs"
description: "Preguntas más frecuentes sobre autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, autoservicio de restablecimiento de contraseña de Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a457ba3109625ab43ae3a88b95a3e7e1e9641921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="password-management-frequently-asked-questions"></a>Preguntas más frecuentes sobre la administración de contraseñas

A continuación se indican algunas de las preguntas más frecuentes sobre todos los aspectos relacionados con el restablecimiento de contraseñas.

Si tiene alguna pregunta general sobre Azure AD y el autoservicio de restablecimiento de contraseña y no encuentra una respuesta aquí, puede pedir ayuda a la comunidad en los [foros de Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Los miembros de la comunidad están formados por ingenieros, jefes de producto, MVP y profesionales de TI.

Estas preguntas más frecuentes se dividen en las siguientes secciones:

* [**Preguntas sobre el registro de restablecimiento de contraseña**](#password-reset-registration)
* [**Preguntas sobre el restablecimiento de contraseña**](#password-reset)
* [**Preguntas sobre el cambio de contraseña**](#password-change)
* [**Preguntas sobre los informes de la administración de contraseñas**](#password-management-reports)
* [**Preguntas sobre la escritura diferida de contraseñas**](#password-writeback)

## <a name="password-reset-registration"></a>Registro de restablecimiento de contraseña

* **P: ¿Mis usuarios pueden registrar sus propios datos de restablecimiento de contraseña?**

  > **R:** Sí, siempre que el restablecimiento de contraseña esté habilitado y que los usuarios cuenten con licencia, pueden ir al portal de Registro de restablecimiento de contraseña en http://aka.ms/ssprsetup para registrar la información de autenticación. Para registrarse, los usuarios también pueden ir al panel de acceso en http://myapps.microsoft.com, hacer clic en la pestaña de perfil y en la opción Registrarme para restablecer la contraseña.
  >
  >
* **P: ¿Puedo definir los datos de restablecimiento de contraseña en nombre de mis usuarios?**

  > **R:** Sí, puede hacerlo en Azure AD Connect, PowerShell, [Azure Portal](https://portal.azure.com) o el Portal de administración de Office. Para más información, vea el artículo [Datos usados en el autoservicio de restablecimiento de contraseña de Azure AD](active-directory-passwords-data.md).
  >
  >
* **P: ¿Puedo sincronizar localmente los datos de las preguntas de seguridad?**

  > **R.:** No es posible hacerlo actualmente.
  >
  >
* **P: ¿Mis usuarios pueden registrar datos de manera tal que otros usuarios no puedan verlos?**

  > **R:** Sí, cuando los usuarios registran datos a través del portal de Registro de restablecimiento de contraseña, se guardan en campos de autenticación privados que solo son visibles para los administradores globales y para el propio usuario.
    >
    > [!NOTE]
    > Si una **cuenta de administrador de Azure** registra su número de teléfono de autenticación, este también se rellena en el campo de teléfono móvil y está visible.
    >
  >
  >
* **P: ¿Mis usuarios necesitan registrarse antes de poder usar el restablecimiento de contraseña?**

  > **R:** No. Si define información de autenticación suficiente en nombre de sus usuarios, estos no tienen que registrarse. El restablecimiento de contraseña funciona siempre que tenga datos con formato correcto almacenados en los campos adecuados del directorio.
  >
  >
* **P: ¿Puedo sincronizar o definir los campos Teléfono de autenticación, Correo electrónico de autenticación o Teléfono de autenticación alternativo en nombre de mis usuarios?**

  > **R.:** No es posible hacerlo actualmente.
  >
  >
* **P: ¿Cómo sabe el portal de registro cuáles son las opciones que tiene que mostrar a mis usuarios?**

  > **R:** En el portal de registro de restablecimiento de contraseña solo se muestran las opciones habilitadas para los usuarios. Estas opciones se encuentran en la sección Políticas para restablecer la contraseña del usuario en la pestaña Configurar del directorio. Por ejemplo, esto significa que, si no habilita las preguntas de seguridad, los usuarios no pueden registrarse para obtener esa opción.
  >
  >
* **P: ¿Cuándo se considera que un usuario está registrado?**

  > **R:** Se considera que un usuario está registrado en SSPR si ha registrado al menos el **Número de métodos requeridos para el restablecimiento** establecido en [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Restablecimiento de contraseña

* **P: ¿Cuánto tiempo tengo que esperar para recibir un correo electrónico, un SMS o una llamada telefónica para el restablecimiento de contraseña?**

  > **R:** Los correos electrónicos, los mensajes SMS y las llamadas telefónicas deberían llegar en un minuto; lo normal sería que tardaran entre 5 y 20 segundos.
    >Si no recibe la notificación en este período de tiempo:
        > * Compruebe la carpeta de correo no deseado.
        > * Compruebe que el número o el correo electrónico de contacto son los que espera.
        > * Compruebe que los datos de autenticación del directorio tienen el formato correcto.
                >     * Ejemplo: "+1 4255551234" o "user@contoso.com"
  >
  >
* **P: ¿Qué idiomas admite el restablecimiento de contraseña?**

  > **R:** La interfaz de usuario del restablecimiento de contraseña, los mensajes SMS y las llamadas de voz están localizados en los mismos idiomas que admite Office 365.
  >
  >
* **P: ¿En qué partes de la experiencia de restablecimiento de contraseña aparece mi marca si defino la personalización de marca de mi organización en la pestaña de configuración del directorio?**

  > **R:** El portal de restablecimiento de contraseña muestra el logotipo de su organización y también le permite configurar el vínculo Póngase en contacto con el administrador para dirigirlo a una dirección URL o a un correo electrónico personalizado. Todo correo electrónico enviado por el proceso de restablecimiento de contraseña incluye el logotipo, los colores y el nombre de su organización en el cuerpo del correo electrónico y se personaliza a partir del nombre.
  >
  >
* **P: ¿Cómo puedo informar a mis usuarios sobre dónde tienen que ir para restablecer sus contraseñas?**

  > **R:** Pruebe algunas de las sugerencias de nuestro [artículo sobre la implementación del autoservicio de restablecimiento de contraseña (SSPR)](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **P: ¿Puedo usar esta página desde un dispositivo móvil?**

  > **R.:** Sí, esta página funciona en dispositivos móviles.
  >
  >
* **P: ¿Se admite el desbloqueo de cuentas locales de Active Directory cuando los usuarios restablecen sus contraseñas?**

  > **R:** Sí, cuando un usuario restablece la contraseña y se ha implementado la escritura diferida de contraseñas mediante Azure AD Connect, esa cuenta de usuario se desbloquea automáticamente al restablecer la contraseña.
  >
  >
* **P: ¿Cómo puedo integrar directamente el restablecimiento de contraseña en la experiencia de inicio de sesión de escritorio de mi usuario?**

  > **R:** Si es cliente de Azure AD Premium, puede instalar Microsoft Identity Manager sin costo adicional e implementar la solución de restablecimiento de contraseña local quera satisfacer este requisito.
  >
  >
* **P: ¿Puedo establecer distintas preguntas de seguridad para diferentes configuraciones regionales?**

  > **R.:** No es posible hacerlo actualmente.
  >
  >
* **P: ¿Cuántas preguntas podemos configurar en la opción de autenticación Preguntas de seguridad?**

  > **R:** Puede configurar hasta 20 preguntas de seguridad personalizadas en [Azure Portal](https://portal.azure.com).
  >
  >
* **P: ¿Qué longitud pueden tener las preguntas de seguridad?**

  > **R.:** Las preguntas de seguridad pueden tener entre 3 y 200 caracteres.
  >
  >
* **P: ¿Qué longitud pueden tener las respuestas a las preguntas de seguridad?**

  > **R.:** Las respuestas pueden tener entre 3 y 40 caracteres.
  >
  >
* **P: ¿Se rechazarán las respuestas duplicadas a las preguntas de seguridad?**

  > **R.:** Sí, rechazaremos las respuestas duplicadas a las preguntas de seguridad.
  >
  >
* **P: ¿Puede un usuario registrar la misma pregunta de seguridad más de una vez?**

  > **R:** No. Una vez que un usuario registra una pregunta específica, no puede registrar esa pregunta por segunda vez.
  >
  >
* **P: ¿Es posible establecer un límite mínimo de preguntas de seguridad para registro y restablecimiento?**

  > **R.:** Sí, es posible establecer un límite para el registro y otro para el restablecimiento. Es posible que se requieran entre 3 y 5 preguntas para el registro y entre 3 y 5 para el restablecimiento.
  >
  >
* **P: He configurado mi directiva para que requiera que los usuarios utilicen preguntas de seguridad para realizar el restablecimiento, pero parece que los administradores de Azure están configurados de forma diferente.**

  > **R:** Este es un comportamiento esperado. Microsoft exige una directiva segura de restablecimiento de contraseña de dos puertas de forma predeterminada para cualquier rol de administrador de Azure. De este modo se impide que los administradores usen preguntas de seguridad. Para más información acerca de esta directiva consulte [Restricciones y directivas de contraseñas en Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences)
  >
  >
* **P: Si un usuario ha registrado más del número máximo de preguntas necesarias para el restablecimiento, ¿cómo se seleccionan las preguntas de seguridad durante el proceso de restablecimiento?**

  > **R:** De manera aleatoria se seleccionan N preguntas de seguridad del número total de preguntas para las cuales se ha registrado un usuario, donde N es el **Número de preguntas necesarias para el restablecimiento**. Por ejemplo, si un usuario tiene registradas 5 preguntas de seguridad, pero solo se requieren 3 para el restablecimiento, 3 de esas 5 se seleccionan de manera aleatoria y se presentan en el momento del restablecimiento. Si el usuario se equivoca al responder las preguntas, el proceso de selección vuelve a ejecutarse para evitar la repetición (hammering) de las preguntas.
  >
  >
* **P: ¿Es posible evitar que los usuarios intenten restablecer la contraseña muchas veces en un período de tiempo breve?**

  > **R:** Sí, existen características de seguridad integradas en el restablecimiento de contraseña a fin de ofrecer protección frente al uso indebido. Los usuarios solo pueden intentar 5 restablecimientos de contraseña en un período de una hora antes de que se les bloquee durante 24 horas. Los usuarios solo pueden intentar validar un número de teléfono 5 veces dentro de una hora antes de que se les bloquee durante 24 horas. Los usuarios solo pueden intentar un método de autenticación 5 veces dentro de una hora antes de que se les bloquee durante 24 horas.
  >
  >
* **P: ¿Durante cuánto tiempo es válido el código de acceso de un solo uso que recibe por correo electrónico o mensaje SMS?**

  > **R.:** La duración de la sesión de restablecimiento de contraseña es de 15 minutos. Desde el principio de la operación de restablecimiento de contraseña, el usuario tiene 15 minutos para restablecer la contraseña. El código de acceso de un solo uso que recibe por correo electrónico o mensaje SMS no es válido después de este período de tiempo.
  >
  >

## <a name="password-change"></a>Cambio de contraseña

* **P: ¿Dónde deberían ir los usuarios para cambiar las contraseñas?**

  > **R:** Los usuarios pueden cambiar sus contraseñas en cualquier lugar en que vean su icono o imagen de perfil (como en la esquina superior derecha de las experiencias de [Office 365](https://portal.office.com) o el [Panel de acceso](https://myapps.microsoft.com)). Los usuarios pueden cambiar las contraseñas en la [página de perfil del Panel de acceso](https://account.activedirectory.windowsazure.com/r#/profile). Los usuarios también deben cambiar automáticamente sus contraseñas en la pantalla de inicio de sesión de Azure AD en caso de que hayan expirado. Por último, los usuarios pueden navegar directamente al [portal de cambio de contraseñas de Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) si desean cambiar las contraseñas.
  >
  >
* **P: ¿Los usuarios pueden recibir una notificación en el Portal de Office cuando expira la contraseña local?**

  > **R:** esto es posible actualmente si usa ADFS y sigue estas instrucciones: [Sending Password Policy Claims with ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) (Envío de notificaciones de directiva de contraseña con ADFS). Esto no es posible hoy si usa la sincronización de hash de contraseña. Esto se debe a que no sincronizamos las directivas de contraseña en el entorno local, por lo que no es posible publicar notificaciones de expiración en las experiencias en la nube. En cualquier caso, también es posible [notificar a los usuarios cuyas contraseñas están a punto de expirar a través de PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >

## <a name="password-management-reports"></a>Informes de administración de contraseñas

* **P: ¿Cuánto tiempo demoran en aparecer los datos en los informes de la administración de contraseñas?**

  > **R.:** Los datos deben aparecer en los informes de la administración de contraseñas en un plazo de entre 5 y 10 minutos. En algunas instancias, es posible que demoren hasta una hora en aparecer.
  >
  >
* **P: ¿Cómo puedo filtrar los informes de administración de contraseñas?**

  > **R:** Para filtrar los informes de administración de contraseñas, haga clic en la lupa pequeña que aparece en el extremo derecho de las etiquetas de columna, cerca de la parte superior del informe. Si desea realizar un filtrado más completo, puede descargar el informe a Excel y crear una tabla dinámica.
  >
  >
* **P: ¿Cuál es el número máximo de eventos que se almacenan en los informes de administración de contraseñas?**

  > **R:** Hasta 75 000 eventos de restablecimiento de contraseña o de registro de restablecimiento de contraseña se almacenan en los informes de administración de contraseñas, los que abarcan hasta los últimos 30 días.  Estamos trabajando para ampliar este número e incluir más eventos.
  >
  >
* **P: ¿Hasta cuándo se remontan los informes de administración de contraseñas?**

  > **R.:** Los informes de administración de contraseñas muestran las operaciones generadas dentro de los últimos 30 días. Por ahora, si desea archivar estos datos, puede descargar periódicamente los informes y guardarlos en una ubicación independiente.
  >
  >
* **P: ¿Existe un número máximo de filas que pueden aparecer en los informes de administración de contraseñas?**

  > **R.:** Sí, pueden aparecer 75 000 filas como máximo en cualquiera de los informes de administración de contraseñas, tanto si se muestran en la interfaz de usuario o se descargan.
  >
  >
* **P: ¿Existe una API para acceder a los datos de informes de registro o de restablecimiento de contraseña?**

  > **R:** Sí, consulte la documentación siguiente para saber cómo puede tener acceso al flujo de datos de informes de restablecimiento de contraseña.  [Obtenga información sobre cómo tener acceso a los eventos de informes de restablecimiento de contraseña mediante programación](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Escritura diferida de contraseñas

* **P: ¿Cómo funciona la escritura diferida de contraseñas en segundo plano?**

  > **R:** Consulte [Funcionamiento de la escritura diferida de contraseñas](active-directory-passwords-writeback.md) para obtener una explicación de lo que ocurre cuando se habilita la escritura diferida de contraseñas, además de cómo fluyen los datos por el sistema para volver al entorno local.
  >
  >
* **P: ¿Cuánto tarda en funcionar la escritura diferida de contraseñas?  ¿Existe un retraso en la sincronización, como ocurre con la sincronización de hash de contraseña?**

  > **R.:** La escritura diferida de contraseñas es inmediata. Se trata de una canalización sincrónica que funciona radicalmente distinto a la sincronización de hash de contraseña. La escritura diferida de contraseñas permite que los usuarios obtengan comentarios en tiempo real sobre el éxito de su operación de cambio o restablecimiento de contraseña. El tiempo promedio para la escritura diferida correcta de una contraseña es de menos de 500 ms.
  >
  >
* **P: Si mi cuenta local está deshabilitada, ¿en qué afecta a mi acceso y a mi cuenta en la nube?**

  > **R:** Si el identificador local está deshabilitado, el acceso y el identificador de la nube también se deshabilitarán durante el próximo intervalo de sincronización a través de AAD Connect, que tiene lugar cada 30 minutos de forma predeterminada.
  >
  >
* **P: Si mi cuenta local está restringida por una directiva de contraseñas de Active Directory local, ¿SSPR se atiene a esta directiva si cambio la contraseña?**

  > **R:** Sí, SSPR se basa y se rige en virtud de la directiva de contraseñas de AD local, incluidas la directiva de contraseñas de dominio típica de AD y cualquier directiva de contraseña muy específica orientada a un usuario determinado.
  >
  >
* **P: ¿Para qué tipos de cuentas funciona la escritura diferida de contraseñas?**

  > **R:** La escritura diferida de contraseñas funciona para usuarios federados y usuarios con sincronización de hash de contraseña.
  >
  >
* **P: ¿La escritura diferida de contraseñas aplica las directivas de contraseñas de mi dominio?**

  > **R:** Sí, la escritura diferida de contraseñas aplica la vigencia, el historial, la complejidad y los filtros de contraseñas, además de cualquier otra restricción que pueda aplicar sobre las contraseñas en su dominio local.
  >
  >
* **P: ¿Es segura la escritura diferida de contraseñas?  ¿Cómo puedo estar seguro de no ser víctima del ataque de un hacker?**

  > **R:** Sí, la escritura diferida de contraseñas es segura. Para más información sobre los cuatro niveles de seguridad que implementa el servicio de escritura diferida de contraseñas, consulte la sección [Modelo de seguridad de la escritura diferida de contraseñas](active-directory-passwords-writeback.md#password-writeback-security-model) en Funcionamiento de la escritura diferida de contraseñas.
  >
  >

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Informes**](active-directory-passwords-reporting.md): detectan si los usuarios acceden a la funcionalidad de SSPR, cuándo lo hacen y dónde.
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas
* [**Escritura diferida de contraseñas**](active-directory-passwords-writeback.md): cómo funciona la escritura diferida de contraseñas con el directorio local
* [**Artículo técnico de profundización**](active-directory-passwords-how-it-works.md): más información para comprender el funcionamiento de la administración de contraseñas
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR
