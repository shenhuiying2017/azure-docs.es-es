---
title: "Solución de problemas de la configuración de Enterprise State Roaming en Azure Active Directory | Microsoft Docs"
description: "Responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración."
services: active-directory
keywords: "configuración de enterprise state roaming, nube de windows, preguntas más frecuentes sobre enterprise state roaming"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: 8ee3b523baf562b06bd5f7d652a431e1d4553d5c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Solución de problemas de la configuración de Enterprise State Roaming en Azure Active Directory

En este tema se proporciona información sobre cómo diagnosticar y solucionar problemas Enterprise State Roaming, y proporciona una lista de problemas conocidos.

## <a name="preliminary-steps-for-troubleshooting"></a>Pasos preliminares para solucionar problemas 
Antes de comenzar la solución de problemas, compruebe que el usuario y el dispositivo se han configurado correctamente y que el dispositivo y el usuario cumplen todos los requisitos de Enterprise State Roaming. 

1. Windows 10, con las actualizaciones más recientes, y la versión 1511 como mínimo (compilación del sistema operativo 10586 o posterior) está instalado en el dispositivo. 
2. El dispositivo está unido a Azure AD o es un dispositivo híbrido unido a Azure AD. Para más información, consulte [cómo poner un dispositivo bajo el control de Azure AD](device-management-introduction.md).
3. Asegúrese de que **Enterprise State Roaming** está habilitado para el inquilino de Azure AD, como se describe en [Habilitación de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-enable.md). Puede habilitar la movilidad para todos los usuarios o solo un grupo seleccionado de usuarios.
4. El usuario ya debe tener asignada una licencia de Azure Active Directory Premium.  
25. Debe reiniciarse el dispositivo y el usuario debe iniciar sesión de nuevo para tener acceso a características de Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Información para incluir si necesita ayuda
Si no puede resolver el problema con las instrucciones siguientes, póngase en contacto con nuestros ingenieros de soporte técnico. Si se comunica con el soporte técnico, incluya la información siguiente:

* **Descripción general del error**: ¿el usuario ve mensajes de error? Si no hay ningún mensaje de error, describe detalladamente el comportamiento inesperado observado. ¿Qué características están habilitadas para la sincronización y qué espera sincronizar el usuario? ¿Son varias características las que no se sincronizan o está aislado a una?
* **Usuarios afectados**: el funcionamiento o error de la sincronización afecta a uno o a varios usuarios. ¿Cuántos dispositivos están implicados por usuario? ¿No se está sincronizando ninguno de ellos o algunos se están sincronizando y otros no?
* **Información sobre el usuario**: ¿qué identidad está usando el usuario para iniciar sesión en el dispositivo? ¿Cómo inicia sesión el usuario en el dispositivo? ¿Forman parte de un grupo de seguridad seleccionado al que se permite sincronizarse? 
* **Información sobre el dispositivo**: ¿este dispositivo está unido a Azure AD o unido a un dominio? ¿Cuál es la compilación del dispositivo? ¿Cuáles son las actualizaciones más recientes?
- **Fecha/hora/zona horaria**: ¿en qué fecha y a qué hora exactamente se ha generado el error (incluir la zona horaria)?

Incluir esta información nos ayudará a solucionar su problema lo antes posible.

## <a name="troubleshooting-and-diagnosing-issues"></a>Solución de problemas y diagnóstico de problemas
En esta sección se ofrecen sugerencias sobre cómo solucionar y diagnosticar problemas relacionados con Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Comprobación de la sincronización y la página de configuración de "Sincronizar la configuración" 

1. Después de unir los equipos Windows 10 a un dominio que está configurado para permitir Enterprise State Roaming, inicie sesión con su cuenta profesional. Vaya a **Configuración** > **Cuentas** > **Sincronizar mi configuración** y confirme que la sincronización y la configuración individual están activadas y que la parte superior de la página de configuración indica que está realizando la sincronización con su cuenta profesional. Confirme que la misma cuenta también se usa como la cuenta de inicio de sesión en **Settings** > **Accounts** > **Your Info** (Configuración > Cuentas > Su información). 
2. Compruebe que la sincronización funciona en varias máquinas realizando algunos cambios en la máquina original, como mover la barra de tareas al lado derecho o en la parte superior de la pantalla. Observe que el cambio se propaga a la segunda máquina en cinco minutos. 
  * El bloqueo y desbloqueo de la pantalla (Win + L) puede ayudar a desencadenar una sincronización.
  * Debe usar la misma cuenta de inicio de sesión en ambos PC para que funcione la sincronización, dado que Enterprise State Roaming está enlazado a la cuenta de usuario y no a la cuenta de máquina.

