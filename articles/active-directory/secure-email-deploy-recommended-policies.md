---
title: "Implementación de directivas recomendadas para correo seguro de Azure AD | Microsoft Docs"
description: "Describe la implementación de las recomendaciones de Microsoft sobre cómo aplicar configuraciones y directivas de correo electrónico."
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 08f72f1ba219946486a0fc62ea74cb338f5a85b6
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---

# <a name="deploy-recommended-policies"></a>Implementación de las directivas recomendadas

En esta sección se describe cómo implementar las directivas recomendadas en un entorno recién aprovisionado. Configurar estas directivas en un entorno de laboratorio independiente permite comprender y evaluar las directivas recomendadas antes de implementarlas en fases en los entornos de preproducción y de producción. El entorno recién aprovisionado puede estar solo en la nube o puede ser híbrido.  

Para implementar correctamente las directivas recomendadas, debe realizar acciones en Azure Portal para satisfacer los requisitos previos indicados anteriormente. En concreto, debe:
* Configurar redes con nombre para asegurar que Azure Identity Protection puede generar una puntuación de riesgo
* Exigir que todos los usuarios se registren en la autenticación multifactor (MFA)
* Configurar la sincronización de contraseñas y el restablecimiento de contraseñas de autoservicio para permitir que los usuarios puedan restablecer por sí mismos las contraseñas

Puede destinar las directivas de Azure AD y de Intune a grupos de usuarios específicos. Se recomienda implementar en fases las directivas definidas anteriormente. De este modo, puede validar el rendimiento de las directivas y los equipos de soporte técnico en relación con la directiva de manera incremental.

## <a name="baseline-ca-policy"></a>Directiva de CA de base

Para crear una directiva de acceso condicional, inicie sesión en Microsoft Azure Portal con sus credenciales de administrador. Luego vaya a **Azure Active Directory > Seguridad > Acceso condicional**. 

Puede agregar una directiva nueva (+Agregar) como se muestra en la captura de pantalla siguiente:

![Directiva de CA de base](./media/secure-email/baseline-ca-policy.png)

En las tablas siguientes se describe la configuración adecuada que se necesita para expresar las directivas requeridas para cada nivel de protección.

### <a name="medium-and-above-risk-requires-mfa"></a>El riesgo medio y superior requiere MFA

En la tabla siguiente se describe la configuración de directiva de acceso condicional que se debe implementar para esta directiva.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Asignaciones**|Usuarios y grupos|Include|Seleccionar usuarios y grupos: seleccione un grupo de seguridad específico que incluya los usuarios de destino|Comience con el grupo de seguridad que incluye usuarios piloto.|
|||Exclude|Grupo de seguridad de excepción, cuentas de servicio (identidades de aplicación)|Pertenencia modificada según necesidad temporal|
||Aplicaciones de nube|Include|Seleccionar aplicaciones: seleccione Office 365 Exchange Online||
||Condiciones|Configurado|Sí|Configuración específica para su entorno y sus necesidades|
||Riesgo de inicio de sesión|Nivel de riesgo|Alto, medio|Comprobar ambos|
|**Controles de acceso**|Conceder|Conceder acceso|True|Seleccionado|
|||Requerir MFA|True|Comprobar|
|||Requerir dispositivos compatibles|False||
|||Requeridos dispositivos unidos a un dominio|False||
|||Requerir todos los controles seleccionados|True|Seleccionado|
|**Habilitar directiva**|||Por|Implementa la directiva de acceso condicional|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exigir un dispositivo compatible o unido a un domino.

