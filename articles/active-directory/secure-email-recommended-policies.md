---
title: "Directivas recomendadas de protección del correo electrónico de Azure AD | Microsoft Docs"
description: "Se describen las directivas de las recomendaciones de Microsoft sobre cómo aplicar directivas y configuraciones de correo electrónico."
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 1747ba5ff55780322eeaae5396099ec373289c98
ms.contentlocale: es-es
ms.lasthandoff: 06/08/2017


---

# <a name="recommended-policies"></a>Directivas recomendadas
 
En este artículo se describen las directivas recomendadas para ayudar a nuestros clientes a proteger el correo electrónico de la organización y contribuir a que los clientes de correo electrónico puedan admitir la autenticación moderna y el acceso condicional. También se analizan las configuraciones de cliente de plataforma predeterminadas que se recomiendan para proporcionar la mejor experiencia de inicio de sesión único a sus usuarios y se describen los requisitos previos para el acceso condicional.

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar las directivas que se describen en el resto de este documento, son varios los requisitos previos que debe cumplir su organización:
* [Configure redes con nombre](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal). Azure AD Identity Protection recopila y analiza todos los datos de sesión disponibles para generar una puntuación de riesgo. Se recomienda que especifique los intervalos de IP públicas de su organización para la red en la configuración de redes con nombre de Azure AD. Al tráfico procedente de estos intervalos se le da una puntuación de riesgo reducido, por lo que el tráfico de fuera del entorno corporativo se trata como de alto riesgo.
* [Registre todos los usuarios con la autenticación multifactor (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices). Azure AD Identity Protection usa Azure MFA para realizar comprobaciones de seguridad adicionales. Se recomienda que exija que todos los usuarios se registren en Azure MFA con antelación.
* [Habilite el registro automático de dispositivos de los equipos unidos a un dominio](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). El acceso condicional puede garantizar que el dispositivo que se conecta al servicio sea un dispositivo compatible o unido a un dominio. Para que este método de seguridad se admita en equipos Windows, el dispositivo debe estar registrado con Azure AD.  En este artículo se describe cómo configurar el registro automático de dispositivos.  Tenga en cuenta que AD FS es un requisito.
* **Prepare el equipo de soporte técnico**. Disponga de un plan para los usuarios que no puedan completar MFA. Por ejemplo, puede agregarlos a un grupo de exclusión de directivas o registrar nueva información de MFA para ellos. Antes de realizar cualquiera de estos cambios de seguridad delicados, debe asegurarse de que el usuario real realice la solicitud. Un paso útil es pedir a los responsables de los usuarios que ayuden con la aprobación.
* [Configure la escritura diferida de contraseñas en AD local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). La escritura diferida de contraseñas permite a Azure AD exigir que los usuarios cambien sus contraseñas locales cuando se haya detectado un alto riesgo de que las cuentas se vean comprometidas. Puede habilitar esta característica mediante Azure AD Connect de dos maneras. Puede habilitar la escritura diferida de contraseñas en la pantalla de características opcionales del asistente para la configuración de Azure AD Connect, o bien puede habilitar a través de Windows PowerShell.  
* [Habilite la autenticación moderna](https://support.office.com/en-us/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662) y [proteja los puntos de conexión heredados](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-supported-apps).  El acceso condicional funciona con aplicaciones móviles y de escritorio que usen la autenticación moderna. Si la aplicación emplea protocolos de autenticación heredados, puede obtener acceso a pesar de las condiciones que se apliquen. Es importante saber qué aplicaciones pueden usar las reglas de acceso condicional y los pasos que debería llevar a cabo para proteger otros puntos de entrada de la aplicación.
* [Habilite Azure Information Protection](https://docs.microsoft.com/en-us/information-protection/get-started/infoprotect-tutorial-step1) mediante la activación de Rights Management. Use Azure Information Protection con el correo electrónico para comenzar a clasificarlo. Siga el tutorial de inicio rápido para personalizar y publicar la directiva.  

### <a name="recommended-email-clients"></a>Clientes de correo electrónico recomendados
Los siguientes clientes de correo electrónico admiten autenticación moderna y acceso condicional. Azure Information Protection todavía no está disponible para todos los clientes.

|Plataforma|Cliente|Versión/notas|Azure Information Protection|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016, 2013 ([Habilitación de la autenticación moderna]((https://support.office.com/en-us/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910))|Sí|
|**iOS**|Outlook|[Más reciente](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|No|
|**Android**|Outlook|[Más reciente](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|No|
|**macOS**|En breve habrá compatibilidad.||No|
|**Linux**|No compatible||No|

#### <a name="additional-client-software"></a>Software cliente adicional
Para acceder a documentos protegidos con Azure Information Protection, puede ser necesario software adicional. Asegúrese de que usa [software y formatos de documento admitidos](https://docs.microsoft.com/information-protection/get-started/requirements-applications) para crear y ver documentos protegidos con Azure Information Protection.

## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>Configuración de cliente recomendada inicio de sesión único y acceso condicional
En esta sección se describen las configuraciones de cliente de plataforma predeterminadas que se recomiendan para proporcionar la mejor experiencia de inicio de sesión único a sus usuarios, así como los requisitos previos para el acceso condicional.

### <a name="windows-devices"></a>Dispositivos Windows
Se recomienda Windows 10 (versión 1703 o posterior), dado que Azure está diseñado para proporcionar la mejor experiencia posible de inicio de sesión único tanto para el entorno local como para Azure AD. Los dispositivos emitidos por una institución educativa o una empresa deben estar configurados para unirse directamente a Azure AD; o bien, si la organización usa la unión a un dominio de AD local, esos dispositivos deben estar [configurados para registrarse de manera automática y silenciosa con Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). 

En dispositivos Windows BYOD, los usuarios pueden usar "Agregar cuenta profesional o educativa". En dispositivos Windows BYOD, los usuarios pueden usar "Agregar cuenta profesional o educativa". Cabe señalar que los usuarios del explorador Chrome en Windows 10 deben [instalar una extensión](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog) para poder obtener la misma experiencia de inicio de sesión fluida que en Edge o IE. Además, si su organización tiene dispositivos Windows 7 unidos a un dominio, puede instalar el [paquete de Microsoft Workplace Join para equipos que no son Windows 10 para registrar](https://www.microsoft.com/en-us/download/details.aspx?id=53554) los dispositivos con Azure AD.

### <a name="ios-devices"></a>Dispositivos iOS

Se recomienda instalar la [aplicación Microsoft Authenticator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) en los dispositivos de usuario antes de implementar directivas MFA o de acceso condicional. Como mínimo, la aplicación se debe instalar cuando [se solicite a los usuarios registrar su dispositivo](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time) en Azure AD agregando una cuenta profesional o educativa, o cuando instalan la aplicación del Portal de empresa para inscribir sus dispositivos en la administración. Esto depende de la directiva de acceso condicional configurada.

### <a name="android-devices"></a>Dispositivos Android

Se recomienda que los usuarios instalen la [aplicación del Portal de empresa de Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
) y la [aplicación Microsoft Authenticator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) antes de que se implementen las directivas de acceso condicional o cuando sea necesario durante determinados intentos de autenticación. Después de instalar la aplicación, puede que se pida a los usuarios que se registren en Azure AD o que inscriban su dispositivo en Intune. Esto depende de la directiva de acceso condicional configurada. 

También se recomienda que los dispositivos de propiedad corporativa (COD) se estandaricen en los fabricantes de equipos originales (OEM) y las versiones que admitan Android for Work o Samsung Knox a fin de permitir que las cuentas de correo se administren y protejan con una directiva de Intune MDM.

## <a name="tiers-of-security-and-protection"></a>Niveles de seguridad y protección

La mayoría de las organizaciones tienen requisitos específicos en lo relativo a la protección y seguridad de los datos. Estos requisitos varían por segmento industrial y por las funciones de trabajo dentro de las organizaciones. Por ejemplo, puede que el departamento legal y los administradores de Office 365 necesiten seguridad adicional y controles de protección de la información en torno a su correspondencia de correo electrónico que no necesitan los usuarios de otras unidades de negocio. 

Cada sector tiene también su propio conjunto de normas especializadas. En lugar de proporcionar una lista de todas las opciones de seguridad posibles o una recomendación por segmento industrial o función de trabajo, en este artículo se proporcionan recomendaciones para tres niveles diferentes de seguridad y protección para el correo electrónico que se pueden aplicar en función de la granularidad de sus necesidades: [línea base, confidencial y muy regulado](https://go.microsoft.com/fwlink/p/?linkid=841656).  

**Línea base** Se recomienda que establezca un estándar mínimo para la protección de los datos, así como las identidades y los dispositivos que acceden a los datos. Se pueden seguir las recomendaciones de línea base para proporcionar una fuerte protección predeterminada que satisfaga las necesidades de muchas organizaciones. 

**Confidencial** Algunos clientes tienen un subconjunto de datos que se deben proteger en niveles superiores o que necesitan que todos los datos se protejan en estos niveles más altos. Puede aplicar una mayor protección a algunos o todos los conjuntos de datos de su entorno de Office 365. Se recomienda proteger las identidades y los dispositivos que acceden a la información confidencial con niveles de seguridad comparables. 

**Muy regulado**. Algunas organizaciones pueden tener una cantidad muy pequeña de datos de alta confidencialidad, secretos comerciales o datos regulados. Microsoft ofrece funcionalidades para ayudar a las organizaciones a satisfacer estos requisitos, como la protección agregada para identidades y dispositivos. 

### <a name="default-protection-mechanism-recommendations"></a>Recomendaciones de mecanismos de protección predeterminados


La tabla siguiente contiene recomendaciones de mecanismos de protección predeterminados para cada uno de los niveles de protección y seguridad definidos previamente:

|Mecanismo de protección|Línea base|Sensible|Muy regulado|
|:-------------------|:-------|:--------|:---------------|
|**Exigir MFA**|Con riesgo de inicio de sesión medio o superior|Con riesgo de inicio de sesión bajo o superior|En todas las nuevas sesiones|
|**Exigir cambio de contraseña**|Para usuarios de alto riesgo|Para usuarios de alto riesgo|Para usuarios de alto riesgo|
|**Exigir Intune App Protection**|Sí|Sí|Sí|
|**Exigir la inscripción en Intune (COD)**|Exigir un dispositivo compatible o unido a un domino.|Exigir un dispositivo compatible o unido a un domino.|Exigir un dispositivo compatible o unido a un domino.|

### <a name="device-ownership"></a>Propiedad del dispositivo
En la tabla anterior se refleja la tendencia de muchas organizaciones a admitir una mezcla de dispositivos de propiedad corporativa (COD), así como dispositivos personales o que aporta el propio usuario (BYOD) para permitir la productividad móvil entre los empleados. Las directivas de Intune App Protection garantizan que el correo electrónico está protegido frente a extrusiones de datos de la aplicación móvil de Outlook y de otras aplicaciones móviles de Office, en COD y BYOD.  

Se exige que los dispositivos de propiedad corporativa sean administrados por Intune o que se unan a un dominio para aplicar protecciones y control adicionales.  Según la confidencialidad de los datos, la organización podría elegir no permitir BYOD en determinadas poblaciones de usuarios o aplicaciones específicas.

## <a name="baseline"></a>Línea base 
En esta sección se describen las recomendaciones de protección del correo electrónico en el nivel de línea base de protección de datos, identidades y dispositivos. Estas recomendaciones deberían satisfacer las necesidades de protección predeterminadas de muchas organizaciones.
>[!NOTE]
>Las directivas siguientes son sumativas y se complementan entre sí. En cada sección se describen solo las adiciones aplicadas a cada nivel.
>

### <a name="conditional-access-policy-settings"></a>Configuración de directivas de acceso condicional

#### <a name="identity-protection"></a>Protección de identidad 
Puede proporcionar a los usuarios una experiencia de inicio de sesión único (SSO), como se ha descrito en secciones anteriores. Solo deberá intervenir cuando sea necesario en función de los [eventos de riesgo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events).  

* Exigir MFA según riesgo de inicio de sesión medio o superior
* Exigir el cambio a una contraseña segura para usuarios de alto riesgo

>[!IMPORTANT]
>Las recomendaciones de esta directiva son la [sincronización de contraseñas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) y el [autoservicio de restablecimiento de contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prevención de la pérdida de datos 
El objetivo de las directivas de administración de dispositivos y aplicaciones es proteger frente a la pérdida de datos en caso de robo o pérdida de un dispositivo. Una forma de hacer esto es asegurarse de que el acceso a los datos esté protegido por un PIN, de que los datos estén cifrados en el dispositivo y de que el dispositivo no esté en peligro.

|Recomendación de directiva|Descripción|
|:--------------------|:----------|
|**Exigir la administración del equipo del usuario**|Exigir que los usuarios unan sus equipos a un dominio de Active Directory o inscribir sus equipos en la administración con Intune o Configuration Manager|
|**Aplicar la configuración de seguridad mediante objetos de directiva de grupo (GPO) o directivas de Configuration Manager para equipos unidos a un dominio**|Implementar directivas que configuren los equipos administrados para habilitar BitLocker, un antivirus y un firewall|
|**Exigir la administración de dispositivos móviles**|Exigir que los dispositivos de usuarios usados para acceder al correo electrónico se administren mediante Intune o que el acceso al correo electrónico de la empresa se realice únicamente a través de directivas de Intune App Protection, como Outlook Mobile|
|**Aplicar una directiva de cumplimiento de dispositivo de Intune en dispositivos administrados**|Aplicar una directiva de cumplimiento de dispositivos de Intune para dispositivos móviles corporativos administrados y equipos administrados por Intune que exija: un PIN con una longitud mínima de 6 caracteres, cifrado del dispositivo, un dispositivo en buen estado (sin Jailbreak o rooting; que pase la atestación de estado) y, si está disponible, exigir dispositivos con bajo riesgo, según determine un MTP de terceros, como Lookout o SkyCure|
|**Aplicar una directiva de Intune App Protection para aplicaciones administradas que se ejecutan en dispositivos no administrados**|Aplicar una directiva de Intune App Protection para aplicaciones administradas que se ejecutan en dispositivos móviles personales no administrados para exigir: un PIN con longitud mínima de 6 caracteres, el cifrado del dispositivo y que el dispositivo esté en buen estado (sin Jailbreak o rooting; que haya pasado la atestación de estado)|

### <a name="user-impact"></a>Impacto en el usuario

Para la mayoría de las organizaciones, es importante poder establecer las expectativas de los usuarios en torno a cuándo y en qué condiciones se espera que inicien sesión en Office 365 para acceder a su correo electrónico.  

Los usuarios suelen beneficiarán del inicio de sesión único (SSO), excepto en las situaciones siguientes: 
* Cuando se solicitan tokens de autenticación para Exchange Online:
  * Es posible que a los usuarios se les pida MFA cada vez que se detecta un riesgo de inicio de sesión medio o superior y los usuarios no han realizado aún MFA en sus sesiones actuales.  
  * Se exigirá que los usuarios usen aplicaciones de correo electrónico que admitan el SDK de Intune App Protection o que accedan al correo electrónico desde dispositivos compatibles con Intune o unidos a un dominio de AD. 
* Cuando los usuarios se encuentran en riesgo de inicio de sesión, y han realizado correctamente MFA, se les pedirá que cambien sus contraseñas.

## <a name="sensitive"></a>Sensible

En esta sección se describen las recomendaciones de protección del correo electrónico en el nivel confidencial de protección de datos, identidades y dispositivos. Estas recomendaciones son para clientes que tienen un subconjunto de datos que se deben proteger en niveles superiores o que necesitan que todos los datos se protejan en estos niveles más altos. 

Puede aplicar una mayor protección a algunos o todos los conjuntos de datos de su entorno de Office 365. Por ejemplo, puede aplicar directivas para asegurarse de que la información confidencial solo se compartan entre aplicaciones protegidas para evitar la pérdida de datos. Se recomienda proteger las identidades y los dispositivos que acceden a la información confidencial con niveles de seguridad comparables. 

### <a name="conditional-access-policy-settings"></a>Configuración de directivas de acceso condicional
#### <a name="identity-protection"></a>Protección de identidad 

Puede proporcionar a los usuarios una experiencia de inicio de sesión único (SSO), como se ha descrito en secciones anteriores. Solo deberá intervenir cuando sea necesario en función de los [eventos de riesgo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events).   

* Exigir MFA en sesiones de bajo riesgo o superior
 * Exigir el cambio a una contraseña segura para usuarios de alto riesgo

>[!IMPORTANT]
>Las recomendaciones de esta directiva son la [sincronización de contraseñas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) y el [autoservicio de restablecimiento de contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prevención de la pérdida de datos 

El objetivo de estas directivas de administración de dispositivos y aplicaciones es proteger frente a la pérdida de datos en caso de robo o pérdida de un dispositivo. Una forma de hacer esto es asegurarse de que el acceso a los datos esté protegido por un PIN, de que los datos estén cifrados en el dispositivo y de que el dispositivo no esté en peligro.

|Recomendación de directiva|Descripción|
|:--------------------|:----------|
|**Exigir la administración del equipo del usuario**|Exigir que los usuarios unan sus equipos a un dominio de Active Directory o inscribir sus equipos en la administración con Intune o Configuration Manager y asegurarse de que esos dispositivos cumplan las directivas antes de permitirles el acceso al correo electrónico|
|**Aplicar la configuración de seguridad mediante objetos de directiva de grupo (GPO) o directivas de Configuration Manager para equipos unidos a un dominio**|Implementar directivas que configuren los equipos administrados para habilitar BitLocker, un antivirus y un firewall|
|**Exigir la administración de dispositivos móviles**|Exigir que los dispositivos de usuarios usados para acceder al correo electrónico se administren mediante Intune o que el acceso al correo electrónico de la empresa se realice únicamente a través de directivas de Intune App Protection, como Outlook Mobile|
|**Aplicar una directiva de cumplimiento de dispositivo de Intune en dispositivos administrados**|Aplicar una directiva de cumplimiento de dispositivos de Intune para dispositivos móviles corporativos administrados y equipos administrados por Intune que exija: un PIN con una longitud mínima de 6 caracteres, cifrado del dispositivo, un dispositivo en buen estado (sin Jailbreak o rooting; que pase la atestación de estado) y, si está disponible, exigir dispositivos con bajo riesgo, según determine un MTP de terceros, como Lookout o SkyCure|
|**Aplicar una directiva de Intune App Protection para aplicaciones administradas que se ejecutan en dispositivos no administrados**|Aplicar una directiva de Intune App Protection para aplicaciones administradas que se ejecutan en dispositivos móviles personales no administrados para exigir: un PIN con longitud mínima de 6 caracteres, el cifrado del dispositivo y que el dispositivo esté en buen estado (sin Jailbreak o rooting; que haya pasado la atestación de estado)|

### <a name="user-impact"></a>Impacto en el usuario

Para la mayoría de las organizaciones, es importante poder establecer las expectativas de los usuarios en torno a cuándo y en qué condiciones se espera que inicien sesión en el correo electrónico de Office 365. 

Los usuarios suelen beneficiarán del inicio de sesión único (SSO), excepto en las situaciones siguientes: 
* Cuando se solicitan tokens de autenticación para Exchange Online:
  * A los usuarios se les pedirá MFA cada vez que se detecte un riesgo de inicio de sesión medio o superior y los usuarios no hayan realizado aún MFA en sus sesiones actuales.  
  * Se exigirá que los usuarios usen aplicaciones de correo electrónico que admitan el SDK de Intune App Protection o que accedan al correo electrónico desde dispositivos compatibles con Intune o unidos a un dominio de AD. 
* Cuando los usuarios se encuentran en riesgo de inicio de sesión, y han realizado correctamente MFA, se les pedirá que cambien sus contraseñas.

## <a name="highly-regulated"></a>Muy regulado
En esta sección se describen las recomendaciones de protección del correo electrónico en el nivel muy regulado de protección de datos, identidades y dispositivos. Estas recomendaciones son para clientes que pueden tener una cantidad muy pequeña de datos de alta confidencialidad, secretos comerciales o datos regulados. Microsoft ofrece funcionalidades para ayudar a las organizaciones a satisfacer estos requisitos, como la protección agregada para identidades y dispositivos. 

### <a name="conditional-access-policy-settings"></a>Configuración de directivas de acceso condicional
#### <a name="identity-protection"></a>Protección de identidad 

En el nivel muy regulado, Microsoft recomienda exigir MFA en todas las sesiones nuevas.
* Exigir MFA en todas las sesiones nuevas
* Exigir el cambio a una contraseña segura para usuarios de alto riesgo

>[!IMPORTANT]
>Las recomendaciones de esta directiva son la [sincronización de contraseñas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) y el [autoservicio de restablecimiento de contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-passwords).
>

#### <a name="data-loss-prevention"></a>Prevención contra la pérdida de datos
El objetivo de estas directivas de administración de dispositivos y aplicaciones es proteger frente a la pérdida de datos en caso de robo o pérdida de un dispositivo. La forma de hacer esto es asegurarse de que el acceso a los datos esté protegido por un PIN, de que los datos estén cifrados en el dispositivo y de que el dispositivo no esté en peligro.

En el nivel muy regulado, se recomienda exigir aplicaciones que admitan la ejecución de directivas de Intune App Protection solo en dispositivos compatibles con Intune o unidos a un dominio.

|Recomendación de directiva|Descripción|
|:--------------------|:----------|
|**Exigir la administración del equipo del usuario**|Exigir que los usuarios unan sus equipos a un dominio de Active Directory o inscribir sus equipos en la administración con Intune o Configuration Manager y asegurarse de que esos dispositivos cumplan las directivas antes de permitirles el acceso al correo electrónico|
|**Aplicar la configuración de seguridad mediante objetos de directiva de grupo (GPO) o directivas de Configuration Manager para equipos unidos a un dominio**|Implementar directivas que configuren los equipos administrados para habilitar BitLocker, un antivirus y un firewall|
|**Exigir la administración de dispositivos móviles**|Exigir que los dispositivos de usuarios usados para acceder al correo electrónico se administren mediante Intune o que el acceso al correo electrónico de la empresa se realice únicamente a través de directivas de Intune App Protection, como Outlook Mobile|
|**Aplicar una directiva de cumplimiento de dispositivo de Intune en dispositivos administrados**|Aplicar una directiva de cumplimiento de dispositivos de Intune para dispositivos móviles corporativos administrados y equipos administrados por Intune que exija: un PIN con una longitud mínima de 6 caracteres, cifrado del dispositivo, un dispositivo en buen estado (sin Jailbreak o rooting; que pase la atestación de estado) y, si está disponible, exigir dispositivos con bajo riesgo, según determine un MTP de terceros, como Lookout o SkyCure|

### <a name="user-impact"></a>Impacto en el usuario
Para la mayoría de las organizaciones, es importante poder establecer las expectativas de los usuarios en torno a cuándo y en qué condiciones se espera que inicien sesión en el correo electrónico de Office 365. 

* La duración máxima de una sesión de inicio de sesión único es de 1 día. Los usuarios deberán volver a autenticarse con MFA después de que expiren las sesiones.
* Cuando los usuarios se encuentran en riesgo de inicio de sesión, y han completado MFA, se les pedirá que cambien sus contraseñas.
* Cuando se solicitan tokens de autenticación para Exchange Online:
  * Se pedirá a los usuarios que realicen MFA cada vez que inicien una nueva sesión.  
  * Se exigirá a los usuarios que usen aplicaciones de correo electrónico compatibles con el SDK de Intune App Protection
  * Se exigirá a los usuarios que accedan al correo electrónico desde dispositivos compatibles con Intune o unidos a un dominio de AD. 
 
 ## <a name="next-steps"></a>Pasos siguientes
 [Implementación de directivas recomendadas](secure-email-deploy-recommended-policies.md)