**Problema potencial**: si los controles de la página **Configuración** no están disponibles y ve el mensaje "Algunas características de Windows solo están disponibles si se usa una cuenta de Microsoft o una cuenta profesional". Este problema puede surgir en aquellos dispositivos que se han configurado para estar unidos a un dominio y registrados en Azure AD, pero el dispositivo todavía no se ha autenticado correctamente en Azure AD. Una posible causa es que se debe aplicar la directiva de dispositivo, pero esta aplicación se realiza de forma asincrónica y puede retrasarse unas pocas horas. 

### <a name="verify-the-device-registration-status"></a>Comprobación del estado de registro de dispositivo
Enterprise State Roaming requiere que el dispositivo esté registrado con Azure AD. Aunque no son específicas para Enterprise State Roaming, las instrucciones siguientes pueden ayudarle a confirmar que el cliente de Windows 10 está registrado, y confirmar la huella digital, la dirección URL de la configuración de Azure AD, el estado de NGC o cualquier otra información.

1.  Abra el símbolo del sistema sin privilegios elevados. Para ello, en Windows, abra el selector Ejecutar (Win + R) y escriba "cmd" para abrirlo.
2.  Una vez abierto el símbolo del sistema, escriba "*dsregcmd.exe /status*".
3.  Para la salida esperada, el valor del campo **AzureAdJoined** debe ser "YES", el valor del campo de **WamDefaultSet** debe ser "YES" y el valor del campo **WamDefaultGUID** debe ser un GUID con "(AzureAd)" al final.

**Problema potencial**: **WamDefaultSet** y **AzureAdJoined** tienen "NO" en el valor del campo, el dispositivo está unido a un dominio y registrado con Azure AD y no se sincroniza. Si se muestra esto, el dispositivo puede que tenga que esperar la aplicación de la directiva o se producirá un error de autenticación cuando el dispositivo se conecte a Azure AD. El usuario puede tener que esperar unas pocas horas para la directiva se aplique. Otros pasos de solución de problemas pueden incluir volver a intentar el registro automático mediante el cierre y el inicio de sesión, o el mediante el inicio de la tarea en el Programador de tareas. En algunos casos, puede ayudar a resolver este problema ejecutar "*dsregcmd.exe /leave*" en una ventana del símbolo del sistema con privilegios elevados, reiniciar y volver a intentar el registro.


**Problema potencial**: el campo de **AzureAdSettingsUrl** está vacío y el dispositivo no se sincroniza. El usuario puede haber iniciado sesión en el dispositivo antes de habilitar Enterprise State Roaming en el portal de Azure Active Directory. Reinicie el dispositivo pida al usuario que inicie sesión. Además, en el portal, pruebe a deshabilitar el administrador de TI y vuelva a habilitar la opción de que los usuarios puedan sincronizar la configuración y los datos de aplicaciones empresariales. Cuando se haya vuelto a habilitar, reinicie el dispositivo pida al usuario que inicie sesión. Si no se soluciona el problema, **AzureAdSettingsUrl** puede estar vacío en el caso de un certificado de dispositivo defectuoso. En este caso, puede ayudar a resolver este problema ejecutar "*dsregcmd.exe /leave*" en una ventana del símbolo del sistema con privilegios elevados, reiniciar y volver a intentar el registro.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming y Multi-Factor Authentication 
En algunas circunstancias, Enterprise State Roaming no puede sincronizar los datos si se ha configurado Azure Multi-Factor Authentication. Para más información sobre estos síntomas, consulte el documento de soporte técnico [KB3193683](https://support.microsoft.com/kb/3193683). 

**Problema potencial**: Si el dispositivo está configurado para requerir Multi-Factor Authentication en el portal de Azure Active Directory, se puede producir un error al sincronizar la configuración cuando se inicia sesión en un dispositivo Windows 10 con una contraseña. Este tipo de configuración de Multi-Factor Authentication se ha diseñado para proteger cuentas de administrador de Azure. Los usuarios administradores pueden seguir realizando la sincronización al iniciar sesión en sus dispositivos Windows 10 con su PIN de Microsoft Passport for Work o también pueden completar Multi-Factor Authentication al acceder a otros servicios de Azure, como Office 365.

**Problema potencial**: La sincronización puede producir un error si el administrador configura la directiva de acceso condicional de Multi-Factor Authentication de Active Directory Federation Services y caduca el token de acceso en el dispositivo. Asegúrese de iniciar sesión y de cerrarla con el PIN de Microsoft Passport for Work, o bien de completar Multi-Factor Authentication al acceder a otros servicios de Azure, como Office 365.

###<a name="event-viewer"></a>Visor de eventos
Para la solución avanzada de problemas, se puede usar el Visor de eventos para buscar errores específicos. Estos aparecen documentados en la tabla siguiente. Los eventos se pueden encontrar en Visor de eventos > Registros de aplicaciones y servicios > **Microsoft** > **Windows** > **SettingSync** y para problemas relacionados con la identidad con la sincronización **Microsoft** > **Windows** > **Azure AD**.


## <a name="known-issues"></a>Problemas conocidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>La sincronización no funciona en los dispositivos que tienen aplicaciones que se hayan cargado con software MDM

Afecta a los dispositivos que ejecutan la actualización de aniversario de Windows 10 (versión 1607). En el Visor de eventos en los registros de SettingSync-Azure, se suele ver el evento Id. 6013 con el error 80070259.

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1607 tiene la actualización acumulativa del 23 de agosto de 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) compilación del sistema operativo 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Los favoritos de Internet Explorer no se sincronizan

