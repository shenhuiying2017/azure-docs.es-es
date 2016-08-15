<properties
	pageTitle="Preguntas más frecuentes sobre itinerancia de datos y configuración | Microsoft Azure"
	description="Responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración."
	services="active-directory"
    keywords="configuración de enterprise state roaming, nube de windows, preguntas más frecuentes sobre enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="femila"/>

# Preguntas más frecuentes sobre itinerancia de datos y configuración

Este tema responde a algunas preguntas que los administradores de TI podrían tener sobre la sincronización de datos de aplicación y la configuración.

## ¿Qué datos se movilizan?
**Configuración de Windows**: la configuración de PC que está integrada en el sistema operativo Windows. Por lo general, estas son opciones que personalizan el equipo del usuario, e incluyen las categorías generales siguientes:

- **Tema**: tema del escritorio, configuración de la barra de tareas, etc.
- **Configuración de Internet Explorer**: pestañas abiertas recientemente, favoritos, etc.
- **Configuración del explorador Edge**: favoritos, lista de lectura
- **Contraseñas**: contraseñas de Internet, perfiles de Wi-Fi, etc.
- **Preferencias de idioma**: distribuciones del teclado, idioma del sistema, fecha y hora, etc.
- **Facilidad de acceso**: tema de contraste alto, narrador, lupa, etc.
- **Otra configuración de Windows**: configuración de símbolo del sistema, lista de aplicaciones, etc.

