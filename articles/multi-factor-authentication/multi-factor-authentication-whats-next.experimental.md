---
title: "Configuración de Azure MFA | Microsoft Docs"
description: "Esta es la página de Azure Multi-Factor Authentication que describe los pasos siguientes a realizar con MFA.  Esto incluye informes, alertas de fraude, omisión por única vez, mensajes de voz personalizados, almacenamiento en caché, direcciones IP de confianza y contraseñas de aplicación."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: kgremban
ms.openlocfilehash: 9d77b9329116afcf2fdde48d672c95020738138c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configuración de Azure Multi-Factor Authentication
Este artículo le ayudará a administrar Azure Multi-Factor Authentication ahora que ya está preparado y con todo funcionando.  Abarca varios temas que le permitirán sacar el máximo partido de Azure Multi-Factor Authentication.  No todas estas características están disponibles en todas las versiones de Azure Multi-Factor Authentication.

| Característica | Descripción | 
|:--- |:--- |
| [Alerta de fraude](#fraud-alert) |Se puede instalar y configurar la alerta de fraude para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos. |
| [Omisión por única vez](#one-time-bypass) |Una omisión por única vez permite a un usuario autenticarse una sola vez omitiendo la autenticación multifactor. |
| [Mensajes de voz personalizados](#custom-voice-messages) |Los mensajes de voz personalizados permiten utilizar sus propias grabaciones o saludos con la autenticación multifactor. |
| [Almacenamiento en caché](#caching-in-azure-multi-factor-authentication) |El almacenamiento en caché permite establecer un período de tiempo específico para que los intentos de autenticación siguientes se realicen correctamente de forma automática. |
| [Direcciones IP de confianza](#trusted-ips) |Los administradores de un inquilino administrado o federado pueden usar IP de confianza para omitir la verificación en dos pasos de los usuarios que inician sesión desde la intranet local de la empresa. |
| [Contraseñas de aplicación](#app-passwords) |Las contraseñas de aplicación permiten omitir la autenticación multifactor en aplicaciones no compatibles con esta funcionalidad sin que por ello dejen de funcionar. |
| [Recordar la autenticación multifactor para exploradores y dispositivos recordados](#remember-multi-factor-authentication-for-devices-that-users-trust) |Permite recordar dispositivos durante un número determinado de días después de que un usuario inicie sesión correctamente mediante MFA. |
| [Métodos de verificación seleccionables](#selectable-verification-methods) |Le permite elegir los métodos de autenticación que están disponibles para que los usuarios los utilicen. |

## <a name="access-the-azure-mfa-management-portal"></a>Acceso al Portal de administración de Azure MFA

Las características que se tratan en este artículo están configuradas en el Portal de administración de Azure Multi-Factor Authentication. Hay dos formas de acceder al Portal de administración de MFA a través del Portal de Azure clásico. La primera es mediante la administración de un proveedor de Multi-Factor Auth. La segunda es mediante la configuración del servicio MFA. 

### <a name="use-an-authentication-provider"></a>Uso de un proveedor de autenticación

Si se usa un proveedor de Multi-Factor Auth para MFA basada en el consumo, utilice este método para acceder al portal de administración.

Para acceder al Portal de administración de MFA a través de un proveedor de Azure Multi-Factor Authentication, inicie sesión en el Portal de Azure clásico como administrador y seleccione la opción Active Directory. Haga clic en la pestaña **Proveedores de autenticación multifactor**, seleccione el directorio y haga clic en el botón **Administrar** en la parte inferior.

### <a name="use-the-mfa-service-settings-page"></a>Uso de la página de configuración del servicio MFA 

Si tiene una de las siguientes licencias, puede usar la página de configuración del servicio MFA:
- Azure MFA
- Azure AD Premium 
- Enterprise Mobility + Security

Para acceder al Portal de administración de MFA a través de la página de configuración del servicio MFA, inicie sesión en el Portal de Azure clásico como administrador y seleccione la opción Active Directory. Haga clic en el directorio y, a continuación, haga clic en la pestaña **Configurar** . En la sección de autenticación multifactor, seleccione **Administrar configuración del servicio**. En la parte inferior de la página Configuración del servicio MFA, haga clic en el vínculo **Ir al portal** .


## <a name="fraud-alert"></a>Alerta de fraude
Se puede instalar y configurar la alerta de fraude para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos.  Los usuarios pueden informar del fraude con la aplicación móvil o a través de su teléfono.

### <a name="set-up-fraud-alert"></a>Configuración de la alerta de fraude
1. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
2. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en **Configuración** en la sección Configurar.
3. En la sección Alerta de fraude de la página Configuración, active la casilla **Permitir a los usuarios enviar alertas de fraude**.
4. Seleccione **Guardar** para aplicar los cambios. 

### <a name="configuration-options"></a>Opciones de configuración

- **Bloquear usuario al notificarse fraudes**: si se informa de que un usuario ha cometido un fraude, se bloquea su cuenta.
- **Código para notificar fraudes durante el saludo inicial**: los usuarios normalmente presionan # para confirmar la verificación en dos pasos. Si desean notificar un fraude, especifican un código antes de presionar #. De manera predeterminada, dicho código es **0**, pero se puede personalizar.

> [!NOTE]
> El saludo predeterminado de Microsoft indica a los usuarios que presionen 0# para enviar una alerta de fraude. Si quiere usar un código distinto de 0, deberá grabar y cargar sus propio saludo personalizado con las instrucciones apropiadas.

![Opciones de alerta de fraude (captura de pantalla)](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Cómo notifican un fraude los usuarios 
Una alerta de fraude se puede notificar de dos maneras.  Ya sea a través de la aplicación móvil o del teléfono.  

#### <a name="report-fraud-with-the-mobile-app"></a>Notificación de fraude con la aplicación móvil
1. Cuando se envíe una comprobación a su teléfono, selecciónela para abrir la aplicación Microsoft Authenticator.
2. Seleccione **Denegar** en la notificación. 
3. Haga clic en **Notificar fraude**.
4. Cierre la aplicación.

#### <a name="report-fraud-with-a-phone"></a>Notificación de fraude con un teléfono
1. Cuando una llamada de comprobación llegue a su teléfono, contéstela.  
2. Para notificar un fraude, especifique su código (el valor predeterminado es 0) y, después, en el signo #. Se le notificará que se ha enviado una alerta de fraude.
3. Finalice la llamada.

### <a name="view-fraud-reports"></a>Visualización de notificaciones de fraude
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione **Proveedores de autenticación multifactor** para mostrar una lista de los proveedores de autenticación multifactor.
4. Seleccione el proveedor de Multi-Factor Authentication y haga clic en **Administrar** en la parte inferior de la página. Se abrirá el Portal de administración de Azure Multi-Factor Authentication.
5. En el Portal de administración de Azure Multi-Factor Authentication, en Ver un informe, haga clic en **Alerta de fraude**.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar los nombres de usuario, los números de teléfono y el estado del usuario.
7. Haga clic en **Ejecutar** para mostrar un informe de alertas de fraude. Haga clic en **Exportar a CSV** si desea exportar el informe.

## <a name="one-time-bypass"></a>Omisión por única vez
Una omisión por única vez permite a un usuario autenticarse una sola vez y, por consiguiente, no realizar la verificación en dos pasos. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos. En aquellas situaciones en las que la aplicación móvil o el teléfono no reciben una notificación o una llamada de teléfono, puede habilitar una omisión por única vez para que el usuario pueda acceder al recurso deseado.

### <a name="create-a-one-time-bypass"></a>Creación de una omisión por única vez
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
3. Si ve el nombre de su proveedor de Azure MFA en la parte izquierda con un signo **+** junto a él, haga clic en ese signo **+**. Se muestran los distintos grupos de replicación del servidor MFA y el grupo predeterminado de Azure. Seleccione el grupo adecuado.
4. En Administración de usuarios, seleccione **Omisión por única vez**.
5. En la página Omisión por única vez, haga clic en **Nueva omisión por única vez**.

  ![Nube](./media/multi-factor-authentication-whats-next/create1.png)

6. Escriba el nombre de usuario, la duración de la omisión y el motivo de esta. Haga clic en **Omitir**.
7. El límite de tiempo entra en vigor inmediatamente, por lo que es preciso que el usuario inicie sesión antes de que expire la omisión por única vez. 

### <a name="view-the-one-time-bypass-report"></a>Visualización del informe de omisión por única vez
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione **Proveedores de autenticación multifactor** para mostrar una lista de los proveedores de autenticación multifactor.
4. Seleccione el proveedor de Multi-Factor Authentication y haga clic en **Administrar** en la parte inferior de la página. Se abrirá el Portal de administración de Azure Multi-Factor Authentication.
5. En el Portal de administración de Azure Multi-Factor Authentication, a la izquierda, en Ver un informe haga clic en **Omisión por única vez**.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar los nombres de usuario, los números de teléfono y el estado del usuario.
7. Haga clic en **Ejecutar** para mostrar un informe de omisiones. Haga clic en **Exportar a CSV** si desea exportar el informe.

## <a name="custom-voice-messages"></a>Mensajes de voz personalizados
Los mensajes de voz personalizados le permiten utilizar sus propias grabaciones o saludos para la verificación en dos pasos. Puede usar mensajes de voz en combinación con las grabaciones de Microsoft o en sustitución de estas.

Antes de comenzar tenga en cuenta lo siguiente:

* Los formatos de archivo compatibles son .wav y. mp3.
* El límite de tamaño de archivo es 5 MB.
* Los mensajes de autenticación deben durar menos de 20 segundos. Los mensajes que duran más de 20 segundos no dan a los usuarios suficiente tiempo para responder antes de que se agote el tiempo de comprobación.

### <a name="set-up-a-custom-message"></a>Configuración de un mensaje personalizado

La creación de mensajes personalizados consta de dos partes. En primer lugar, se carga el mensaje y, después, se activa para los usuarios.

Para cargar un mensaje personalizado:

1. Cree un mensaje de voz personalizado mediante uno de los formatos de archivo compatibles.
2. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
3. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
4. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en **Mensajes de voz** en la sección Configurar.
5. En la página Configurar: mensajes de voz, haga clic en **Nuevo mensaje de voz**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom1.png)
6. En la página Configurar: Nuevos mensajes de voz, haga clic en **Administrar archivos de sonido**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom2.png)
7. En la página Configurar: Archivos de sonido, haga clic en **Cargar archivo de sonido**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom3.png)
8. En la página Configurar: Cargar archivo de sonido, haga clic en **Examinar** y navegue hasta el mensaje de voz; luego haga clic en **Abrir**.
9. Agregue una descripción y haga clic en **Cargar**.
10. Después de que se cargue el mensaje de voz, verá un mensaje que confirma que se ha cargado correctamente el archivo.

Para activar el mensaje para los usuarios:

1. A la izquierda, haga clic en **Mensajes de voz**.
2. En la sección Mensajes de voz, haga clic en **Nuevo mensaje de voz**.
3. En la lista desplegable Idioma, seleccione un idioma.
4. Si este mensaje es para una aplicación específica, especifíquela en el cuadro aplicación.
5. En la lista desplegable Tipo de mensaje, seleccione el tipo de mensaje al que va a reemplazar el nuevo mensaje personalizado.
6. En la lista desplegable Archivo de sonido, seleccione el archivo de sonido que cargó en la primera parte.
7. Haga clic en **Crear**. Un mensaje confirmará que ha creado correctamente un mensaje de voz.
    ![Nube](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Almacenamiento en caché en Azure Multi-Factor Authentication
El almacenamiento en caché permite establecer un período específico para que los intentos de autenticación siguientes que se realicen en dicho período tengan de forma automática un resultado satisfactorio. El almacenamiento en caché se utiliza principalmente cuando los sistemas locales, como VPN, envían muchas solicitudes de comprobación mientras la primera solicitud aún está en curso. Esto permite que las solicitudes subsiguientes se realicen correctamente de forma automática después de que el usuario lleva a cabo correctamente la primera comprobación en curso. 

El almacenamiento en caché no está concebido para usarse en los inicios de sesión en Azure AD.

### <a name="set-up-caching"></a>Configuración del almacenamiento en caché 
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
3. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en **Almacenamiento en caché** en la sección Configurar.
4. En la página Configure caching (Configurar almacenamiento en caché), haga clic en **Nueva caché**.
5. Seleccione el tipo de caché y los segundos en caché. Haga clic en **Crear**.

<center>![Nube](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IP de confianza
IP de confianza es una característica de Azure MFA que los administradores de un inquilino administrado o federado pueden usar para omitir la verificación en dos pasos. Esta verificación es la que utilizan los usuarios que están iniciando sesión desde la intranet local. Esta característica está disponible con la versión completa de Azure Multi-Factor Authentication, no la versión gratuita para administradores. Para más información sobre cómo obtener la versión completa de Azure Multi-Factor Authentication, consulte [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Tipo de un inquilino de Azure AD. | Opciones de IP de confianza disponibles |
|:--- |:--- |
| Administrado |<li>Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la verificación en dos pasos para los usuarios que inician sesión desde la intranet de la empresa.</li> |
| Federado |<li>Todos los usuarios federados: todos los usuarios federados que inician sesión desde dentro de la organización omitirán la verificación en dos pasos mediante una notificación que emite AD FS.</li><br><li>Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la verificación en dos pasos para los usuarios que inician sesión desde la intranet de la empresa. |

Esta omisión solo funciona desde dentro de la intranet de una empresa. 

**Experiencia del usuario final en la red corporativa:**

Si IP de confianza se deshabilita, se requiere la verificación en dos pasos para los flujos del explorador y se requieren las contraseñas de aplicación para las aplicaciones de cliente antiguas. 

Cuando se habilita la IP de confianza, la verificación en dos pasos *no* es necesaria para los flujos del explorador. Las contraseñas de aplicación *no* son obligatorias para las aplicaciones de cliente enriquecidas anteriores, siempre que el usuario no haya creado ya una contraseña de aplicación. Una vez que una contraseña de aplicación está en uso, es necesaria. 

**Experiencia del usuario final fuera de la red corporativa:**

Independientemente de que IP de confianza esté habilitado, se requiere la verificación en dos pasos para los flujos del explorador y se requieren las contraseñas de aplicación para las aplicaciones de cliente antiguas. 

### <a name="to-enable-trusted-ips"></a>Para habilitar IP de confianza
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Navegue a la página MFA Service Settings (Configuración del servicio MFA) como ha indicado en las instrucciones del principio de este artículo.
3. En la página Configuración del servicio, en IP de confianza, hay dos opciones:
   
   * **Para solicitudes de usuarios federados cuyo origen esté en mi intranet**: active la casilla. Todos los usuarios federados que inicien sesión desde la red corporativa omitirán la verificación en dos pasos mediante una notificación emitida por AD FS.
   * **Para solicitudes de un intervalo de IP públicas específico**: escriba las direcciones IP en el cuadros de texto proporcionado y use para ello la notación CIDR. Por ejemplo: xxx.xxx.xxx.0/24 para direcciones IP en el intervalo de xxx.xxx.xxx.1 – xxx.xxx.xxx.254, o xxx.xxx.xxx.xxx/32 para una única dirección IP. Puede especificar hasta 50 intervalos de direcciones IP. Los usuarios que inician sesión desde estas direcciones IP omiten la comprobación en dos pasos.
4. Haga clic en **Guardar**.
5. Una vez que se hayan aplicado las actualizaciones, haga clic en **Cerrar**.

![IP de confianza](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Contraseñas de aplicación
Algunas aplicaciones, como Office 2010, o las versiones anteriores, y Apple Mail, no admiten la comprobación en dos pasos, ya que no estén configuradas para aceptar una segunda comprobación. Para usarlas, es preciso utilizar "contraseñas de aplicación", en lugar de la contraseña tradicional. Las contraseñas de aplicación permiten a la aplicación omitir la comprobación en dos pasos y seguir trabajando.

> [!NOTE]
> Autenticación moderna para los clientes de Office 2013
> 
> Los clientes de Office 2013 (incluido Outlook), y los más recientes, admiten protocolos de autenticación modernos y se pueden habilitar para que funcionen con la comprobación en dos pasos. Una vez habilitada esta opción, estos clientes no necesitan contraseñas de aplicación.  Para más información, consulte [Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Aspectos importantes acerca de las contraseñas de aplicación
Esta es una lista de cosas importantes que debe saber acerca de las contraseñas de aplicación.

* Las contraseñas de aplicación se deben escribir en el cuadro de entrada una vez por cada aplicación. No es preciso que los usuarios realicen un seguimiento de las mismas y las escriban en cada ocasión.
* La contraseña real se genera automáticamente y no la proporciona el usuario. Las contraseñas generadas automáticamente son más difíciles de adivinar y, por tanto, más seguras.
* Hay un límite de 40 contraseñas por usuario. 
* Las aplicaciones que almacenan en caché las contraseñas y las usan en escenarios locales, pueden comenzar a dar error ya que la contraseña de aplicación no se reconocerá fuera del identificador de organización. Un ejemplo es el de los mensajes de correo electrónico de Exchange que son locales pero que se archivan en la nube. No funciona la misma contraseña.
* Cuando se inicia la autenticación multifactor, puede usar la contraseña con algunos clientes que no usan explorador. No se pueden utilizar contraseñas de aplicación para tareas administrativas. Asegúrese de crear una cuenta de servicio con una contraseña segura para ejecutar scripts de PowerShell y no la habilite para la comprobación en dos pasos.

> [!WARNING]
> Las contraseñas de aplicación no funcionan en entornos híbridos donde los clientes se comunican tanto en el entorno local como en los puntos de conexión de detección automática en la nube. Las contraseñas de dominio son necesarias para autenticarse en el entorno local y las contraseñas de aplicación para autenticarse con la nube.

### <a name="naming-guidance-for-app-passwords"></a>Guía de nomenclatura para las contraseñas de aplicación
Los nombres de las contraseñas de aplicación deben reflejar el dispositivo en el que se usan. Por ejemplo, si tiene un portátil con aplicaciones sin explorador, cree una contraseña de aplicación denominada Portátil y utilícela. Después, cree otra contraseña de aplicación denominada Escritorio para las mismas aplicaciones en el equipo de escritorio. 

Microsoft recomienda crear una contraseña de aplicación por dispositivo, no por aplicación.

### <a name="federated-sso-app-passwords"></a>Contraseñas de aplicación federada (SSO)
Azure AD admite la federación (inicio de sesión único) con Active Directory Domain Services (AD DS) de Windows Server. Si su organización está federada con Azure AD y va a usar Azure Multi-Factor Authentication, no deje de leer la información acerca de las contraseñas de aplicación. Esta sección se aplica solo a los clientes federados (SSO).

* Las contraseñas de aplicación las comprueba Azure AD y, por tanto, se omite la federación. La federación solo se usa activamente al configurar la contraseñas de aplicación.
* En el caso de los usuarios federados (SSO), nunca se usa el proveedor de identidades (IdP), a diferencia del flujo pasivo. Las contraseñas se almacenan en el identificador de la organización. Si el usuario abandona la empresa, esta información tiene que fluir al identificador de la organización usando DirSync en tiempo real. La deshabilitación o eliminación de la cuenta puede tardar hasta tres horas en sincronizarse, retrasando la deshabilitación o eliminación de contraseña de aplicación en Azure AD.
* La configuración del control de acceso de cliente local no admite la contraseña de aplicación
* La contraseña de aplicación no tiene la ninguna funcionalidad de auditoría o registro de autenticación local.
* Puede que algunos diseños arquitectónicos avanzados requieran una combinación de nombre de usuario y contraseñas de la organización, y de contraseñas de aplicación cuando se usa la verificación en dos pasos con clientes. Aunque depende de dónde se autentiquen. Para los clientes que se autentican en una infraestructura local, utilizaría un nombre de usuario y una contraseña de la organización. Para los clientes que se autentican en Azure AD, usaría la contraseña de aplicación.

  Por ejemplo, suponga que tiene una arquitectura que consta de estas instancias:

  * Federa su instancia local de Active Directory con Azure AD
  * Usa Exchange Online
  * Usa Lync que es específicamente local
  * Usa Azure Multi-Factor Authentication

  ![Página de proofup](./media/multi-factor-authentication-whats-next/federated.png)

  En estas instancias, debe realizar los siguientes pasos:

  * Cuando inicie sesión en Lync, utilice el nombre de usuario y la contraseña de su organización.
  * Al intentar obtener acceso a la libreta de direcciones a través de un cliente de Outlook que se conecta a Exchange online, utilice una contraseña de aplicación.

### <a name="allow-app-password-creation"></a>Permiso para la creación de contraseñas de aplicación
De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación, pero usted puede habilitar la característica por sí mismo. Para que los usuarios tengan la posibilidad de crear contraseñas de aplicación, use el procedimiento siguiente:

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Navegue a la página MFA Service Settings (Configuración del servicio MFA) como ha indicado en las instrucciones del principio de este artículo.
3. Seleccione el botón de radio que hay junto a **Allow users to create app passwords to sign into non-browser apps** (Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador).

![Crear contraseñas de aplicación](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Creación de contraseñas de aplicación
Los usuarios pueden crear contraseñas de aplicación durante el registro inicial. Se les permite hacerlo al final del proceso.

Los usuarios también pueden crear contraseñas de aplicación después del registro. Ellos pueden crear contraseñas de aplicación si cambian su configuración en Azure Portal o en el Portal de Office 365. Para más información e instrucciones detalladas para los usuarios, consulte [¿Qué son las contraseñas de aplicación en Azure Multi-Factor Authentication?](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Recuerdo de Multi-Factor Authentication para los dispositivos en los que los usuarios confían
Recordar Multi-Factor Authentication para los dispositivos y exploradores en los que los usuarios confían es una característica gratuita para todos los usuarios de MFA. La autenticación multifactor permite a los usuarios omitir MFA durante un número determinado de días después de iniciar sesión. Esta característica permite mejorar la facilidad de uso, ya que minimiza el número de veces que un usuario puede realizar la verificación en dos pasos en el mismo dispositivo.

Sin embargo, si una cuenta o un dispositivo corren peligro, el hecho de recordar MFA para los dispositivos de confianza puede afectar a la seguridad. Si una cuenta corporativa se pone en peligro o un dispositivo de confianza es objeto de pérdida o robo, debe [restaurar Multi-Factor Authentication en todos los dispositivos](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Esta acción revoca el estado de confianza de todos los dispositivos y el usuario debe volver a realizar la comprobación en dos pasos. También puede indicar a los usuarios que restauren MFA en sus propios dispositivos con las instrucciones que se indican en [Administración de la configuración de la comprobación en dos pasos](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).

### <a name="how-it-works"></a>Cómo funciona

Recordar que Multi-Factor Authentication funciona mediante el establecimiento de una cookie persistente en el explorador cuando un usuario marca el cuadro "Don't ask again for **X** days" (No preguntar de nuevo durante X días) al inicio de sesión. No se volverá a pedir al usuario MFA desde ese explorador hasta que la cookie expire. Si el usuario abre un explorador diferente en el mismo dispositivo o borra sus cookies, se le pedirá de nuevo la comprobación. 

La casilla "Don't ask again for **X** days" (No preguntar de nuevo durante X días) no se muestra en aplicaciones sin explorador, ya admitan o no la autenticación moderna. Estas aplicaciones usan tokens de actualización que proporcionan nuevos tokens de acceso cada hora. Cuando un token de actualización se valida, Azure AD comprueba que la última vez que se realizara la verificación en dos pasos estuviera dentro del número de días configurado. 

En conclusión, recuerde que MFA en dispositivos de confianza reduce el número de autenticaciones en las aplicaciones web (que normalmente la solicitan todas las veces). Pero también aumenta el número de autenticaciones para los clientes de autenticación moderna (que normalmente la solicitan cada 90 días).

> [!NOTE]
>Esta característica no es compatible con la característica de AD FS "Mantener la sesión iniciada" cuando los usuarios realizan la comprobación en dos pasos para AD FS mediante el Servidor Azure MFA o una solución MFA de terceros. Si los usuarios seleccionan "Mantener la sesión iniciada" en AD FS y también marcan su dispositivo como de confianza para MFA, no podrán realizar la comprobación después de que expire el número de días de "Recordar MFA". Azure AD solicita una nueva comprobación en dos pasos, pero AD FS devuelve un token con la fecha y la notificación de MFA originales en lugar de volver a realizar la comprobación en dos pasos. Este proceso crea un bucle de comprobación entre Azure AD y AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Habilitación del recuerdo de la autenticación multifactor
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Navegue a la página MFA Service Settings (Configuración del servicio MFA) como ha indicado en las instrucciones del principio de este artículo.
3. En la página Configuración del servicio, en la sección para administrar la configuración de dispositivos de usuario, active la casilla **Permitir que los usuarios recuerden la autenticación multifactor en dispositivos de confianza**.
   ![Recordar dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
4. Establezca el número de días que desea permitir que los dispositivos de confianza omitan la verificación en dos pasos. El valor predeterminado es 14 días.
5. Haga clic en **Guardar**.
6. Haga clic en **Cerrar**.

### <a name="mark-a-device-as-trusted"></a>Marca de un dispositivo como de confianza

Una vez que se habilitar esta característica, los usuarios pueden marcar un dispositivo como de confianza cuando inician sesión. Para ello, solo deben activar **Don't ask again** (No volver a preguntar).

![No volver a preguntar (captura de pantalla)](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificación seleccionables
Puede elegir los métodos de verificación que pueden emplear los usuarios. En la tabla siguiente se proporciona una breve descripción de cada método.

Cuando los usuarios inscriben sus cuentas para MFA, deciden su método de comprobación preferido de las opciones habilitadas. Las instrucciones para el proceso de inscripción se tratan en [Configuración de mi cuenta para la comprobación en dos pasos](multi-factor-authentication-end-user-first-time.md).

| Método | Descripción |
|:--- |:--- |
| Llamada al teléfono |Hace una llamada de voz automática. El usuario responde a la llamada y pulsa la # del teclado del teléfono para autenticarse. Este número de teléfono no se sincroniza con Active Directory local. |
| Mensaje de texto al teléfono |Envía un mensaje de texto que contiene un código de verificación. Se le pide al usuario que o bien conteste al mensaje de texto con el código de comprobación o que escriba el código de verificación en la interfaz de inicio de sesión. |
| Notificación a través de aplicación móvil |Envía una notificación push a su teléfono o dispositivo registrado. El usuario ve la notificación y selecciona **Comprobar** para completar la comprobación. <br>La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de comprobación desde aplicación móvil |La aplicación Microsoft Authenticator genera un nuevo código de verificación de OATH cada 30 segundos. El usuario escribe dicho código en la interfaz de inicio de sesión.<br>La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Habilitación/deshabilitación de los métodos de autenticación
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Navegue a la página MFA Service Settings (Configuración del servicio MFA) como ha indicado en las instrucciones del principio de este artículo.
3. En la página Configuración del servicio, en Opciones de comprobación, active o desactive las opciones que desee usar.
   ![Opciones de verificación](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Haga clic en **Guardar**.
5. Haga clic en **Cerrar**.