Afecta a los dispositivos que ejecutan la actualización de noviembre de Windows 10 (versión 1511).

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1511 tiene la actualización acumulativa de julio de 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) compilación del sistema operativo 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>El tema no se está sincronizando, así como los datos protegidos Windows Information Protection 

Para evitar la pérdida de datos, los datos que están protegidos con [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) no se sincronizarán a través de Enterprise State Roaming para dispositivos con la actualización de aniversario de Windows 10.



**Acción recomendada**  
Ninguno. Las futuras actualizaciones de Windows pueden resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>La configuración de fecha, hora y región no se sincroniza en dispositivos unidos a un dominio 
  
Los dispositivos que están unidos a un dominio no sincronizarán la configuración de fecha, hora y región: hora automática. El uso de la hora automática puede invalidar otras configuraciones de fecha, hora y región, y hacer que esos valores se sincronicen. 

**Acción recomendada**  
Ninguno. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC avisa al sincronizar las contraseñas

Afecta a los dispositivos que ejecutan la actualización de noviembre de Windows 10 (versión 1511) con una NIC inalámbrica configurada para sincronizar contraseñas.

**Acción recomendada**  
Asegúrese de que el cliente de Windows 10 v1511 tiene la actualización acumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) Compilación del sistema operativo 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>La sincronización no funciona en dispositivos que usan tarjetas inteligentes para el inicio de sesión
Si intenta iniciar sesión en el dispositivo Windows con una tarjeta inteligente o tarjeta inteligente virtual, la sincronización dejará de funcionar.     

**Acción recomendada**  
Ninguno. Las futuras actualizaciones de Windows pueden resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>El dispositivo unidos a un dominio no se sincroniza después de salir de la red corporativa     
Los dispositivos unidos a un dominio registrados en Azure AD pueden experimentar errores de sincronización si el dispositivo está fuera del sitio durante largos períodos de tiempo y no se puede completar la autenticación de dominio.

**Acción recomendada**  
Conecte el dispositivo a una red corporativa, para que pueda reanudarse la sincronización.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>El dispositivo unido a Azure AD no se está sincronizando y el usuario tiene un nombre principal de usuario con mayúsculas y minúsculas.
 Si el usuario se escribe con mayúsculas y minúsculas (por ejemplo, Nombre de Usuario en lugar de nombre de usuario) y el usuario se encuentra en un dispositivo unido a Azure AD que se ha actualizado de Windows 10 Build 10586 a 14393, se puede producir un error en el dispositivo del usuario al realizar la sincronización. 

**Acción recomendada**  
El usuario deberá separar y volver a unir el dispositivo a la nube. Para ello, inicie sesión como usuario de administrador local y separe el dispositivo en **Configuración** > **Sistema** > **Acerca de** y seleccione "Administrar o desconectar del trabajo o de la escuela". Limpie los archivos siguientes y, después, una de nuevo el dispositivo con Azure AD en **Configuración** > **Sistema** > **Acerca de** y seleccionando "Conectarse a la red del trabajo o colegio". Continúe uniendo el dispositivo con Azure Active Directory y complete el flujo.

En el paso de limpieza, limpie los archivos siguientes:
- Settings.dat en `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos los archivos en la carpeta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Id. de evento 6065: 80070533 Este usuario no puede iniciar sesión porque esta cuenta está deshabilitada  
En el Visor de eventos, en los registros de SettingSync/Debug, este error puede verse cuando las credenciales del usuario han expirado. Además, puede producirse cuando el inquilino no tenía AzureRMS aprovisionado automáticamente. 

**Acción recomendada**  
En el primer caso, pida al usuario que actualice sus credenciales e inicie sesión en el dispositivo con las nuevas credenciales. Para resolver el problema de AzureRMS, continúe con los pasos enumerados en [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Id. de evento 1098: Error: error en la operación del agente de token de 0xCAA5001C  
En el Visor de eventos, en los registros AAD/Operational, este error puede aparecer como evento 1104: llamada de complemento AP de nube AAD Obtener error devuelto por token: 0xC000005F. Este problema se produce si faltan permisos o atributos de propiedad.  

**Acción recomendada**  
Continúe con los pasos enumerados en el artículo [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>pasos siguientes

- Emplee el foro [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) para proporcionar comentarios y realizar sugerencias sobre cómo mejorar Enterprise State Roaming.

- Para más información, consulte [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Temas relacionados
* [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Preguntas más frecuentes sobre itinerancia de datos y configuración](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