**Datos de la aplicación**: las aplicaciones universales de Windows pueden escribir los datos de configuración en una carpeta de "movilidad" y todos los datos escritos en esta carpeta se sincronizarán automáticamente. Depende del desarrollador de las aplicaciones individuales el diseñar una aplicación para sacar partido a esta funcionalidad. Para más información sobre cómo desarrollar una aplicación universal de Windows que usa la movilidad, consulte [Almacenar y recuperar la configuración y otros datos de aplicación](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) y el [blog para desarrolladores de itinerancia de datos de aplicación de Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## ¿Qué cuenta se usa para la sincronización de configuración?
En Windows 8 y Windows 8.1, la sincronización de configuración siempre utiliza cuentas de Microsoft de consumidor. Los usuarios empresariales tenían la capacidad de conectar una cuenta de Microsoft a su cuenta de dominio de Active Directory para obtener acceso a la sincronización de configuración. En Windows 10, esta funcionalidad "de cuenta de Microsoft conectada" se ha sustituido por un marco de cuenta principal o secundario.

La cuenta principal se define como la cuenta usada para iniciar sesión en Windows: puede ser una cuenta de Microsoft, una cuenta de Azure Active Directory (Azure AD), una cuenta de Active Directory local o una cuenta local. Además de la cuenta principal, los usuarios de Windows 10 pueden agregar una o más cuentas de nube secundarias a su dispositivo. Estas cuentas secundarias suelen ser una cuenta de Microsoft, una cuenta de Azure Active Directory o alguna otra cuenta como Gmail o Facebook. Estas cuentas secundarias proporcionan acceso a servicios adicionales, como el inicio de sesión único o a la Tienda Windows, pero no pueden activar la sincronización de configuración.

En Windows 10, solo puede utilizarse la cuenta principal del dispositivo puede utilizarse para sincronizar la configuración (consulte [¿Cómo actualizo desde la sincronización de configuración de cuenta de Microsoft en Windows 8 a la sincronización de configuración de Azure AD en Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)).

Los datos nunca se mezclan entre las distintas cuentas de usuario en el dispositivo. La sincronización de la configuración tiene dos reglas:
- La configuración de Windows siempre usará un perfil itinerante con la cuenta principal.
- Los datos de la aplicación se etiquetarán con la cuenta utilizada para adquirir la aplicación. Solo se sincronizarán las aplicaciones etiquetadas con la cuenta principal. El etiquetado de la propiedad de la aplicación se determina cuando se instala de prueba una aplicación a través de la Tienda Windows o de la administración de dispositivos móviles (MDM).

Si no se puede identificar al propietario de una aplicación, se movilizará con la cuenta principal. Si un dispositivo se actualiza desde Windows 8 o Windows 8.1 a Windows 10, todas las aplicaciones se etiquetarán como adquiridas por la cuenta de Microsoft, ya que, en general, la mayoría de las aplicaciones se adquirieron a través de la Tienda Windows y no había compatibilidad de la Tienda Windows con cuentas de Azure AD antes de Windows 10. Si se instala una aplicación a través de una licencia sin conexión, la aplicación se etiquetará mediante la cuenta principal en el dispositivo.

>[AZURE.NOTE]  
Los dispositivos Windows 10 corporativos y que estén asociados a Azure AD ya no pueden conectar sus cuentas Microsoft a una cuenta de dominio. La capacidad de conectar una cuenta de Microsoft con una cuenta de dominio y de tener toda la sincronización de datos del usuario en la cuenta de Microsoft (es decir, movilidad de la cuenta de Microsoft a través de la funcionalidad "cuenta de Microsoft y Active Directory conectados") se quita de los dispositivos Windows 10 que se unen a un entorno Active Directory o Azure AD conectado.

## ¿Cómo actualizo desde la sincronización de configuración de cuenta de Microsoft en Windows 8 a la sincronización de configuración de Azure AD en Windows 10?
Un usuario unido al dominio de Active Directory que ejecuta Windows 8 o Windows 8.1 con una cuenta de Microsoft conectada sincronizará la configuración a través de la cuenta de usuario de Microsoft. Después de actualizar a Windows 10, los usuarios unidos a dominio sincronizarán la configuración de usuario a través de la cuenta de Microsoft, siempre que el dominio de Active Directory no se conecte con Azure AD. Si el dominio de Active Directory local se conecta con Azure AD, el dispositivo del usuario comenzará a intentar sincronizar la configuración mediante la cuenta de Azure AD conectada. Si el administrador de Azure AD no permite Enterprise State Roaming, los usuarios con una cuenta de Azure AD conectada detendrán la sincronización de la configuración. Después de habilitar la sincronización de configuración a través de Azure AD, los usuarios de Windows 10 iniciarán inmediatamente la sincronización de configuración de Windows con Azure AD si el usuario inicia sesión con una identidad de Azure AD.

Los usuarios que almacenan sus datos personales en los dispositivos corporativos deben ser conscientes de que el sistema operativo Windows y los datos de la aplicación comenzarán a sincronizarse en Azure AD. Esto tiene las implicaciones siguientes:

- La configuración de las cuentas Microsoft personales se irá desvinculando poco a poco de la de las cuentas de Azure AD profesionales o educativas, ya que, en estos momentos, la funcionalidad de sincronización de la configuración de Azure AD y cuentas Microsoft utiliza cuentas independientes.
- Ahora, la sincronización de los datos personales, como contraseñas Wi-Fi, credenciales web, y pestañas y favoritos de Internet Explorer —que anteriormente se realizaba a través de una cuenta Microsoft conectada—, se llevará a cabo mediante Azure AD.


## ¿Cómo funciona la interoperabilidad de la cuenta de Microsoft y Enterprise State Roaming de Azure AD?
En la versión de noviembre de 2015 o posterior de Windows 10, Enterprise State Roaming solo puede utilizarse para una sola cuenta a la vez. Si el usuario inicia sesión en Windows con una cuenta profesional o educativa de Azure AD, todos los datos se sincronizarán a través de Azure AD. Si el usuario inicia sesión en Windows mediante una cuenta personal de Microsoft, todos los datos se sincronizarán a través de la cuenta de Microsoft. Los datos de aplicación universal usarán un perfil itinerante con la cuenta de inicio de sesión principal del dispositivo si la licencia de la aplicación es propiedad de la cuenta principal. No se sincronizarán los datos de aplicación universal de las aplicaciones que son propiedad de cuentas secundarias.

## ¿Se sincroniza la configuración para las cuentas de Azure AD desde varios inquilinos?
Cuando varias cuentas de AD Azure de distintos inquilinos de Azure AD se encuentran en el mismo dispositivo, debe actualizar el registro del dispositivo para comunicarse con el servicio Azure Rights Management Service (RMS) para cada inquilino de Azure AD.

1. En primer lugar, necesita el GUID para cada inquilino de Azure AD. Abra el Portal de Azure clásico y seleccione un inquilino de Azure AD. El GUID del inquilino es la dirección URL que aparece en la barra de direcciones del explorador, tal y como se muestra a continuación: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Cuando ya disponga del GUID, tendrá que agregar la siguiente clave del Registro: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<GUID del id. del inquilino>**. En la clave <**GUID del id. del inquilino**>, cree un valor de cadena múltiple (REG-MULTI-SZ) denominado "**AllowedRMSServerUrl**" y, para sus datos, especifique las direcciones URL de los puntos de distribución de licencias de los demás inquilinos de Azure a los que accede el dispositivo.
3. Puede encontrar las direcciones URL de los puntos de distribución de licencias ejecutando el cmdlet **Get-AadrmConfiguration**. Si los valores de **LicensingIntranetDistributionPointUrl **y **LicenseingExtranetDistributionPointUrl** son diferentes, especifique los dos. Si los valores son iguales, especifique solo un valor.


## ¿Cuáles son las opciones de configuración de itinerancia de las aplicaciones de escritorio de Windows existentes?
La itinerancia solo funciona en las aplicaciones universales de Windows. Hay dos opciones disponibles para habilitar la itinerancia en aplicaciones de escritorio de Windows existentes:

- Con [Desktop Bridge](http://aka.ms/desktopbridge) puede transferir fácilmente sus aplicaciones de escritorio de Windows a la plataforma universal de Windows. A partir de aquí, habrá que realizar cambios mínimos en el código para poder aprovechar las ventajas de la itinerancia de datos de la aplicación de Azure AD. Desktop Bridge proporciona a las aplicaciones una identidad de aplicación, que se requiere para habilitar la itinerancia de datos de las aplicaciones de escritorio existentes.
- Con [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx), puede crear una plantilla de configuración personalizada para aplicaciones de escritorio de Windows existentes y habilitar la itinerancia solo en las aplicaciones de Win32. Esta opción no precisa que el desarrollador cambie el código de la aplicación. Solo los clientes que han comprado el paquete de optimización de escritorio de Microsoft pueden utilizar UE-V para habilitar la itinerancia de Active Directory local.

El administrador puede configurar UE-V para usar un perfil itinerante en los datos de aplicación de escritorio de Windows deshabilitando la itinerancia de la configuración de sistema operativo Windows y de los datos de aplicación universal mediante las [directivas de grupo de UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2).

- Deshabilite la directiva de grupo "Roam Windows settings" (Usar perfil itinerante en la configuración de Windows).
- Habilite la directiva de grupo "Do not synchronize Windows Apps" (No sincronizar aplicaciones de Windows).
- Deshabilite la itinerancia de Internet Explorer en la sección de aplicaciones

En el futuro, Microsoft podría investigar formas de integrar UE-V profundamente en Windows y ampliar UE-V para movilizar la configuración a través de la nube de Azure AD.


## ¿Se pueden almacenar configuraciones sincronizados y datos locales?
Enterprise State Roaming almacena todos los datos sincronizados en la nube de Azure, mientras que UE-V ofrece una solución de itinerancia local para las empresas que tienen esta necesidad.

## ¿Quién administra los datos que usan un perfil itinerante?
Las empresas tienen la propiedad de los datos que usan un perfil itinerante mediante Enterprise State Roaming. Los datos se almacenan en un centro de datos de Azure. Todos los datos de usuario se cifran en tránsito y en reposo en la nube mediante Azure Rights Management (Azure RMS). Esto es una mejora comparado con la sincronización de configuración basada en la cuenta de Microsoft, donde solo determinados datos confidenciales, como las credenciales de usuario, se cifran antes de dejar el dispositivo.

Microsoft se compromete a proteger los datos de los clientes. Los datos de configuración de los usuarios empresariales se cifran automáticamente mediante Azure RMS cada vez que salen de un dispositivo Windows 10, de tal forma que ningún usuario pueda leer estos datos. Si la organización tiene una suscripción de pago para Azure RMS, puede utilizar otras características de Azure RMS, como el seguimiento y revocación de documentos, proteger automáticamente correos electrónicos que contienen información confidencial y administrar sus propias claves (la solución "bring your own key" solución, también conocida como BYOK). Para más información sobre estas características y cómo funciona Azure RMS, consulte [What is Azure Rights Management?](https://technet.microsoft.com/jj585026.aspx) (¿Qué es Azure Rights Management?)

## ¿Puedo administrar la sincronización para una aplicación o configuración específica?
En Windows 10, no hay ninguna configuración de MDM o de directiva de grupo para deshabilitar la itinerancia en una aplicación individual. Los administradores de inquilinos pueden deshabilitar la sincronización de los datos de la aplicación en todas las aplicaciones en un dispositivo administrado, pero no hay ningún control más preciso de nivel de aplicación o dentro de la aplicación.

## ¿Qué puede hacer un usuario individual para habilitar o deshabilitar la movilidad?
En la aplicación** Configuración**, vaya a **Cuentas** -> **Sincronizar la configuración**. En esta página, puede ver qué cuenta se usa para movilizar la configuración y puede habilitar o deshabilitar los grupos de configuración individuales que se van a movilizar.

##¿Cuál es la recomendación actual de Microsoft para habilitar la movilidad en Windows 10?
Microsoft tiene diferentes soluciones de itinerancia de configuración, incluidos los perfiles de usuario móviles, UE-V y Enterprise State Roaming. En futuras versiones de Windows, Microsoft se ha comprometido a invertir en Enterprise State Roaming. Si su organización no está preparada o no está cómoda con el desplazamiento de datos a la nube, Microsoft recomienda utilizar UE-V como principal tecnología móvil. Si su organización necesita habilitar la itinerancia en sus aplicaciones de escritorio de Windows y está dispuesta a migrar los datos a la nube, Microsoft recomienda utilizar Enterprise State Roaming y UE-V. Aunque estas dos soluciones son tecnologías muy similares, no son incompatibles. Actualmente, se complementan entre sí para garantizar que su organización proporcione los servicios de itinerancia que necesitan los usuarios.

Al utilizar Enterprise State Roaming y UE-V, se aplican las siguientes reglas:

- Enterprise State Roaming es el agente de movilidad principal en el dispositivo. UE-V se utiliza para complementar el "intervalo de Win32".
- La itinerancia que ofrece UE-V de la configuración de Windows y los datos de aplicaciones modernas para UWP debe deshabilitarse con directivas de grupo de UE-V; el motivo es que Enterprise State Roaming ya se encarga de esto.

##¿Cómo admite Enterprise State Roaming la Infraestructura de escritorio virtual (VDI)?
Enterprise State Roaming solo es compatible con las SKU de cliente de Windows 10, pero no con las de servidor. Si una máquina virtual cliente está hospedada en una máquina de hipervisor y el usuario final accede a la máquina virtual de manera remota, los datos del usuario usarán un perfil itinerante. Si varios usuarios comparten el mismo sistema operativo y acceden a un servidor de manera remota para disfrutar de una experiencia de escritorio completa, es posible que la itinerancia no funcione. No se admite oficialmente el escenario basado en la última sesión.


## ¿Qué ocurre cuando mi organización adquiere Azure RMS después de usar la movilidad durante un tiempo?
Si su organización ya está usando la itinerancia en Windows 10 con la suscripción gratuita de uso limitado de Azure RMS y compra una suscripción de Azure RMS de pago, no afectará a la funcionalidad de la característica de itinerancia y el administrador de TI no tendrá que realizar cambios en la configuración.

## Problemas conocidos

- El inicio de sesión de la tarjeta inteligente o de la tarjeta inteligente virtual en Windows hace que la sincronización de configuración deje de funcionar. Si intenta iniciar sesión en el dispositivo con una tarjeta inteligente o tarjeta inteligente virtual, la sincronización dejará de funcionar. Las futuras actualizaciones de Windows 10 pueden resolver este problema.
- La sincronización de los favoritos de Internet Explorer no funciona en versiones anteriores de la compilación de Windows 10. Necesitará la actualización acumulativa de julio de Windows 10 (compilación 10586.494 o posterior) para que funcione la sincronización de los favoritos de Internet Explorer.
- En algunas circunstancias, Enterprise State Roaming no puede sincronizar los datos si se ha configurado la autenticación multifactor (MFA).
    - Si se ha establecido que el usuario tenga que autenticarse con [Azure MFA](multi-factor-authentication.md) en el Portal de Azure AD, es posible que no se pueda sincronizar la configuración al iniciar sesión en un dispositivo Windows 10 con una contraseña. Este tipo de configuración de MFA se ha diseñado para proteger cuentas de administrador de Azure. Los usuarios administradores pueden seguir realizando la sincronización al iniciar sesión en sus dispositivos Windows 10 mediante el PIN de [Microsoft Passport for Work](active-directory-azureadjoin-passport.md); como alternativa, también pueden completar la autenticación multifactor al acceder a otros servicios de Azure, como Office 365.
    - Puede que sincronización no se realice correctamente si el administrador configura la directiva de acceso condicional de MFA de AD FS y expira el token de acceso del dispositivo. No se olvide de cerrar la sesión e iniciarla con el PIN de [Microsoft Passport for Work](active-directory-azureadjoin-passport.md), o bien de completar la autenticación multifactor al acceder a otros servicios de Azure, como Office 365.
   
- Si una máquina se une mediante dominios con registro automático a dispositivos de Azure AD, puede experimentar un error de sincronización si la maquina está fuera del sitio durante largos períodos de tiempo y no se podrá completar la autenticación de dominio. Para resolver este problema, conecte la máquina a una red corporativa para que se pueda reanudar la sincronización.


## Temas relacionados
- [Información general de Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitación de Enterprise State Roaming en Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Configuración de MDM y directivas de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referencia de la configuración de movilidad de Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0803_2016-->