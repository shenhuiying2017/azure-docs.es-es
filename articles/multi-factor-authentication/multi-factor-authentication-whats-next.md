---
title: Pasos siguientes para Azure Multi-Factor Authentication
description: "Esta es la página de Azure Multi-Factor Authentication que describe los pasos siguientes a realizar con MFA.  Esto incluye informes, alertas de fraude, omisión por única vez, mensajes de voz personalizados, almacenamiento en caché, direcciones IP de confianza y contraseñas de aplicación."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d904de6370c1c6bd5116471465ec63fd8999a7ee


---
# <a name="configuring-azure-multi-factor-authentication"></a>Configuración de Azure Multi-Factor Authentication
Este artículo le ayudará a administrar Azure Multi-Factor Authentication ahora que ya está preparado y con todo funcionando.  Abarca una variedad de temas que le permitirán aprovechar al máximo Azure Multi-Factor Authentication.  No todas estas características están disponibles en todas las versiones de Azure Multi-Factor Authentication.

La configuración de algunas de las siguientes características se encuentra en el Portal de administración de Azure Multi-Factor Authentication. Existen dos maneras diferentes de acceder a este portal, ambas mediante Azure Portal. La primera es mediante la administración de un Proveedor de Multi-Factor Authentication si se usa MFA basada en el consumo. La segunda es mediante la configuración del servicio MFA. La segunda opción requiere un Proveedor de Multi-Factor Authentication o una licencia de Azure MFA, Azure AD Premium o Enterprise Mobility Suite.

Para acceder al Portal de administración de MFA a través de un Proveedor de Multi-Factor Authentication, inicie sesión en el Portal de Azure como administrador y seleccione la opción Active Directory. Haga clic en la pestaña **Proveedores de autenticación multifactor**, seleccione el directorio y haga clic en el botón **Administrar** en la parte inferior.

Para acceder al Portal de administración de MFA a través de la página Configuración del servicio de MFA, inicie sesión en el Portal de Azure como administrador y seleccione la opción Active Directory. Haga clic en el directorio y, a continuación, haga clic en la pestaña **Configurar** . En la sección de autenticación multifactor, seleccione **Administrar configuración del servicio**. En la parte inferior de la página Configuración del servicio MFA, haga clic en el vínculo **Ir al portal** .