Para crear una directiva de acceso condicional de Intune para Exchange Online, inicie sesión en el [Portal de administración de Microsoft (http://manage.microsoft.com)](http://manage.microsoft.com/) con sus credenciales de administrador y, luego, vaya a **Directiva > Acceso condicional > Directiva de Exchange Online**.

![Directiva de Exchange Online](./media/secure-email/exchange-online-policy.png)

Debe establecer una directiva de acceso condicional específicamente para Exchange Online en el portal de administración de Intune para exigir un dispositivo compatible o un dispositivo unido a un dominio.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Acceso a las aplicaciones**|Outlook y otras aplicaciones que usan la autenticación moderna de usuario|Todas las plataformas|True|Seleccionado|
|||Windows debe cumplir los siguientes requisitos|El dispositivo debe estar unido a un dominio o ser compatible|Seleccionado (lista)|
|||Plataforma seleccionada|False||
||Outlook Web Access (OWA)|Bloquear dispositivos no compatibles en la misma plataforma de Outlook|True|Comprobar|
||Aplicaciones de Exchange ActiveSync que usan la autenticación básica|Bloquear dispositivos no compatibles en plataformas admitidas por Microsoft Intune|True|Comprobar|
|||Bloquear todos los demás dispositivos en plataformas no admitidas por Microsoft Intune|True|Comprobar|
|**Implementación de directivas**|Grupos de destino|Seleccione los grupos de Active Directory a los que se dirige esta directiva|||
|||Todos los usuarios|False||
|||Grupos de seguridad seleccionados|True|Seleccionado|
|||Modificar|Seleccionar un grupo de seguridad específico que incluya los usuarios de destino||
||Grupos exentos|Seleccionar grupos de Active Directory que quedarán exentos de esta directiva (reemplaza a los miembros de la lista de grupos de destino)|||
|||Sin usuarios exentos|True|Seleccionado|
|||Grupos de seguridad seleccionados|False|||

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Acceso condicional de administración de aplicaciones móviles para Exchange Online

Debe establecer una directiva de acceso condicional específicamente para Exchange Online en el portal de administración de Intune para administrar las aplicaciones móviles.

Para administrar aplicaciones móviles, inicie sesión en Microsoft Azure Portal con sus credenciales de administrador y, luego, vaya a **Intune App Protection > Configuración > Acceso condicional > Exchange Online**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Acceso a las aplicaciones**|Aplicaciones permitidas|Habilitar acceso a las aplicaciones|Permitir aplicaciones que admiten directivas de aplicaciones de Intune|Seleccionado (lista): da como resultado una lista de combinaciones de plataformas o aplicaciones compatibles con las directivas de aplicaciones de Intune|
|**Acceso de usuarios**|Aplicaciones permitidas|Grupos de usuarios restringidos|Agregar grupos de usuarios: seleccione un grupo de seguridad específico que incluya los usuarios de destino|Comience con el grupo de seguridad que incluye usuarios piloto|
|||Grupos de usuarios exentos|Grupos de seguridad de excepciones|||

#### <a name="apply-to"></a>Aplicar a

Una vez que se complete el proyecto piloto, estas directivas se deben aplicar a todos los usuarios de la organización.

## <a name="sensitive-ca-policy"></a>Directiva de CA confidencial

### <a name="low-and-above-risk-requires-mfa"></a>El riesgo bajo y superior requiere MFA
En la tabla siguiente se describe la configuración de directiva de acceso condicional que se debe implementar para las directivas de riesgo bajo y superior.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Asignaciones**|Usuarios y grupos|Include|Seleccionar usuarios y grupos: seleccione un grupo de seguridad específico que incluya los usuarios de destino|Comience con el grupo de seguridad que incluye usuarios piloto|
|||Exclude|Grupo de seguridad de excepción, cuentas de servicio (identidades de aplicación)|Pertenencia modificada según necesidad temporal|
||Aplicaciones de nube|Include|Seleccionar aplicaciones: seleccione Office 365 Exchange Online||
||Condiciones|Configurado|Sí|Configuración específica para su entorno y sus necesidades|
||Riesgo de inicio de sesión|Configurado|Sí|Configuración específica para su entorno y sus necesidades|
|||Nivel de riesgo|Bajo, medio, alto|Comprobar los tres|
|**Controles de acceso**|Conceder|Conceder acceso|True|Seleccionado|
|||Requerir MFA|True|Comprobar|
|||Requerir dispositivos compatibles|False||
|||Requerir dispositivo unido a un dominio|False||
|||Requerir todos los controles seleccionados|True|Seleccionado|
|**Habilitar directiva**|||Por|Implementa la directiva de acceso condicional|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exigir un dispositivo compatible o unido a un domino.
(Consultas las instrucciones de base)

### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Acceso condicional de administración de aplicaciones móviles para Exchange Online

(Consultas las instrucciones de base)

#### <a name="apply-to"></a>Aplicar a

Una vez que se complete el proyecto piloto, estas directivas se deben aplicar a los usuarios de la organización que requieran acceso a correo electrónico considerado como confidencial.

## <a name="highly-regulated-ca-policy"></a>Directiva de CA exhaustivamente regulada
### <a name="mfa-required"></a>Se requiere MFA

En la tabla siguiente se describe la configuración de directiva de acceso condicional que se debe implementar para la directiva exhaustivamente regulada.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Asignaciones**|Usuarios y grupos|Include|Seleccionar usuarios y grupos: seleccione un grupo de seguridad específico que incluya los usuarios de destino|Comience con el grupo de seguridad que incluye usuarios piloto|
|||Exclude|Grupo de seguridad de excepción, cuentas de servicio (identidades de aplicación)|Pertenencia modificada según necesidad temporal|
||Aplicaciones de nube|Include|Seleccionar aplicaciones: seleccione Office 365 Exchange Online||
|**Controles de acceso**|Conceder|Conceder acceso|True|Seleccionado|
|||Requerir MFA|True|Comprobar|
|||Requerir dispositivos compatibles|False|Comprobar|
|||Requerir dispositivo unido a un dominio|False||
|||Requerir todos los controles seleccionados|True|Seleccionado|
|**Habilitar directiva**|||Por|Implementa la directiva de acceso condicional|

### <a name="require-a-compliant-or-domain-joined-device"></a>Exigir un dispositivo compatible o unido a un domino.
(Consultas las instrucciones de base)
### <a name="mobile-application-management-conditional-access-for-exchange-online"></a>Acceso condicional de administración de aplicaciones móviles para Exchange Online
(Consultas las instrucciones de base)
#### <a name="apply-to"></a>Aplicar a
Una vez que se complete el proyecto piloto, estas directivas se deben aplicar a los usuarios de la organización que requieran acceso a correo electrónico considerado como exhaustivamente regulado.

## <a name="user-risk-policy"></a>Directiva de riesgo de usuario
### <a name="high-risk-users-must-change-password"></a>Los usuarios de alto riesgo deben cambiar la contraseña
Para asegurarse de que todas las cuentas en peligro de usuarios de alto riesgo deban cambiar la contraseña cuando inicien sesión, debe aplicar la directiva siguiente. 

Inicie sesión en [Microsoft Azure Portal (http://portal.azure.com)](http://portal.azure.com/) con sus credenciales de administración y, luego, vaya a **Azure AD Identity Protection > Directiva de riesgo de usuario**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Asignaciones**|Usuarios|Include|Todos los usuarios|Seleccionado|
|||Exclude|None||
||Condiciones|Riesgo de usuario|Alto|Seleccionado|
|**Controles**|Access|Permitir acceso|True|Seleccionado|
||Access|Requerir cambio de contraseña|True|Comprobar|
|**Revisión**|N/D|N/D|N/D|N/D|
|**Aplicar directiva**|||Por|Inicia aplicación de directiva|

## <a name="additional-configurations"></a>Configuraciones adicionales
Además de las directivas mencionadas, debe establecer la siguiente configuración de administración de dispositivos y aplicaciones móviles que se describe en esta sección. 

### <a name="intune-mobile-application-management"></a>Administración de aplicaciones móviles de Intune 

Para garantizar que el correo electrónico está protegido con las recomendaciones de directiva anteriormente mencionadas para cada nivel de protección de seguridad y datos, debe crear directivas de Intune App Protection en Azure Portal.

Para crear una nueva directiva de protección de aplicaciones, inicie sesión en Microsoft Azure Portal con las credenciales de administrador y, luego, vaya a **Intune App Protection > Configuración > Directiva de aplicación**.

Agregue una directiva nueva (+Agregar) como se muestra en la captura de pantalla siguiente:

![Administración de aplicaciones móviles de Intune](./media/secure-email/intune-mobile-app-mgmt.png)

>[!NOTE]
>Hay ligeras diferencias en las opciones de directivas de protección de aplicaciones entre iOS y Android. La directiva siguiente es específica para Android.
>

En las tablas siguientes se describe detalladamente la configuración adecuada que se necesita para expresar las directivas requeridas para cada nivel de protección.
| En la tabla siguiente se describe la configuración de directiva de Intune App Protection recomendada.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**General**|Email|Nombre|Directiva de correo electrónico seguro para Android|Escribir un nombre de directiva|
|||Descripción||Escribir texto que describe la directiva|
|||Plataforma|Android|Hay ligeras diferencias en las opciones de directivas de protección de aplicaciones entre iOS y Android. Esta directiva es específica para Android|
|**Aplicaciones**|Aplicaciones|Aplicaciones|Outlook|Seleccionado (lista)|
|**Configuración**|Reubicación de datos|Evitar copia de seguridad de Android|Sí|En iOS, llamará específicamente a iTunes y iCloud|
||||Permitir a la aplicación transferir datos a otras aplicaciones|Aplicaciones administradas por directivas||
|||Permitir a la aplicación recibir datos a otras aplicaciones|Aplicaciones administradas por directivas||
|||Impedir "Guardar como"|Sí||
|||Restringir cortar, copiar y pegar con otras aplicaciones|Aplicaciones administradas por directivas||
|||Restringir contenido web para mostrar en Managed Browser|No||
|||Cifrar datos de aplicación|Sí|En iOS, seleccionar la opción: Cuando el dispositivo esté bloqueado|
|||Deshabilitar sincronización de contactos|No||
||Access|Requerir PIN para acceder|Sí||
|||Número de intentos antes del restablecimiento del PIN|3||
|||Permitir PIN sencillo|No||
|||Longitud del PIN|6||
|||Permitir huella digital en lugar de PIN|Sí||
|||Requerir credenciales corporativas para el acceso|No||
|||Bloquear las aplicaciones administradas para que no se ejecuten en dispositivos con jailbreak o rooting|Sí||
|||Volver a comprar el requisito de acceso después de (minutos)|30||
|||Período de gracia sin conexión|720||
|||Intervalo sin conexión (días) antes de que se borren los datos de la aplicación|90||
|||Bloquear captura de pantalla y asistente de Android|No|En iOS, no es una opción disponible|

Cuando haya finalizado, recuerde hacer clic en "Crear". Repita los pasos anteriores y reemplace la plataforma seleccionada (desplegable) con iOS. Esto crea dos directivas de aplicación, para que una vez que cree la directiva, asigne grupos a la directiva y la implemente.

### <a name="intune-mobile-device-management"></a>Administración de dispositivos móviles de Intune
Para crear las siguientes directivas de cumplimiento y configuración, inicie sesión en el [Portal de administración de Microsoft (http://manage.microsoft.com)](https://manage.microsoft.com/) con sus credenciales de administración.

#### <a name="ios-email-profile"></a>Perfil de correo electrónico de iOS
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directivas de configuración > Agregar > iOS > Perfil de correo electrónico (iOS 8 y posterior)**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Perfil de correo electrónico**|Exchange Active Sync|Host (#)|Outlook.office365.com||
|||Nombre de cuenta (#)|SecureEmailAccount|Elección del administrador|
|||Nombre de usuario|Nombre principal de usuario|Seleccionado: lista desplegable|
|||Dirección de correo electrónico|Dirección SMTP principal|Seleccionado: lista desplegable|
|||Método de autenticación|Nombre de usuario y contraseña|Seleccionado: lista desplegable|
|||Usar S/MIME|False||
||Configuración de sincronización|Número de días para sincronizar el correo electrónico|Dos semanas|Seleccionado: lista desplegable|
|||Usar SSL|True|Comprobar|
|||Permitir mover mensajes a otras cuentas de correo electrónico|False||
|||Permitir enviar correo electrónico desde aplicaciones de terceros|True||
|||Sincronizar direcciones de correo electrónico usadas recientemente|True|Comprobar|

#### <a name="ios-app-sharing-profile"></a>Perfil de uso compartido de aplicaciones iOS
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directivas de configuración > Agregar > iOS > Configuración general (iOS 8.0 y posterior)**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Seguridad**|Todo|Todo|Sin configurar||
|**Nube**|Todo|Todo|Sin configurar||
|**Aplicaciones**|Browser|Todo|Sin configurar||
||Aplicaciones|Permitir instalación de aplicaciones|Sin configurar||
|||Requerir una contraseña para acceder a la tienda de aplicaciones|Sin configurar||
|||En todas las compras en la aplicación|Sin configurar||
|||Permitir documentos administrados en otras aplicaciones administradas (iOS 8.0 y posterior)|No|Seleccionado: lista desplegable|
|||Permitir documentos no administrados en otras aplicaciones administradas|Sin configurar||
|||Permitir videoconferencia|Sin configurar||
|||Permitir que el usuario confíe en nuevos creadores de aplicaciones empresariales|Sin configurar||
||Juegos|Todo|Sin configurar||
||Contenido multimedia|Todo|Sin configurar|||

#### <a name="android-email-profile"></a>Perfil de correo electrónico de Android
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directivas de configuración > Agregar > iOS > Perfil de correo electrónico (Samsung KNOX Standard 4.0 y posterior)**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Perfil de correo electrónico**|Exchange Active Sync|Host (#)| Outlook.office365.com|
|||Nombre de cuenta (#)|SecureEmailAccount|Elección del administrador|
|||Nombre de usuario|Nombre principal de usuario|Seleccionado: lista desplegable|
|||Dirección de correo electrónico|Dirección SMTP principal|Seleccionado: lista desplegable|
|||Método de autenticación|Nombre de usuario y contraseña|Seleccionado: lista desplegable|
|||Usar S/MIME|False||
||Configuración de sincronización|Número de días para sincronizar el correo electrónico|Dos semanas|Seleccionado: lista desplegable|
|||Programación de sincronización|Sin configurar|Seleccionado: lista desplegable|
|||Usar SSL|True|Comprobar|
|||Tipo de contenido para sincronizar|||
|||Email|True|Comprobar (bloqueado)|
|||Contactos|True|Comprobar|
|||Calendario|True|Comprobar|
|||Tareas|True|Comprobar|
|||Notas|True|Comprobar|

#### <a name="android-for-work-email-profile"></a>Perfil de correo electrónico de Android for Work
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directivas de configuración > Agregar > iOS > Perfil de correo electrónico (Android for Work, Gmail)**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Perfil de correo electrónico**|Exchange Active Sync|Host(#)| Outlook.office365.com|
|||Nombre de cuenta(#)|SecureEmailAccount|Elección del administrador|
|||Nombre de usuario|Nombre principal de usuario|Seleccionado: lista desplegable|
|||Dirección de correo electrónico|Dirección SMTP principal|Seleccionado: lista desplegable|
|||Método de autenticación|Nombre de usuario y contraseña|Seleccionado: lista desplegable|
||Configuración de sincronización|Número de días para sincronizar el correo electrónico|Dos semanas|Seleccionado: lista desplegable|
|||Usar SSL|True|Comprobar|

#### <a name="android-for-work-app-sharing-profile"></a>Perfil de uso compartido de aplicaciones de Android for Work
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directivas de configuración > Agregar > iOS > Configuración general (Android for Work)**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Seguridad**|Password|Longitud mínima de contraseña|Sin configurar||
|||Número de errores de inicio de sesión repetidos antes de quitar el perfil de trabajo|Sin configurar||
|||Minutos de inactividad antes de que se bloquee el dispositivo|Sin configurar||
|||Expiración de contraseñas (días)|Sin configurar||
|||Recordar historial de contraseñas|Sin configurar||
|||Requerir contraseña para desbloquear el dispositivo móvil|Sin configurar||
|||Permitir desbloqueo por huella digital (Android 6.0+)|Sin configurar||
|||Permitir Smart Lock y otros agentes de confianza (Android 6.0+)|Sin configurar||
||Configuración de perfil de trabajo|Permitir uso compartido de datos entre perfiles de trabajo y personales|Las aplicaciones en el perfil de trabajo pueden administrar solicitudes de uso compartido del perfil personal|Seleccionado: lista desplegable|
|||Ocultar notificaciones de perfil de trabajo cuando el dispositivo esté bloqueado (Android 6.0+)|Sin configurar||
|||Establecer directiva de permisos de aplicación predeterminada (Android 6.0+)|Sin configurar|||

#### <a name="device-compliance-policy"></a>Directiva de cumplimiento de dispositivos
En el [portal de administración de Intune (https://manage.microsoft.com)](https://manage.microsoft.com/), cree las siguientes directivas de configuración en **Directiva > Directiva de cumplimiento > Agregar**.

|Categorías|Tipo|Propiedades|Valores|Notas|
|:---------|:---|:---------|:-----|:----|
|**Seguridad del sistema**|Password|Requerir contraseña para desbloquear dispositivos móviles (...)|Sí|Seleccionado: lista desplegable|
|||Permitir contraseñas sencillas (…)|No|Seleccionado: lista desplegable|
|||Longitud mínima de contraseña (…)|6|Seleccionado: lista|
||Configuración de contraseña avanzada|Todo|Sin configurar||
||Cifrado|Requerir cifrado en dispositivo móvil (...)|Sí|Seleccionado: lista desplegable|
||Perfiles de correo electrónico|Intune debe administrar la cuenta de correo electrónico (iOS 8.0+)|Sí| Seleccionado: lista desplegable|
|||Selección (#)||Se debe seleccionar Directiva de configuración de correo electrónico para iOS: directiva de correo electrónico de iOS (consulte las directivas de configuración mencionadas anteriormente)|
|**Estado de dispositivos**|Atestación de estado de dispositivo Windows|Requerir que los dispositivos se informen como correctos (Windows 10 Desktop y Windows 10 Mobile y posterior)|Sí||
||Configuración de seguridad de dispositivo|Todo|Sin configurar||
||Protección contra amenazas de dispositivo|Todo|Sin configurar||
||Jailbreak|El dispositivo no debe tener jailbreak ni rooting (iOS 8.0+, Android 4.0+)|Sí||
|**Propiedades del dispositivo**|Versión del sistema operativo|Todo|Sin configurar|||

Para considerar implementadas todas las directivas anteriores, deben estar dirigidas a grupos de usuarios. Para hacerlo, cree la directiva (o guárdela) o más adelante seleccione Administrar implementación en la sección Directiva (en el mismo nivel que la opción Agregar).

## <a name="remediating-events-that-have-results-in-medium-or-high-risk-access"></a>Corrección de eventos que tienen resultados en acceso de riesgo medio o alto
Si un usuario informa que ahora debe realizar MFA cuando anteriormente no era necesario hacerlo, el soporte técnico puede revisar su estado desde una perspectiva de riesgo.  

Los usuarios de la organización que tengan un rol de Administrador global o Administrador de seguridad pueden usar Azure AD Identity Protection para revisar los eventos de riesgo que contribuyeron a la puntuación de riesgo calculado. Si se identifican algunos eventos marcados como sospechosos pero que se confirman como válidos (como un inicio de sesión desde una ubicación desconocida cuando hay un empleado de vacaciones), el administrador puede solucionar el evento para que se le deje de considerar en la puntuación de riesgo.

## <a name="next-steps"></a>Pasos siguientes
[Descripciones de los servicios de EMS y Office 365](secure-email-ems-office365-service-descriptions.md)

