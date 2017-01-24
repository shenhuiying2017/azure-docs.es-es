---
title: "Introducción a la administración de contraseñas en Azure AD | Microsoft Docs"
description: "Permita a los usuarios restablecer sus contraseñas, descubra los requisitos previos para el restablecimiento de contraseñas y permita la escritura diferida de contraseñas para administrar las contraseñas locales en Active Directory."
services: active-directory
keywords: "Administración de contraseñas de Active Directory, administración de contraseñas, restablecimiento de contraseñas de Azure AD"
documentationcenter: 
author: asteen
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/05/2016
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: e2e5c302d04a41386bfc98dd4e3f8546265dd9f3
ms.openlocfilehash: e686952a7363e4758f8a3532b54cf5e7f05ce865


---
# <a name="getting-started-with-password-management"></a>Introducción a la administración de contraseñas
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).
> 
> 

Permitir que los usuarios administren sus propias contraseñas de Active Directory local o de Azure Active Directory en la nube solo requiere de unos pocos pasos simples. Una vez que se asegure de haber cumplido algunos requisitos previos simples, tendrá habilitado el cambio y el restablecimiento de contraseñas para toda su organización de manera inmediata. Este artículo le guiará a través de los conceptos siguientes:

* [**Cómo permitir que los usuarios restablezcan sus contraseñas de Azure Active Directory en la nube**](#enable-users-to-reset-their-azure-ad-passwords)
  * [Requisitos previos para el restablecimiento de contraseña de autoservicio](#prerequisites)
  * [Paso 1: configurar la directiva de restablecimiento de contraseña](#step-1-configure-password-reset-policy)
  * [Paso 2: agregar datos de contacto del usuario de prueba](#step-2-add-contact-data-for-your-test-user)
  * [Paso 3: restablecer la contraseña como usuario](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Cómo permitir que los usuarios restablezcan o cambien sus contraseñas de Active Directory locales**](#enable-users-to-reset-or-change-their-ad-passwords)
  * [Requisitos previos de escritura diferida de contraseñas](#writeback-prerequisites)
  * [Paso 1: descargar la versión más reciente de Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
  * [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect)
  * [Paso 3: configurar el firewall](#step-3-configure-your-firewall)
  * [Paso 4: configurar los permisos adecuados](#step-4-set-up-the-appropriate-active-directory-permissions)
  * [Paso 5: restablecer la contraseña como usuario y comprobarla](#step-5-reset-your-ad-password-as-a-user)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Cómo permitir que los usuarios restablezcan sus contraseñas de Azure AD
En esta sección se explica cómo habilitar el restablecimiento de contraseña para el directorio en la nube de AAD, cómo registrar a los usuarios para el restablecimiento de contraseña de autoservicio y, finalmente, cómo realizar un restablecimiento de contraseña de autoservicio de prueba como usuario.

* [Requisitos previos para el restablecimiento de contraseña de autoservicio](#prerequisites)
* [Paso 1: configurar la directiva de restablecimiento de contraseña](#step-1-configure-password-reset-policy)
* [Paso 2: agregar datos de contacto del usuario de prueba](#step-2-add-contact-data-for-your-test-user)
* [Paso 3: restablecer la contraseña como usuario](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Requisitos previos
Antes de poder habilitar y utilizar el restablecimiento de contraseña de autoservicio, debe completar los siguientes requisitos previos:

* Crear un inquilino de AAD. Para obtener más información, consulte [Introducción a Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Obtener una suscripción de Azure. Para obtener más información, consulte [¿Qué es un inquilino de Azure AD?](active-directory-administer.md#what-is-an-azure-ad-tenant)
* Asociar su inquilino de AAD con su suscripción de Azure. Para obtener más información, consulte [Cómo se asocian las suscripciones a Azure con Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Actualice a Azure AD Premium, Basic o utilice una licencia de Office 365 de pago. Para obtener más información, consulte [Ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
  
  > [!NOTE]
  > Para habilitar el restablecimiento de contraseña de autoservicio para usuarios en la nube, debe actualizar a Azure AD Premium o a Azure AD Basic o utilizar una licencia de Office 365 de pago.  Para habilitar el restablecimiento de contraseña de autoservicio para usuarios locales, debe actualizar a Azure AD Premium. Para obtener más información, consulte [Ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Esta información incluye instrucciones detalladas sobre cómo suscribirse a Azure AD Premium o Basic, cómo activar su plan de licencias y el acceso de Azure AD y cómo asignar acceso a las cuentas de administrador y de usuario.
  > 
  > 
* Crear, al menos, una cuenta de administrador y una cuenta de usuario en el directorio de AAD.
* Asigne un AAD Premium, Basic o licencia de Office 365 de pago a la cuenta de administrador y a la cuenta de usuario que creó.

### <a name="step-1-configure-password-reset-policy"></a>Paso 1: configurar la directiva de restablecimiento de contraseña
Para configurar la directiva de restablecimiento de contraseña del usuario, complete los pasos siguientes:

1. Abra el explorador que prefiera y vaya al [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el [Portal de Azure clásico](https://manage.windowsazure.com), busque la **extensión de Active Directory** en la barra de navegación izquierda.
   
   ![Administración de contraseñas en Azure AD][001]
3. En la pestaña **Directorio** , haga clic en el directorio en el que desea configurar la directiva de restablecimiento de contraseña del usuario, por ejemplo, Wingtip Toys.
   
    ![][002]
4. Haga clic en la pestaña **Configure** .
   
   ![][003]
5. En la pestaña **Configurar**, desplácese hasta la sección de la **directiva de restablecimiento de la contraseña de usuario**.  Ahí puede configurar todos los aspectos de la directiva de restablecimiento de contraseña del usuario de un directorio determinado.  
   
   > [!NOTE]
   > Esta **directiva se aplica solo a los usuarios finales de su organización, no a los administradores**. Por motivos de seguridad, Microsoft controla la directiva de restablecimiento de contraseña de los administradores. Si no ve esta sección, asegúrese de haberse suscrito a Azure Active Directory Premium o Basic y de haber **asignado una licencia** a la cuenta de administrador que configura esta característica.
   > 
   > 
   
   ![][004]
6. Para configurar la directiva de restablecimiento de la contraseña de usuario, deslice el botón de alternancia **Usuarios habilitados para restablecer la contraseña** a **Sí**.  Con esto se revelan muchos controles más que le permitirán configurar cómo funciona esta característica en el directorio.  Puede personalizar el restablecimiento de contraseña como desee.  Si desea obtener más información sobre qué hace cada uno de los controles de la directiva de restablecimiento de contraseña, consulte [Personalización de la administración de contraseñas de Azure AD](active-directory-passwords-customize.md).
   
   ![][005]
7. Después de configurar la directiva de restablecimiento de contraseña del usuario como desea para su inquilino, haga clic en el botón **Guardar** en la parte inferior de la pantalla.
   
   > [!NOTE]
   > Se recomienda usar una directiva de restablecimiento de la contraseña del usuario de dos desafíos, con el fin de que pueda ver cómo funciona esta funcionalidad en el caso más complejo.
   > 
   > 
   
   ![][006]

### <a name="step-2-add-contact-data-for-your-test-user"></a>Paso 2: agregar datos de contacto del usuario de prueba
Tiene varias opciones para especificar los datos de los usuarios de su organización que se usarán para restablecer la contraseña.

* Editar usuarios en el [Portal de Azure clásico](https://manage.windowsazure.com) o en el [Portal de administración de Office 365](https://portal.microsoftonline.com)
* Usar AAD Connect para sincronizar las propiedades de usuario en Azure AD desde un dominio de Active Directory local
* Usar Windows PowerShell para editar las propiedades de usuario
* Permitir a los usuarios que registren sus propios datos guiándolos al portal de registro en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* Requerir a los usuarios que se registren para el restablecimiento de contraseña al iniciar sesión en el panel de acceso en [http://myapps.microsoft.com](http://myapps.microsoft.com) mediante el establecimiento de la opción de configuración del SSPR **¿Desea obligar a los usuarios a registrarse cuando inicien sesión en el panel de acceso?** en **Sí**

Si desea obtener más información sobre qué datos se usan para el restablecimiento de contraseña, además de cualquier requisito de formato de esos datos, consulte [¿Qué datos se utilizan para el restablecimiento de contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Para agregar datos de contacto del usuario a través del Portal de registro del usuario
1. Para usar el Portal de registro para el restablecimiento de contraseña, debe proporcionar a los usuarios de su organización un vínculo a esta página ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) o activar la opción para requerir que los usuarios se registren automáticamente.  Una vez que hagan clic en este vínculo, se les pedirá iniciar sesión con su cuenta profesional.  Después de hacerlo, verán la página siguiente:
   
   ![][007]
2. Aquí los usuarios pueden proporcionar y comprobar su teléfono móvil, su dirección de correo electrónico alternativa o las preguntas de seguridad.  La comprobación de un teléfono móvil tiene el aspecto siguiente.
   
   ![][008]
3. Una vez que un usuario especifica esta información, se actualizará la página para indicar que la información es válida (aparece encubierta abajo).  Si hace clic en los botones **Finalizar** o **Cancelar**, el usuario se desplazará hasta el panel de acceso.
   
   ![][009]
4. Una vez que un usuario compruebe ambos datos, su perfil se actualizará con la información proporcionada.  En este ejemplo, el número de **teléfono de la oficina** se especificó manualmente, por lo que el usuario también puede usarlo como método de contacto para restablecer su contraseña.
   
   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Paso 3: restablecer la contraseña de Azure AD como usuario
Ahora que configuró una directiva de restablecimiento de usuario y especificó los detalles de contacto del usuario, este puede realizar un restablecimiento de contraseña de autoservicio.

#### <a name="to-perform-a-self-service-password-reset"></a>Para realizar un restablecimiento de contraseña de autoservicio
1. Si va a un sitio como [**portal.microsoftonline.com**](http://portal.microsoftonline.com), verá una pantalla de inicio de sesión similar a la siguiente.  Haga clic en el vínculo **¿No puede tener acceso a su cuenta?** para probar la interfaz de usuario del restablecimiento de contraseña.
   
   ![][011]
2. Después de hacer clic en **¿No puede acceder a su cuenta?**, pasará a una página nueva en la que se le pedirá el **identificador del usuario** cuya contraseña desea restablecer.  Escriba aquí el **identificador de usuario** de prueba, escriba el CAPTCHA y haga clic en **Siguiente**.
   
   ![][012]
3. Dado que en este caso el usuario ha especificado un **teléfono de la oficina**, un **teléfono móvil** y un **correo electrónico alternativo**, verá que se le han dado todas esas opciones para pasar el primer desafío.
   
   ![][013]
4. En este caso, elija **llamar** primero al **teléfono de la oficina**.  Tenga en cuenta que, cuando se selecciona un método basado en teléfono, los usuarios deberán **comprobar su número de teléfono** antes de poder restablecer sus contraseñas.  Esto es para evitar que usuarios malintencionados realicen llamadas no deseadas a los números de teléfono de los usuarios de la organización.
   
   ![][014]
5. Una vez que el usuario confirme el número de teléfono, un clic en el muro de llamadas hará que aparezca un control de número y sonará su teléfono.  Cuando conteste el teléfono, se reproducirá un mensaje que indicará que el **usuario debe presionar "#"** para comprobar la cuenta.  Al presionar esta tecla, se comprobará automáticamente que el usuario cumple el primer desafío y la interfaz de usuario avanza al segundo paso de comprobación.
   
   ![][015]
6. Una vez que se cumple el primer desafío, la interfaz de usuario se actualiza automáticamente para quitarlo de la lista de opciones que tiene el usuario.  En este caso, como usó primero el **teléfono de la oficina**, solo quedan **Teléfono móvil** y **Correo electrónico** alternativo como opciones válidas para utilizarlas como el desafío para el segundo paso de la comprobación.  Haga clic en la opción **Enviar un mensaje de correo electrónico a mi dirección alternativa** .  Una vez hecho eso, al presionar el correo electrónico se enviará un mensaje de correo electrónico a la dirección alternativa existente en el archivo.
   
   ![][016]
7. El siguiente es un ejemplo de un correo electrónico que verán los usuarios; observe la personalización de marca del inquilino:
   
   ![][017]
8. Cuando llegue el correo electrónico, se actualizará la página y podrá escribir el código de comprobación que aparece en el correo electrónico en el cuadro de entrada que se muestra a continuación.  Una vez que se escribe un código adecuado, se ilumina el botón Siguiente y puede pasar al segundo paso de comprobación.
   
   ![][018]
9. Una vez cumplidos los requisitos de la directiva organizativa, podrá elegir una contraseña nueva.  La contraseña se valida cuando cumple los requisitos de contraseña "segura" de AAD (consulte [Directiva de contraseñas de Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)) y aparece un validador de seguridad que indica al usuario si la contraseña escrita satisface la directiva.
   
   ![][019]
10. Una vez que proporciona contraseñas coincidentes que satisfacen la directiva organizativa, se restablece la contraseña y puede iniciar inmediatamente sesión con la contraseña nueva.
    
    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Permitir que los usuarios restablezcan o cambien sus contraseñas de AD
En esta sección se explica cómo configurar el restablecimiento de contraseña para escribir contraseñas en diferido en Active Directory local.

* [Requisitos previos de escritura diferida de contraseñas](#writeback-prerequisites)
* [Paso 1: descargar la versión más reciente de Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Paso 3: configurar el firewall](#step-3-configure-your-firewall)
* [Paso 4: configurar los permisos adecuados](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Paso 5: restablecer la contraseña como usuario y comprobarla](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Requisitos previos de escritura diferida
Antes de poder habilitar y utilizar la escritura diferida, debe asegurarse de completar los siguientes requisitos previos:

* Tener un inquilino de Azure AD con Azure AD Premium habilitado.  Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
* Se configuró y habilitó el restablecimiento de contraseña en el inquilino.  Para obtener más información, consulte [Permitir que los usuarios restablezcan o cambien sus contraseñas de AD](#enable-users-to-reset-their-azure-ad-passwords)
* Tiene, al menos una cuenta de administración y una cuenta de usuario de prueba con una licencia de Azure AD Premium que puede utilizar para probar esta característica.  Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
  
  > [!NOTE]
  > Asegúrese de que la cuenta de administrador que usa para habilitar la escritura diferida de contraseña sea una cuenta de administrador de la nube (creada en Azure AD) y no una cuenta federada (creada en AD local y sincronizada en Azure AD).
  > 
  > 
* Tiene una implementación local de AD de uno o de varios bosques que ejecuta Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 o Windows Server 2012 R2 con los Service Packs más recientes instalados.
  
  > [!NOTE]
  > Si ejecuta una versión anterior de Windows Server 2008 o 2008 R2, puede seguir utilizando esta característica, pero necesita [descargar e instalar KB 2386717](https://support.microsoft.com/kb/2386717) antes de poder aplicar la directiva de contraseña de AD local en la nube.
  > 
  > 
* Tiene instalada la herramienta Azure AD Connect y preparó el entorno de AD para que se sincronice con la nube.  Para obtener más instrucciones, consulte [Uso de la infraestructura de identidad local en la nube](connect/active-directory-aadconnect.md).
  
  > [!NOTE]
  > Antes de probar la escritura diferida de contraseñas, asegúrese de finalizar primero una importación y sincronización completas de AD y Azure AD en Azure AD Connect.
  > 
  > 
* Si utiliza Sincronización de Azure AD o Azure AD Connect, será preciso abrir el puerto **TCP 443** de salida (y, en algunos casos, el puerto **TCP 9350-9354**).  Vea el [Paso 3: configurar el firewall](#step-3-configure-your-firewall) para obtener más información. Ya no se admite el uso de DirSync para este escenario.  Si todavía usa DirSync, actualice a la versión más reciente de Azure AD Connect antes de implementar la escritura diferida de contraseñas.
  
  > [!NOTE]
  > Recomendamos encarecidamente que quienes utilicen las herramientas Sincronización de Azure AD o DirSync actualicen a la versión más reciente de Azure AD Connect para asegurarse de contar con la mejor experiencia posible y las características nuevas tan pronto como se lanzan.
  > 
  > 

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Paso 1: descargar la versión más reciente de Azure AD Connect
La escritura diferida de contraseñas está disponible en las versiones de Azure AD Connect o en la herramienta Sincronización de Azure AD con el número de versión **1.0.0419.0911** o posterior.  La escritura diferida de contraseñas con desbloqueo automático de cuenta está disponible en las versiones de Azure AD Connect o en la herramienta Sincronización de Azure AD con el número de versión **1.0.0485.0222** o posterior. Si ejecuta una versión anterior, al menos actualice a esta versión antes de continuar. [Haga clic aquí para descargar la versión más reciente de Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Para comprobar la versión de Sincronización de Azure AD
1. Navegue a **%ProgramFiles%\Azure Active Directory Sync\**.
2. Busque el archivo ejecutable **ConfigWizard.exe** .
3. Haga clic con el botón derecho en el archivo ejecutable y seleccione la opción **Propiedades** del menú contextual.
4. Haga clic en la pestaña **Detalles** .
5. Busque el campo **Versión del archivo** .
   
   ![][021]

Si el número de la versión es mayor o igual que **1.0.0419.0911** o si va a instalar Azure AD Connect, puede ir directamente al [Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect a través de la interfaz de usuario o PowerShell y comprobarla](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Si es la primera vez que instala la herramienta Azure AD Connect, se recomienda que siga algunas prácticas recomendadas para preparar el entorno para la sincronización de directorios.  Antes de instalar la herramienta Azure AD Connect, debe activar la sincronización de directorios en el [Portal de administración de Office 365](https://portal.microsoftonline.com) o en el [Portal de Azure clásico](https://manage.windowsazure.com).  Para obtener más información, consulte [Administración de Azure AD Connect](active-directory-aadconnect-whats-next.md).
> 
> 

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Paso 2: habilitar la escritura diferida de contraseñas en Azure AD Connect
Ahora que descargó la herramienta Azure AD Connect, está listo para habilitar la escritura diferida de contraseñas.  Puede hacerlo de dos maneras.  Puede habilitar la escritura diferida de contraseñas en la pantalla de características opcionales del asistente para la configuración de Azure AD Connect, o bien puede habilitar a través de Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Para habilitar la escritura diferida de contraseñas en el asistente de configuración
1. En el equipo con **Sincronización de directorios**, abra el Asistente para configuración de **Azure AD Connect**.
2. Haga clic en los pasos hasta llegar a la pantalla de configuración de **características opcionales** .
3. Active la opción **Escritura diferida de contraseñas** .
   
   ![][022]
4. Complete el asistente. En la última página aparecerá un resumen de los cambios, el que incluirá el cambio de la configuración de la escritura diferida de contraseñas.

> [!NOTE]
> La escritura diferida de contraseñas se puede deshabilitar en cualquier momento si se ejecuta nuevamente este asistente y se desactiva la característica, o bien si se establece la opción **Escribir contraseñas en diferido en el directorio local** en **No** en la sección **Políticas para restablecer la contraseña del usuario** de la pestaña **Configurar** de su directorio en el [Portal de Azure clásico](https://manage.windowsazure.com).  Para obtener más información sobre cómo personalizar la experiencia de restablecimiento de contraseña, consulte [Personalización de la administración de contraseñas de Azure AD](active-directory-passwords-customize.md).
> 
> 

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Para habilitar la escritura diferida de contraseñas con Windows PowerShell
1. En el equipo con **Sincronización de directorios**, abra una nueva ventana de **Windows PowerShell con privilegios elevados**.
2. Si el módulo todavía no está cargado, escriba el comando `import-module ADSync` para cargar los cmdlets de Azure AD Connect en la sesión actual.
3. Para obtener la lista de los conectores de Azure AD del sistema, ejecute el cmdlet `Get-ADSyncConnector` y almacene los resultados en `$aadConnectorName`, como `$connectors = Get-ADSyncConnector|where-object {$\_.name -like "\*AAD"}`
4. Para obtener el estado actual de la escritura diferida del conector actual, ejecute el siguiente cmdlet: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Habilite la escritura diferida de contraseñas mediante la ejecución del cmdlet: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Si se le solicita una credencial, asegúrese de que la cuenta de administrador que especifica para AzureADCredential sea una **cuenta de administrador de la nube (creada en Azure AD)**y no una cuenta federada (creada en AD local y sincronizada en Azure AD).
> 
> [!NOTE]
> La escritura diferida de contraseñas se puede deshabilitar a través de PowerShell mediante la repetición de las mismas instrucciones anteriores, pero pasando `$false` en el paso, o bien mediante el establecimiento de la opción **Write Passwords Back to On-Premises Directory** (Escribir contraseñas en diferido en el directorio local) en **No** en la **sección Políticas para restablecer la contraseña del usuario** de la pestaña **Configurar** de su directorio en el [Portal de Azure clásico](https://manage.windowsazure.com).
> 
> 

#### <a name="verify-that-the-configuration-was-successful"></a>Comprobar que la configuración se realizó correctamente
Una vez que la configuración se realice correctamente, verá el mensaje La escritura diferida de restablecimiento de contraseña está habilitada en la ventana de Windows PowerShell o un mensaje de éxito en la interfaz de usuario de la configuración.

También puede comprobar que el servicio se ha instalado correctamente. Para ello, debe abrir Visor de eventos, navegar al registro de eventos de la aplicación y buscar el evento **31005 - OnboardingEventSuccess** en el origen **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Paso 3: configurar el firewall
Después de haber habilitado la Escritura diferida de contraseñas, debe asegurarse de que la máquina que ejecuta Azure AD Connect puede llegar a los servicios en la nube de Microsoft para recibir las solicitudes de escritura diferida de contraseñas. Este paso implica actualizar las reglas de conexión de los dispositivos de red (servidores proxy, firewalls, etc.) para permitir conexiones salientes a determinadas direcciones URL propiedad de Microsoft y a direcciones IP a través de puertos de red específicos. Estos cambios pueden variar en función de la versión de la herramienta Azure AD Connect. Para obtener más contexto, puede leer más sobre el [Funcionamiento de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#how-password-writeback-works) y el [Modelo de seguridad de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>¿Por qué tengo que hacerlo?

Para que la Escritura diferida de contraseñas funcione correctamente, la máquina que ejecuta Azure AD Connect debe poder establecer conexiones HTTPS salientes a **.servicebus.windows.net* y la dirección IP específica usada por Azure, tal como se define en la [lista de intervalos de IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Para la herramienta Azure AD Connect, versiones 1.0.8667.0 y posteriores:

- **Opción 1:** permitir todas las conexiones HTTPS salientes a través del puerto 443 (mediante la dirección URL o la dirección IP).
    - Cuándo utilizarlo:
        - Utilice esta opción si desea tener la configuración más sencilla, que no es necesario actualizar a medida que cambian los intervalos IP del centro de datos de Azure a lo largo del tiempo.
    - Pasos necesarios:
        - Permitir todas las conexiones HTTPS salientes a través del puerto 443 mediante la dirección URL o la dirección IP.
<br><br>
- **Opción 2:** permitir conexiones HTTPS salientes a direcciones URL e intervalos de IP específicos.
    - Cuándo utilizarlo:
        - Use esta opción si está en un entorno de red restringido, o bien si no se siente cómodo al permitir conexiones salientes.
        - En esta configuración, para que la escritura diferida de contraseñas continúe funcionando, debe asegurarse de que los dispositivos de conexión de red se actualizan cada semana con las direcciones IP más recientes de la lista de intervalos de IP del centro de datos de Microsoft Azure. Estos intervalos de IP están disponibles como un archivo XML que se actualiza todos los miércoles (hora del Pacífico), y entran en vigor el lunes siguiente (hora del Pacífico).
    - Pasos necesarios:
        - Permitir todas las conexiones HTTPS salientes a *.servicebus.windows.net.
        - Permitir todas las conexiones HTTPS salientes a todas las direcciones IP de la lista de intervalos de IP del centro de datos de Microsoft Azure y mantener esta configuración actualizada semanalmente.

> [!NOTE]
> Si ha configurado la Escritura diferida de contraseñas, sigue las instrucciones anteriores y no ve ningún error en el registro de eventos de Azure AD Connect, pero recibe errores de conectividad al realizar pruebas, puede ser que un dispositivo de conexión de red en su entorno esté bloqueando las conexiones HTTPS a direcciones IP. Por ejemplo, aunque se permita una conexión a *https://*.servicebus.windows.net*, es posible que una determinada dirección IP de ese intervalo esté bloqueada. Para resolver este problema, necesita configurar el entorno de red para permitir todas las conexiones HTTPS salientes a través del puerto 443 a cualquier dirección URL o dirección IP (opción 1 anterior) o trabajar con el equipo de red para permitir explícitamente las conexiones HTTPS a direcciones IP específicas (opción 2 anterior).

**Para versiones anteriores:**

- Permitir conexiones TCP salientes a través de los puertos 443, 9350-9354 y 5671. 
- Permitir conexiones salientes a *https://ssprsbprodncu-sb.accesscontrol.windows.net/*.

> [!NOTE]
> Si está en una versión de Azure AD Connect anterior a la 1.0.8667.0, Microsoft recomienda encarecidamente actualizar a la [versión más reciente de Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594), que incluye una serie de mejoras de red para la escritura diferida, con el fin de facilitar la configuración.

Una vez configurados los dispositivos de red, reinicie el equipo que ejecuta la herramienta Azure AD Connect.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Paso 4: configurar los permisos adecuados de Active Directory
En todos los bosques que contengan usuarios cuyas contraseñas se van a restablecer, si X es la cuenta que se especificó para ese bosque en el Asistente para configuración (durante la configuración inicial), X debe contar con los derechos extendidos **Restablecer contraseña**, **Cambiar contraseña**, **Escribir permisos** en `lockoutTime` y **Escribir permisos** en `pwdLastSet` sobre el objeto raíz de cada dominio de dicho bosque. El derecho debe estar marcado como heredado por todos los objetos de usuario.  

Si no está seguro de cuál es la cuenta a la que se hace referencia en el párrafo anterior, abra la UI de configuración de Azure Active Directory Connect y haga clic en la opción **Revisar su solución** .  La cuenta a la que debe agregar permisos se muestra subrayada en rojo en la captura de pantalla siguiente.

**<font color="red">Asegúrese de establecer este permiso para todos los dominios de todos los bosques del sistema, ya que, si no lo hace, la escritura diferida de contraseñas no funcionará correctamente.</font>**

  ![][032]

  Al establecer estos permisos se permitirá que la cuenta de servicio de agente de administración de cada bosque administre las contraseñas en nombre de las cuentas de usuario dentro de ese bosque. Si olvida asignar estos permisos, y aunque la escritura diferida aparecerá como correctamente configurada, los usuarios encontrarán errores cuando intenten administrar sus contraseñas locales desde la nube. Los siguientes son los pasos detallados sobre cómo puede hacer esto con el complemento de administración de **Usuarios y equipos de Active Directory** :

> [!NOTE]
> Estos permisos podrían demorar hasta una hora en replicarse a todos los objetos del directorio.
> 
> 

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Para configurar los permisos adecuados para que se realice la escritura diferida
1. Abra **Usuarios y equipos de Active Directory** con una cuenta con los permisos de administración de dominios adecuados.
2. En la opción **Menú Ver**, asegúrese de que **Características avanzadas** está activado.
3. En el panel izquierdo, haga clic con el botón derecho en el objeto que representa la raíz del dominio.
4. Haga clic en la pestaña **Seguridad** .
5. haga clic en **Opciones avanzadas**.
   
   ![][024]
6. En la pestaña **Permisos**, haga clic en **Agregar**.
   
   ![][025]
7. Seleccione la cuenta a la que desea otorgar permisos. Se trata de la misma cuenta que se especificó al configurar la sincronización de ese bosque.
8. En la lista desplegable que aparece en la parte superior, seleccione **Objetos de usuario descendientes**.
9. En el cuadro de diálogo **Entrada de permiso** que se muestra, active la casilla correspondiente a **Restablecer contraseña**, **Cambiar contraseña**, **Escribir permisos** en `lockoutTime` y **Escribir permisos** en `pwdLastSet`.
   
   ![][026]
   ![][027]
   ![][028]
10. Luego haga clic en **Aplicar o Aceptar** en todos los cuadros de diálogo abiertos.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Paso 5: restablecer la contraseña de AD como usuario
Ahora que la escritura diferida de contraseñas está habilitada, para saber si funciona, restablezca la contraseña de un usuario cuya cuenta se haya sincronizado en el inquilino de la nube.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Para comprobar que la escritura diferida de contraseñas funciona correctamente
1. Vaya a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) o a cualquier pantalla de inicio de sesión de identificación de la organización y haga clic en el vínculo **¿No puede tener acceso a su cuenta?**
   
   ![][029]
2. Debiera ver una página nueva que pide un Id. de usuario para el que desea restablecer una contraseña. Escriba su Id. de usuario de prueba y siga el flujo del restablecimiento de contraseñas.
3. Después de restablecer su contraseña, verá una pantalla similar a esta. Esto significa que restableció correctamente la contraseña en los directorios locales y de la nube.
   
   ![][030]
4. Para comprobar si la operación finalizó correctamente o diagnosticar algún error, vaya al **equipo con Sincronización de directorios**, abra el **Visor de eventos**, navegue al **registro de eventos de la aplicación** y busque el evento **31002 - PasswordResetSuccess** en el origen **PasswordResetService** del usuario de prueba.
   
   ![][031]

<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis componentes diferentes del servicio y lo que hace cada uno
* [**Personalizar**](active-directory-passwords-customize.md): obtenga información acerca de cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md): obtenga información acerca de cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización
* [**Obtener información**](active-directory-passwords-get-insights.md): obtenga información acerca de nuestras funcionalidades integradas de creación de informes.
* [**P+F**](active-directory-passwords-faq.md) : obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"



<!--HONumber=Jan17_HO2-->