| Característica | Description | ¿Qué se va a tratar? |
|:--- |:--- |:--- |
| [Alerta de fraude](#fraud-alert) |Se puede instalar y configurar la alerta de fraude para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos. |Cómo instalar, configurar y notificar fraudes |
| [Omisión por única vez](#one-time-bypass) |Una omisión por única vez permite a un usuario autenticarse una sola vez omitiendo la autenticación multifactor. |Cómo instalar y configurar una omisión puntual |
| [Mensajes de voz personalizados](#custom-voice-messages) |Los mensajes de voz personalizados permiten utilizar sus propias grabaciones o saludos con la autenticación multifactor. |Cómo instalar y configurar mensajes y saludos personalizados |
| [Almacenamiento en caché](#caching-in-azure-multi-factor-authentication) |El almacenamiento en caché permite establecer un período de tiempo específico para que los intentos de autenticación siguientes se realicen correctamente de forma automática. |Cómo instalar y configurar el almacenamiento en caché de la autenticación |
| [Direcciones IP de confianza](#trusted-ips) |IP de confianza es una característica de la autenticación multifactor que permite a los administradores de un inquilino administrado o federado la capacidad de omitir la autenticación multifactor para los usuarios que inician sesión desde la intranet local de la empresa. |Configurar e instalar las direcciones IP que están exentas de la autenticación multifactor |
| [Contraseñas de aplicación](#app-passwords) |Las contraseñas de aplicación permiten omitir la autenticación multifactor en aplicaciones no compatibles con esta funcionalidad sin que por ello dejen de funcionar. |Información acerca de las contraseñas de aplicación. |
| [Recordar la autenticación multifactor para exploradores y dispositivos recordados](#remember-multi-factor-authentication-for-devices-users-trust) |Permite recordar dispositivos durante un número determinado de días después de que un usuario inicie sesión correctamente mediante MFA. |Información sobre cómo habilitar esta característica y configurar el número de días. |
| [Métodos de verificación seleccionables](#selectable-verification-methods) |Le permite elegir los métodos de autenticación que están disponibles para que los usuarios los utilicen. |Información acerca de cómo habilitar o deshabilitar métodos de autenticación específicos, como mensajes de texto o llamada. |

## <a name="fraud-alert"></a>Alerta de fraude
Se puede instalar y configurar la alerta de fraude para que los usuarios puedan informar sobre intentos fraudulentos de acceso a sus recursos.  Los usuarios pueden informar del fraude con la aplicación móvil o a través de su teléfono.

### <a name="to-set-up-and-configure-fraud-alert"></a>Para instalar y configurar la alerta de fraude
1. Inicie sesión en http://azure.microsoft.com
2. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
3. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en Configuración en la sección Configurar.
4. En la sección Alerta de fraude de la página Configuración, active la casilla Permitir a los usuarios enviar alertas de fraude.
5. Si desea que los usuarios se bloquean cuando se informe de un fraude, coloque una comprobación en la casilla correspondiente a Bloquear usuario al notificarse fraudes.
6. En el cuadro de texto **Código para notificar fraudes durante el saludo inicial** , escriba un código de números que se pueda usar durante la comprobación de llamadas. Si un usuario introduce este código más # en lugar de solo el signo #, se notificará una alerta de fraude.
7. Haga clic en Guardar en la parte inferior.

> [!NOTE]
> El saludo predeterminado de Microsoft indica a los usuarios que presionen 0# para enviar una alerta de fraude. Si quiere usar un código distinto de 0, deberá grabar y cargar sus propio saludo personalizado con las instrucciones apropiadas.
> 
> 

![Nube](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Para informar sobre un fraude
Una alerta de fraude se puede notificar de dos maneras.  Ya sea a través de la aplicación móvil o del teléfono.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Para notificar de una alerta de fraude con la aplicación móvil
1. Cuando se envíe una comprobación a su teléfono, selecciónela para iniciar la aplicación Microsoft Authenticator.
2. Para informar sobre un fraude, haga clic en Cancelar y notificar fraude. Aparecerá un cuadro que dice que se enviará una notificación al personal de soporte técnico de TI.
3. Haga clic en Notificar fraude.
4. En la aplicación, haga clic en Cerrar.

![Nube](./media/multi-factor-authentication-whats-next/report1.png)

![Nube](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Para notificar de una alerta de fraude con el teléfono
1. Cuando una llamada de comprobación llegue a su teléfono, contéstela.  
2. Para informar de fraude, escriba el código que se ha configurado para informar de fraude a través del teléfono y, a continuación, el signo #. Se le notificará que se ha enviado una alerta de fraude.
3. Finalice la llamada.

### <a name="to-view-the-fraud-report"></a>Para ver el informe de fraude
1. Inicie sesión en [http://azure.microsoft.com](https://azure.microsoft.com/)
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione los proveedores de Multi-Factor Authentication. Aparecerá una lista de los proveedores de Multi-Factor Authentication.
4. Si tiene más de un proveedor de Multi-Factor Authentication, seleccione aquel en el que desea ver el informe de la alerta de fraude y haga clic en Administrar en la parte inferior de la página. Si solo tiene uno, haga clic en Administrar. Se abrirá el Portal de administración de Azure Multi-Factor Authentication.
5. En el Portal de administración de Azure Multi-Factor Authentication, a la izquierda, en Ver un informe haga clic en Alerta de fraude.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar cualquier nombre de usuario específico, números de teléfono y el estado de los usuarios.
7. Haga clic en Ejecutar. Aparecerá un informe similar al siguiente. También puede hacer clic en Exportar a CSV si desea exportar el informe.

## <a name="one-time-bypass"></a>Omisión por única vez
Una omisión por única vez permite a un usuario autenticarse una sola vez omitiendo la autenticación multifactor. La omisión es temporal y expira una vez que ha pasado el número especificado de segundos.  Por ello en situaciones donde la aplicación móvil o el teléfono no están recibiendo una notificación o una llamada de teléfono, puede habilitar una omisión única, para que el usuario puede tener acceso al recurso deseado.

### <a name="to-create-a-one-time-bypass"></a>Para crear una omisión por única vez
1. Inicie sesión en http://azure.microsoft.com
2. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
3. En el Portal de administración de Azure Multi-Factor Authentication, si ve el nombre del inquilino o del Proveedor de Azure MFA a la izquierda con una signo + al lado, haga clic en él para ver los diferentes grupos de aplicaciones de servidor MFA y el grupo predeterminado de Azure. Haga clic en el grupo adecuado.
4. En Administración de usuarios, haga clic en **Omisión por única vez**.
   ![Nube](./media/multi-factor-authentication-whats-next/create1.png)
5. En la página Omisión por única vez, haga clic en **Nueva omisión por única vez**.
6. Escriba el nombre de usuario, el número de segundos que durará la omisión, el motivo de esta y haga clic en **Omitir**.
   ![Nube](./media/multi-factor-authentication-whats-next/create2.png)
7. Ahora, el usuario tiene que iniciar sesión antes de que expire la omisión por única vez.

### <a name="to-view-the-one-time-bypass-report"></a>Para ver el informe de omisión por única vez
1. Inicie sesión en [http://azure.microsoft.com](https://azure.microsoft.com/)
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione los proveedores de Multi-Factor Authentication. Aparecerá una lista de los proveedores de Multi-Factor Authentication.
4. Si tiene más de un proveedor de Multi-Factor Authentication, seleccione aquel en el que desea ver el informe de la alerta de fraude y haga clic en Administrar en la parte inferior de la página. Si solo tiene uno, haga clic en Administrar. Se abrirá el Portal de administración de Azure Multi-Factor Authentication.
5. En el Portal de administración de Azure Multi-Factor Authentication, a la izquierda, en Ver un informe haga clic en Omisión por única vez.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar cualquier nombre de usuario específico, números de teléfono y el estado de los usuarios.
7. Haga clic en Ejecutar. Aparecerá un informe similar al siguiente. También puede hacer clic en Exportar a CSV si desea exportar el informe.

<center>![Nube](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Mensajes de voz personalizados
Los mensajes de voz personalizados permiten utilizar sus propias grabaciones o saludos con la autenticación multifactor.  Pueden utilizarse además o reemplazar a las grabaciones de Microsoft.

Antes de comenzar tenga en cuenta lo siguiente:

* Los formatos de archivo compatibles en la actualidad son .wav y. mp3.
* El límite de tamaño de archivo es 5 MB.
* Se recomienda que los mensajes de autenticación no tengan más de 20 segundos. Cualquier valor superior a este podría provocar un error de comprobación porque el usuario puede no responder antes de que finalice el mensaje y se agote el tiempo de espera de la comprobación.

### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Para configurar mensajes de voz personalizados en Azure Multi-Factor Authentication
1. Cree un mensaje de voz personalizado mediante uno de los formatos de archivo compatibles.
2. Inicie sesión en http://azure.microsoft.com
3. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
4. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en Mensajes de voz en la sección Configurar.
5. En la sección Mensajes de voz, haga clic en **Nuevo mensaje de voz**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom1.png)
6. En la página Configurar: Nuevos mensajes de voz, haga clic en **Administrar archivos de sonido**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom2.png)
7. En la página Configurar: Archivos de sonido, haga clic en **Cargar archivo de sonido**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom3.png)
8. En la página Configurar: Cargar archivo de sonido, haga clic en **Examinar** y navegue hasta el mensaje de voz; luego haga clic en **Abrir**.
   ![Nube](./media/multi-factor-authentication-whats-next/custom4.png)
9. Agregue una descripción y haga clic en Cargar.
10. Cuando se complete, verá un mensaje que confirma que se ha cargado correctamente el archivo.
11. A la izquierda, haga clic en Mensajes de voz.
12. En la sección Mensajes de voz, haga clic en Nuevo mensaje de voz.
13. En la lista desplegable Idioma, seleccione un idioma.
14. Si este mensaje es para una aplicación específica, especifíquela en el cuadro aplicación.
15. En Tipo de mensaje, seleccione el tipo de mensaje que se va a reemplazar por el nuevo mensaje personalizado.
16. En la lista desplegable Archivo de sonido, seleccione el archivo.
17. Haga clic en **Crear**. Un mensaje confirmará que ha creado correctamente un mensaje de voz.
    ![Nube](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Almacenamiento en caché en Azure Multi-Factor Authentication
El almacenamiento en caché permite establecer un período de tiempo específico para que los intentos de autenticación siguientes se realicen correctamente de forma automática. Se utiliza principalmente cuando sistemas locales, como VPN, envían varias solicitudes de comprobación mientras la primera solicitud aún está en curso. Esto permite que las solicitudes subsiguientes se realicen correctamente de forma automática después de que el usuario lleva a cabo correctamente la comprobación en curso. Tenga en cuenta que el almacenamiento en caché no está concebido para usarse en los inicios de sesión en Azure AD.

### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Para configurar el almacenamiento en caché en Azure Multi-Factor Authentication
1. Inicie sesión en http://azure.microsoft.com
2. Desplácese hasta el Portal de administración de MFA según las instrucciones que aparecen en la parte superior de esta página.
3. En el Portal de administración de Azure Multi-Factor Authentication, haga clic en Almacenamiento en caché en la sección Configurar.
4. En la página Configurar almacenamiento en caché, haga clic en Nueva caché
5. Seleccione el tipo de caché y los segundos en caché. Haga clic en Crear.

<center>![Nube](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Direcciones IP de confianza
IP de confianza es una característica de la autenticación multifactor que permite a los administradores de un inquilino administrado o federado la capacidad de omitir la autenticación multifactor para los usuarios que inician sesión desde la intranet local de la empresa. Las características están disponibles para los inquilinos de Azure AD que tienen licencias de Azure AD Premium, Enterprise Mobility Suite o Azure Multi-Factor Authentication.

| Tipo de un inquilino de Azure AD. | Opciones de IP de confianza disponibles |
|:--- |:--- |
| Administrada |Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la autenticación multifactor para los usuarios que inician sesión desde la intranet de la empresa. |
| Federada |<li>Todos los usuarios federados: todos los usuarios federados que vayan a iniciar sesión desde dentro de la organización omitirán la autenticación multifactor mediante una notificación omitida por AD FS.</li><li>Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la autenticación multifactor para los usuarios que inician sesión desde la intranet de la empresa. |

Esta omisión solo funciona desde dentro de la intranet de una empresa. Por ejemplo, si solo ha seleccionado todos los usuarios federados y un usuario inicia sesión desde fuera de la intranet corporativa, ese usuario tendrá que autenticarse mediante la autenticación multifactor, incluso si presenta una notificación de AD FS. La siguiente tabla describe cuando son necesarias las contraseñas de autenticación multifactor y de aplicación dentro y fuera de la red corporativa cuando está habilitada la opción de IP de confianza.

| IP de confianza está activado | IP de confianza está desactivado |
|:--- |:--- |:--- |
| Dentro de la red corporativa |Para los flujos de explorador NO es necesaria la autenticación multifactor. |
| Para las aplicaciones cliente enriquecidas, las contraseñas regulares funcionarán si el usuario no ha creado ninguna contraseña de aplicación. Una vez que se cree una contraseña de aplicación, se requerirán contraseñas de aplicación. |Para las aplicaciones cliente enriquecidas, se requerirán contraseñas de aplicación. |
| Fuera de la red corporativa |Para los flujos de explorador es necesaria la autenticación multifactor. |
| Para las aplicaciones cliente enriquecidas, se requerirán contraseñas de aplicación. |Para las aplicaciones cliente enriquecidas, se requerirán contraseñas de aplicación. |

### <a name="to-enable-trusted-ips"></a>Para habilitar IP de confianza
1. Inicie sesión en el Portal de Azure clásico.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio en el que desea configurar IP de confianza.
4. En el directorio que ha seleccionado, haga clic en Configurar.
5. En la sección de la autenticación multifactor, haga clic en Administrar configuración del servicio.
6. En la página Configuración del servicio, en IP de confianza, seleccione:
   
   * Para solicitudes de usuarios federados cuyo origen esté en mi intranet: todos los usuarios federados que inician sesión desde la red corporativa omitirán la autenticación multifactor a través del uso de una notificación emitida por AD FS.
   * Para solicitudes de un intervalo de IP públicas específico: escriba las direcciones IP en los cuadros proporcionados usando la notación CIDR. Por ejemplo: xxx.xxx.xxx.0/24 para direcciones IP en el intervalo de xxx.xxx.xxx.1 – xxx.xxx.xxx.254, o xxx.xxx.xxx.xxx/32 para una única dirección IP. Puede especificar hasta 50 intervalos de direcciones IP.
7. Haga clic en Guardar.
8. Una vez que se han aplicado las actualizaciones, haga clic en Cerrar.

![Direcciones IP de confianza](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Contraseñas de aplicación
En algunas aplicaciones, como Office 2010 o anteriores y Apple Mail no se puede utilizar la autenticación multifactor.  Para usar estas aplicaciones, tendrá que utilizar "contraseñas de aplicación" en lugar de la contraseña tradicional.  Las contraseñas de aplicación permiten a la aplicación omitir la autenticación multifactor y poder seguir trabajando.

> [!NOTE]
> Autenticación moderna para los clientes de Office 2013
> 
> Los clientes de Office 2013 (incluye Outlook) ahora admiten nuevos protocolos de autenticación que pueden habilitarse para admitir Multi-Factor Authentication.  Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013.  Para más información, consulte [Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
> 
> 

### <a name="important-things-to-know-about-app-passwords"></a>Aspectos importantes acerca de las contraseñas de aplicación
La siguiente es una lista importante de cosas que debe saber acerca de las contraseñas de aplicación.

* Los usuarios pueden tener varias contraseñas de aplicación, lo que aumenta el área expuesta al robo. Como son difíciles de recordar,  las contraseñas de aplicación se suelen anotar. Esto no es recomendable y no debe hacerse porque para iniciar sesión con la contraseña de aplicación es necesario solo un factor.
* Las aplicaciones que almacenan en caché las contraseñas y las usan en escenarios locales, pueden comenzar a dar error ya que la contraseña de aplicación no se reconocerá fuera del identificador de organización. Un ejemplo es el de los mensajes de correo electrónico de Exchange que son locales pero que se archivan en la nube. No funciona la misma contraseña.
* La contraseña real se genera automáticamente y no la proporciona el usuario. Esto es así  porque una contraseña generada automáticamente es más difícil de adivinar y por tanto es más segura.
* Actualmente hay un límite de 40 contraseñas por usuario. Para crear una nueva contraseña de aplicación se le pedirá que elimine una de las existentes.
* Una vez habilitada la autenticación multifactor en una cuenta de usuario, las contraseñas de aplicación pueden usarse con la mayoría de los clientes que no son explorador, como Outlook y Lync, pero no se puede realizar acciones administrativas con contraseñas de aplicación a través de aplicaciones que no son explorador, como Windows PowerShell, incluso si ese usuario tiene una cuenta administrativa.  Asegúrese de crear una cuenta de servicio con una contraseña segura para ejecutar scripts de PowerShell y no habilite esa cuenta para la autenticación multifactor.

> [!WARNING]
> Las contraseñas de aplicación no funcionan en entornos híbridos donde los clientes se comunican tanto en el entorno local como en los puntos de conexión de detección automática en la nube. Esto se debe a que se necesitan contraseñas de dominio para autenticarse en el entorno local y contraseñas de aplicación para autenticarse con la nube.
> 
> 

### <a name="naming-guidance-for-app-passwords"></a>Guía de nomenclatura para las contraseñas de aplicación
Se recomienda que los nombres de contraseña de aplicación reflejen el dispositivo en el que se usan. Por ejemplo, si tiene un equipo portátil que tiene aplicaciones sin explorador como Outlook, Word y Excel, sólo necesita crear una contraseña de aplicación denominada Portátil y utilizar esa contraseña de aplicación en todas estas aplicaciones. Aunque puede crear contraseñas independientes para cada una de estas aplicaciones, no se recomienda. El modo recomendado es usar una contraseña de aplicación por dispositivo.

<center>![Nube](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Contraseñas de aplicación federada (SSO)
Azure AD admite la federación con los Servicios de dominio de Active Directory (AD DS) de Windows Server. Si su organización está federada (SSO) con Azure AD y va a usar Azure Multi-Factor Authentication, a continuación tiene información importante que debe tener en cuenta al utilizar las contraseñas de aplicación. Esto se aplica solo a los clientes federados (SSO).

* La contraseña de aplicación se comprueba mediante Azure AD y, por tanto, omite la federación. La federación solo se usa activamente al configurar la contraseña de aplicación.
* Para los usuarios federados (SSO), nunca se usa el proveedor de identidades (IdP), a diferencia del flujo pasivo. Las contraseñas se almacenan en el identificador de la organización. Si el usuario abandona la empresa, esta información tiene que fluir al identificador de la organización usando DirSync en tiempo real. La deshabilitación o eliminación de la cuenta puede tardar hasta tres horas en sincronizarse, lo que retrasa la deshabilitación o eliminación de la contraseña de aplicación en Azure AD.
* La configuración del control de acceso de cliente local no se cumple con la contraseña de aplicación
* No hay ningún registro o capacidad de auditoría en la autenticación local que esté disponible para la contraseña de aplicación
* Es necesaria una  mayor formación del usuario final para el cliente de Microsoft Lync 2013. Para los pasos necesarios, vea cómo cambiar la contraseña en su correo electrónico a la contraseña de aplicación.
* Algunos diseños de arquitectura avanzada pueden requerir el uso de una combinación de nombre de usuario y contraseñas de la organización y de contraseñas de aplicación cuando se usa la autenticación multifactor con clientes, en función de dónde se autentiquen. Para los clientes que se autentican en una infraestructura local, utilizaría un nombre de usuario y una contraseña de la organización. Para los clientes que se autentican en Azure AD, usaría la contraseña de aplicación.

Por ejemplo, suponga que tiene una arquitectura que consta de lo siguiente:

* Federa su instancia local de Active Directory con Azure AD
* Usa Exchange Online
* Usa Lync que es específicamente local
* Usa Azure Multi-Factor Authentication

![Página de proofup](./media/multi-factor-authentication-whats-next/federated.png)

 En estos casos, debe hacer lo siguiente:

* Cuando inicie sesión en Lync, utilice el nombre de usuario y la contraseña de su organización.
* Al intentar obtener acceso a la libreta de direcciones a través de un cliente de Outlook que se conecta a Exchange online, utilice una contraseña de aplicación.

### <a name="allowing-app-password-creation"></a>Permiso para la creación de contraseñas de aplicación
De forma predeterminada, los usuarios no pueden crear contraseñas de aplicación.  Esta característica debe habilitarse.  Para permitir que los usuarios tengan la posibilidad de crear contraseñas de aplicación, use el procedimiento siguiente.

#### <a name="to-enable-users-to-create-app-passwords"></a>Para permitir que los usuarios creen contraseñas de aplicación
1. Inicie sesión en el portal clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio para el usuario que desea activar.
4. En la parte superior, haga clic en Usuarios.
5. En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth.  
6. En la parte superior de la página de autenticación multifactor, haga clic en Configuración del servicio.
7. Asegúrese de que está seleccionado el botón de opción de Permitir a los usuarios crear contraseñas de aplicación para iniciar sesión en aplicaciones que no son de explorador.

![Crear contraseñas de aplicación](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Creación de contraseñas de aplicación
Los usuarios pueden crear contraseñas de aplicación durante el registro inicial.  Se les da una opción al final del proceso de registro que les permite hacerlo.

Además, los usuarios también pueden crear contraseñas de aplicación más adelante cambiando su configuración en Azure Portal, el portal de Office 365 o

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Creación de contraseñas de aplicación en el portal de Office 365
- - -
1. Inicie sesión en el portal de Office 365.
2. En la esquina superior derecha, seleccione el widget de configuración.
3. A la izquierda seleccione Comprobación de seguridad adicional
4. A la derecha, seleccione **Actualizar los números de teléfono usados para la seguridad de cuenta**
5. En la página de proofup, en la parte superior, seleccione las contraseñas de aplicación
6. Haga clic en **Crear**
7. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**
8. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación.

<center>![Nube](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Creación de contraseñas de aplicación en el Portal de Azure
- - -
1. Inicie sesión en el portal clásico de Azure.
2. En la parte superior, haga clic con el botón derecho en su nombre de usuario y seleccione Comprobación de seguridad adicional.
3. En la página de proofup, en la parte superior, seleccione las contraseñas de aplicación
4. Haga clic en **Crear**
5. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**
6. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación.

![Contraseñas de aplicación](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Para crear contraseñas de aplicación si no tiene una suscripción a Office 365 o Azure
- - -
1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En la parte superior, seleccione el perfil.
3. Haga clic en su nombre de usuario y seleccione Comprobación de seguridad adicional.
4. En la página de proofup, en la parte superior, seleccione las contraseñas de aplicación
5. Haga clic en **Crear**
6. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**
7. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación.

![Contraseñas de aplicación](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Recordar Multi-Factor Authentication para confianza de usuarios de dispositivos
Recordar Multi-Factor Authentication para los dispositivos y exploradores en los que los usuarios confían es una característica gratuita para todos los usuarios de MFA.  Permite ofrecer a los usuarios la opción de omitir MFA durante un número determinado de días después de realizar un inicio de sesión correcto con MFA. Esto puede mejorar la experiencia de uso para los usuarios.

Sin embargo, como los usuarios pueden recordar MFA para los dispositivos de confianza, esta característica puede reducir la seguridad de la cuenta. Para garantizar la seguridad de las cuentas, debe restaurar Multi-Factor Authentication para sus dispositivos si se presenta cualquiera de los siguientes escenarios:

* Si  su cuenta corporativa se ha visto comprometida
* Si un dispositivo recordado se ha perdido o ha sido robado

> [!NOTE]
> Esta característica se implementa como una caché de cookies del explorador. No funcionará si no están habilitadas las cookies del explorador.
> 
> 

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Cómo habilitar o deshabilitar Recordar Multi-Factor Authentication
1. Inicie sesión en el portal clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Active Directory, haga clic en el directorio en el que desea configurar que los dispositivos recuerden la autenticación multifactor.
4. En el directorio que ha seleccionado, haga clic en Configurar.
5. En la sección de la autenticación multifactor, haga clic en Administrar configuración del servicio.
6. En la página Configuración del servicio, en la sección para administrar la configuración de dispositivos de usuario, active o desactive la opción **Permitir a los usuarios recordar Multi-Factor Authentication en los dispositivos en los que confían**.
   ![Recordar dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
7. Establezca el número de días que desea permitir la suspensión. El valor predeterminado es 14 días.
8. Haga clic en Guardar.
9. Haga clic en Cerrar.

## <a name="selectable-verification-methods"></a>Métodos de verificación seleccionables
En versiones de nube y locales, puede elegir qué métodos de comprobación están disponibles para los usuarios. En la tabla siguiente se proporciona una breve descripción de cada método.

Cuando los usuarios inscriben sus cuentas para MFA, deciden su método de comprobación preferido de las opciones habilitadas. Las instrucciones para el proceso de inscripción se tratan en [Configuración de mi cuenta para la comprobación en dos pasos](multi-factor-authentication-end-user-first-time.md).

| Método | Descripción |
|:--- |:--- |
| Llamada al teléfono |Hace una llamada de voz automática al teléfono de autenticación. El usuario responde a la llamada y pulsa la # del teclado del teléfono para autenticarse. Este número de teléfono no se sincroniza con Active Directory local. |
| Mensaje de texto al teléfono |Envía un mensaje de texto que contiene un código de verificación para el usuario. Se le pide al usuario que o bien conteste al mensaje de texto con el código de comprobación o que escriba el código de verificación en la interfaz de inicio de sesión. |
| Notificación a través de aplicación móvil |En este modo, la aplicación Microsoft Authenticator impide el acceso no autorizado a cuentas y detiene las transacciones fraudulentas. Esto se realiza mediante una notificación push en su teléfono o dispositivo registrado. Simplemente vea la notificación y, si es legítima, seleccione Verificar. En caso contrario, puede elegir Denegar o denegar e informar sobre la notificación fraudulenta. Para obtener información sobre cómo informar de notificaciones fraudulentas consulte How to use the Deny and Report Fraud Feature for Multi-Factor Authentication (Uso de la característica para denegar y notificar fraudes para Multi-Factor Authentication).</br></br>La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de comprobación desde aplicación móvil |En este modo, la aplicación Microsoft Authenticator puede utilizarse como un token de software para generar un código de comprobación de OATH. A continuación, este código de comprobación se puede escribir junto con el nombre de usuario y la contraseña a fin de proporcionar una segunda forma de autenticación.</li><br><p> La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Habilitación/deshabilitación de los métodos de autenticación
1. Inicie sesión en el portal clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Active Directory, haga clic en el directorio para el que desea habilitar o deshabilitar los métodos de autenticación.
4. En el directorio que ha seleccionado, haga clic en Configurar.
5. En la sección de la autenticación multifactor, haga clic en Administrar configuración del servicio.
6. En la página Configuración del servicio, en Opciones de comprobación, active o desactive las opciones que desee usar.</br></br>
   ![Opciones de comprobación](./media/multi-factor-authentication-whats-next/authmethods.png)
7. Haga clic en Guardar.
8. Haga clic en Cerrar.




<!--HONumber=Nov16_HO3-->


