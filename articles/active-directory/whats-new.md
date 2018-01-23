---
title: "Novedades Notas de la versión de Azure Active | Microsoft Docs"
description: "Conozca las novedades de Azure Active Directory (Azure AD), incluidas las notas de versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6e1cf6e2ee717ef7629e1388d7bca2090eed46fa
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?




> Para estar al día de las novedades de Azure Active Directory, suscríbase a esta [![fuente](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Estamos mejorando Azure Active Directory de forma continuada. Para ayudarle a mantenerse al día con los avances más recientes, este tema le proporciona información acerca de:

-   Versiones más recientes 
-   Problemas conocidos 
-   Corrección de errores 
-   Funciones obsoletas 
-   Planes de cambios 

Vuelva a visitar esta página con regularidad, ya que se actualiza mensualmente.


## <a name="december-2017"></a>Diciembre de 2017
 

### <a name="terms-of-use-in-the-access-panel-for-end-users"></a>Términos de uso en el panel de acceso para los usuarios finales

**Tipo:** nueva característica  
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento
 
Los usuarios finales tienen ahora la posibilidad de ir al panel de acceso y ver los términos de uso que han aceptado previamente.

Los usuarios pueden revisar y ver los términos de uso que han aceptado. Se puede realizar mediante cualquiera de los procedimientos siguientes:

1. Navegue e inicie de sesión en el [portal Mis aplicaciones](https://myapps.microsoft.com).

2. En la esquina superior derecha, haga clic en su nombre y seleccione **Perfil** en la lista desplegable. 

3. En Perfil, haga clic en **Revisar los términos de uso**. 

4. Desde allí puede revisar los términos de uso que ha aceptado. 

Para obtener más información, consulte [Característica Términos de uso de Azure Active Directory (versión preliminar)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nueva experiencia de inicio de sesión de Azure AD

**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Capacidad de producto:** autenticación de usuarios
 
Como parte del viaje para unir los sistemas de identidad de cuentas de Microsoft y Azure AD, hemos rediseñado la interfaz de usuario en ambos sistemas para que tengan una apariencia coherente. Además, hemos paginado la página de inicio de sesión de Azure AD para poder recopilar el nombre de usuario en primer lugar, seguido de la credencial en una segunda pantalla.

Para obtener más información, consulte [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Nueva experiencia de inicio de sesión de Azure AD disponible en versión preliminar pública).
 
---
 

### <a name="fewer-login-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-login"></a>Menos solicitudes de inicio de sesión: nueva experiencia "Mantener la sesión iniciada" para el inicio de sesión de Azure AD

**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Capacidad de producto:** autenticación de usuarios
 
Hemos sustituido la casilla **Mantener la sesión iniciada** en la página de inicio de sesión de Azure AD por un nuevo mensaje que se muestra cuando el usuario se autentica correctamente. 

Si un usuario responde **Sí** a este mensaje, el servicio le proporciona un token de actualización persistente. Este es el mismo comportamiento que se produce cuando el usuario activa la casilla **Mantener la sesión iniciada** en la experiencia antigua. Para los inquilinos federados, este mensaje se muestra cuando el usuario se autentica correctamente en el servicio federado.

Para obtener más información, consulte [Fewer login prompts: The new “Keep me signed in” experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Menos solicitudes de inicio de sesión: nueva experiencia "Mantener la sesión iniciada" de Azure AD disponible en versión preliminar). 

---
 

### <a name="add-configuration-to-require-the-tou-to-be-expanded-prior-to-accepting"></a>Agregar configuración para exigir que las condiciones de uso se expandan antes de su aceptación.

**Tipo:** nueva característica  
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno
 
Hemos agregado una opción para que los administradores exijan a los usuarios finales que expandan las condiciones de uso antes de aceptarlas.

Seleccione activado o desactivado para Requerir a los usuarios que expandan las condiciones de uso. Si se establece en activado, los usuarios finales deberán consultar las condiciones de uso antes de aceptarlas.

Para obtener más información, consulte [Característica Términos de uso de Azure Active Directory (versión preliminar)](active-directory-tou.md).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Activación de ámbito para las asignaciones de roles válidos

**Tipo:** nueva característica  
**Categoría de servicio:**Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
La activación con ámbito permite activar las asignaciones de roles de recursos de Azure válidas con menos autonomía que los valores predeterminados de las asignaciones originales. Por ejemplo, se asigna como propietario de una suscripción en su inquilino. La activación con ámbito le permite activar el propietario para hasta cinco de los recursos incluidos en la suscripción (por ejemplo, grupos de recursos, máquinas virtuales, etc.). La definición del ámbito de la activación puede reducir la posibilidad de ejecutar los cambios no deseados en recursos importantes de Azure.

Para más información, vea [¿Qué es Azure AD Privileged Identity Management?](active-directory-privileged-identity-management-configure.md).
 
---
 

### <a name="new-federated-apps-in-azure-ad-app-gallery"></a>Nuevas aplicaciones federadas en la galería de aplicaciones de Azure AD

**Tipo:** nueva característica  
**Categoría de servicio:** aplicaciones empresariales  
**Capacidad de producto:** integración de terceros
 
En diciembre de 2017, agregamos las siguientes aplicaciones nuevas en nuestra galería de aplicaciones con compatibilidad de federación:

|NOMBRE|Tipo de integración|DESCRIPCIÓN|
|:-- |----------------|:----------|
|EFI Digital StoreFront|SAML 2.0|[Aplicación Web 2 Print](https://go.microsoft.com/fwlink/?linkid=861685)|
|Vodeclic|SAML 2.0|[Use Microsoft Azure AD para administrar el acceso de los usuarios y para habilitar el inicio de sesión único con Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522).  Requiere una cuenta de Vodeclic existente.|
|Accredible|SAML 2.0|[Cree, administre y entregue certificados, notificaciones y credenciales de cadena de bloques](https://go.microsoft.com/fwlink/?linkid=863523).|
|FactSet|SAML 2.0|[Inicio de sesión único para la aplicación FDSWeb de FactSet](https://go.microsoft.com/fwlink/?linkid=863525).|
|Integración de Azure AD con MobileIron|SAML 2.0|La misión de [MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) es permitir que las empresas modernas protejan y administren la información a medida que se mueve a móviles y a la nube, al tiempo que conserve la privacidad y la confianza del usuario final.|
|IMAGE WORKS|SAML 2.0|Use Azure AD para administrar el acceso de los usuarios, aprovisionar cuentas de usuario y habilitar el inicio de sesión único con [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517). Requiere una suscripción de IMAGE WORKS existente.|
|SAML SSO for Bitbucket by resolution GmbH|SAML 2.0|[SSO Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) delega la autenticación a Azure AD. Los usuarios que ya tienen una sesión iniciada en Azure AD puede acceder a Bitbucket directamente. Los usuarios pueden crearse y actualizarse sobre la marcha con datos de atributos de SAML.|
|SAML SSO for Bamboo by resolution GmbH|SAML 2.0|[SSO Bamboo](https://go.microsoft.com/fwlink/?linkid=863520) delega la autenticación a Azure AD. Los usuarios que ya tienen una sesión iniciada en Azure AD puede acceder a Bamboo directamente.|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) es el software de intranet social moderno y con todas las características que ayuda a sus empleados y a su empresa.|
|MOBI|SAML 2.0|[Centralice, comprenda y controle su ecosistema de dispositivos completo](https://go.microsoft.com/fwlink/?linkid=863521).|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) es una plataforma moderna para la administración del rendimiento, los comentarios en tiempo real y la definición de objetivos. Facultamos a los empleados para que puedan controlar su propio desarrollo, y pueda centrarse más en las estrategias.|
|CybSafe|OpenID Connect y OAuth|CybSafe es una plataforma de reconocimiento cibernético con certificación GCHQ. Utiliza tecnología avanzada y análisis de datos para reducir visiblemente el aspecto humano de la ciberseguridad y el riesgo de protección de datos.|
|WebHR|OpenID Connect y OAuth|El software de RR. HH. social todo en uno preferido por todo el mundo. Con la confianza de más de 20 000 empresas en 197 países|
 |Integración de Azure AD con Zenegy|OpenID Connect y OAuth|Con esta aplicación puede utilizar las credenciales de Azure Active Directory de su empresa para iniciar sesión en Zenegy.|
|Adobe Experience Manager|SAML 2.0|Adobe Experience Manager (AEM) es una solución de plataforma de administración de contenido completa para la creación de sitios web, aplicaciones móviles y formularios, que facilita la administración de recursos y contenido de marketing.|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flujos de trabajo de aprobación de roles de directorio de Azure AD

**Tipo:** característica modificada  
**Categoría de servicio:**Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
El flujo de trabajo de aprobación de roles de directorio de Azure AD está disponible de manera general.

Con el flujo de trabajo de aprobación, los administradores de roles con privilegios pueden solicitar la activación de roles de solicitud de los miembros del rol para poder usar el rol con privilegios.
Es posible que se deleguen responsabilidades de aprobación a varios usuarios y grupos. Los miembros del rol válidos reciben notificaciones cuando la aprobación se completa y su rol está activo.

---
 

### <a name="pass-through-authentication---skype-for-business-support"></a>Autenticación de paso a través: soporte técnico de Skype Empresarial

**Tipo:** característica modificada  
**Categoría de servicio:** autenticaciones (inicios de sesión)  
**Capacidad de producto:** autenticación de usuarios


La autenticación de paso a través admite ahora los inicios de sesión de usuario en aplicaciones cliente de Skype Empresarial que admitan la autenticación moderna, incluidas las topologías híbridas y en línea. 

Para obtener más información, consulte [Topologías de Skype Empresarial compatibles con la autenticación moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-active-directory-privileged-identity-management-pim-for-azure-rbac-preview"></a>Actualizaciones a Azure Active Directory Privileged Identity Management (PIM) para RBAC de Azure (versión preliminar)

**Tipo:** característica modificada  
**Categoría del servicio:** PIM  
**Funcionalidad del producto:** Privileged Identity Management
 
Con nuestra actualización de versión preliminar pública de Azure Active Directory Privileged Identity Management (PIM) para RBAC de Azure, ahora puede realizar lo siguiente:

Usar Just Enough Administration. Requerir aprobación para activar roles de recursos. Programar una activación futura de un rol que requiera la aprobación de roles de AAD y RBAC de Azure.

 
Para obtener más información, consulte [PIM para recursos de Azure (versión preliminar)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>Noviembre de 2017
 
### <a name="retiring-acs"></a>Retirada de ACS



**Tipo:** plan de cambio  
**Categoría del servicio:** ACS  
**Capacidad de producto:** Access Control Service 


Microsoft Azure Active Directory Access Control (también conocido como Access Control Service o ACS) se retirará a finales de 2018.  En las próximas semanas ofreceremos más información, como una programación detallada y una guía de migración de alto nivel. Mientras tanto, si tiene cualquier pregunta relacionada con ACS, háganosla llegar a través de los comentarios de esta página y un miembro de nuestro equipo nos ayudará a responderle.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir el acceso desde explorador a Intune Managed Browser 


**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades




Con este comportamiento, podrá restringir el acceso desde explorador a Office 365 y otras aplicaciones en la nube conectadas a Azure AD mediante Intune Managed Browser como una aplicación aprobada. 

Este cambio le permite configurar la siguiente condición de acceso condicional basado en aplicaciones:

**Aplicaciones cliente:** explorador

**¿Cuál es el efecto del cambio?**

En la actualidad, el acceso está bloqueado cuando se usa esta condición. Cuando la vista previa de este comportamiento está disponible, todos los accesos requerirán el uso de la aplicación de explorador administrado. 

Busque esta funcionalidad y otra información en los próximos blogs y notas de versión. 

Para más información, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD

 
**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades




Se prevé que las siguientes aplicaciones se agreguen a la lista de [aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para obtener más información, consulte 

- [Requisito de aplicación cliente aprobada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Términos de soporte técnico de uso para varios idiomas



**Tipo:** nueva característica    
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento





Los administradores ahora pueden crear nuevas condiciones de uso (CDU) que contengan varios documentos PDF. Puede etiquetar estos documentos PDF con el idioma correspondiente. Los usuarios que se encuentran en el ámbito se muestran en el PDF con el idioma correspondiente según sus preferencias. Si no hay ninguna coincidencia, se muestra el idioma predeterminado.


---
 

### <a name="realtime-password-writeback-client-status"></a>Estado del cliente de escritura diferida de contraseñas en tiempo real



**Tipo:** nueva característica  
**Categoría del servicio:** SSPR  
**Capacidad de producto:** autenticación de usuarios


 

Ahora puede revisar el estado de su cliente de escritura diferida de contraseñas local. Esta opción está disponible en la sección **Integración local** de la página **[Restablecimiento de contraseña](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

Si hay problemas con la conexión con el cliente de escritura diferida local, verá un mensaje de error que le proporciona:

- Información sobre por qué no puede conectarse a su cliente de escritura diferida local 
- Un vínculo a documentación que le ayuda a resolver el problema. 


Para más información, vea [Integración local](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Acceso condicional basado en aplicaciones de Azure AD 



 
**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Funcionalidad del producto:** seguridad y protección de identidades





Ahora puede restringir el acceso a Office 365 y otras aplicaciones de Azure en la nube conectadas a Azure AD para [aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) que admiten directivas de Intune App Protection mediante [acceso condicional basado en aplicaciones de Azure AD](active-directory-conditional-access-mam.md). Las directivas de Intune App Protection se utilizan para configurar y proteger los datos de empresa en estas aplicaciones cliente.

Mediante la combinación de directivas de acceso condicional [basado en aplicaciones](active-directory-conditional-access-mam.md) con las directivas de acceso condicional [basado en dispositivos](active-directory-conditional-access-policy-connected-applications.md), tiene la flexibilidad necesaria para proteger los datos de dispositivos personales y de la empresa.

Ahora tiene a su disposición los siguientes controles y condiciones para su uso con acceso condicional basado en aplicaciones:

**Condición de plataforma admitida**

- iOS
- Android

**Condición de aplicaciones cliente**

- Aplicaciones móviles y aplicaciones de escritorio

**Control de acceso**

- Requerir aplicación cliente aprobada


Para más información, consulte [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Administración de dispositivos de Azure AD en Azure Portal



**Tipo:** nueva característica  
**Categoría del servicio:** administración y registro de dispositivos  
**Funcionalidad del producto:** seguridad y protección de identidades

 



Ahora puede encontrar todos los dispositivos conectados a Azure AD y las actividades relacionadas con dispositivos en un solo lugar. Hay una nueva experiencia de administración para administrar todas las configuraciones e identidades de dispositivos en Azure Portal. En esta versión puede:

- Ver todos los dispositivos que están disponibles para el acceso condicional en Azure AD

- Ver propiedades, incluidos los dispositivos unidos a Azure AD híbrido

- Encontrar las claves de BitLocker para los dispositivos unidos a Azure AD, administrar el dispositivo con Intune y mucho más.

- Administrar la configuración relacionada con dispositivos de Azure AD


Para más información, consulte [Administración de dispositivos con Azure Portal](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Compatibilidad con macOS como plataforma de dispositivos para acceso condicional de Azure AD 



**Tipo:** nueva característica    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades 
 

Ahora puede incluir (o excluir) macOS como condición de plataforma de dispositivos en la directiva de acceso condicional de Azure AD. Con la incorporación de macOS a las plataformas de dispositivos compatibles, puede:

- **Inscribir y administrar dispositivos macOS mediante Intune**: similar a otras plataformas como iOS y Android, hay disponible una aplicación de portal de empresa para macOS para realizar inscripciones unificadas. La nueva aplicación de portal de empresa para macOS permite inscribir un dispositivo con Intune y registrarlo con Azure AD.
 
- **Asegurarse de que los dispositivos macOS siguen las directivas de cumplimiento de su organización definidas en Intune**: en Intune, en Azure Portal, ahora puede configurar directivas de cumplimiento para dispositivos macOS. 
  
- **Restringir el acceso a las aplicaciones de Azure AD a solo los dispositivos compatibles con macOS**: la creación de directivas de acceso condicional tiene macOS como una opción de plataforma de dispositivo independiente. Esta opción permite crear directivas de acceso condicional específicas de macOS para el conjunto de aplicaciones de destino en Azure.

Para obtener más información, consulte 

- [Creación de una directiva de cumplimiento para dispositivos macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Extensión NPS para Azure MFA 


**Tipo:** nueva característica    
**Categoría del servicio:** MFA  
**Capacidad de producto:** autenticación de usuarios




La extensión de Servidor de directivas de redes (NPS) para Azure MFA agrega funcionalidades de MFA basadas en la nube a la infraestructura de autenticación mediante los servidores existentes. Con la extensión NPS, puede agregar verificación de llamadas de teléfono, mensaje de texto o de aplicaciones de teléfono al flujo de autenticación existente sin tener que instalar, configurar ni mantener servidores nuevos. 

Esta extensión se creó para las organizaciones que desean proteger las conexiones VPN sin tener que implementar el servidor de Azure MFA. La extensión de NPS actúa como un adaptador entre RADIUS y Azure MFA basada en la nube para proporcionar un segundo factor de autenticación para usuarios federados o sincronizados.


Para más información, consulte [Integración de la infraestructura existente de NPS con Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restauración o eliminación permanente de usuarios eliminados


**Tipo:** nueva característica    
**Categoría del servicio:** administración de usuarios  
**Funcionalidad del producto:** directorio 



En el centro de administración de Azure AD, ahora puede:

- Restaurar un usuario eliminado 
- Eliminar un usuario permanentemente 


**Para probarlo:**

1. En el centro de administración de Azure AD, seleccione [**Todos los usuarios**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) en la sección **Administrar**. 

2. En la lista **Mostrar**, seleccione **Usuarios eliminados recientemente**. 

4. Seleccione uno o varios usuarios recién eliminados y después restáurelos o elimínelos permanentemente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD

 
**Tipo:** característica modificada  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades


Las siguientes aplicaciones se han agregado a la lista de [aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


Para obtener más información, consulte 

- [Requisito de aplicación cliente aprobada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Posibilidad de usar "O" entre controles en una directiva de acceso condicional 


**Tipo:** característica modificada    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades

 
Se ha implementado la posibilidad de usar 'O' (requiere uno de los controles seleccionados) en controles de acceso condicional. Esta característica permite crear directivas con un operador **O** entre controles de acceso. Por ejemplo, puede usar esta característica para crear una directiva que requiere que un usuario inicie sesión mediante autenticación multifactor **O** que esté en un dispositivo compatible.

Para más información, consulte [Controles en el acceso condicional de Azure Active Directory](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Agregación de eventos de riesgo en tiempo real


**Tipo:** característica modificada    
**Categoría del servicio:** protección de identidad  
**Funcionalidad del producto:** seguridad y protección de identidades


Para mejorar la experiencia de administración, en Azure AD Identity Protection, todos los eventos de riesgo en tiempo real que se han originado desde la misma dirección IP en un día determinado ahora se agregan para cada tipo de evento de riesgo. Este cambio limita el volumen de los eventos de riesgo que se muestran sin ningún cambio en la seguridad del usuario.

La detección en tiempo real subyacente funciona cada vez que el usuario inicia sesión. Si tiene una directiva de seguridad de inicio de sesión de riesgo configurada para MFA o bloquear el acceso, todavía se desencadena durante cada inicio de sesión con riesgo.

 
---
 




## <a name="october-2017"></a>Octubre de 2017


### <a name="deprecating-azure-ad-reports"></a>Dejar de usar informes de Azure AD


**Tipo:** plan de cambio  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  



Azure Portal proporciona lo siguiente:

- Una nueva consola de administración de Azure Active Directory 
- Nuevas API de informes de actividades y de seguridad
 
Debido a estas nuevas funcionalidades, las API de informes bajo el punto de conexión **/reports** se retirará el 10 de diciembre de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detección automática de campos de inicio de sesión


**Tipo:** fijo   
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  



Azure Active Directory admite la detección automática de campos de inicio de sesión para las aplicaciones que presentan un campo de nombre de usuario y contraseña HTML.  Estos pasos se documentan en [Captura automática de campos de inicio de sesión para una aplicación](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Puede encontrar esta funcionalidad mediante la adición de una aplicación *situada fuera de la galería* en la página **Aplicaciones empresariales** en [Azure Portal](http://aad.portal.azure.com). Además, en esta nueva aplicación puede configurar el modo **Inicio de sesión único** en **Inicio de sesión único basado en contraseña**. Para ello, especifique una URL web y, a continuación, guarde la página.
 
Debido a un problema del servicio, esta funcionalidad se deshabilitó temporalmente. El problema está resuelto y la detección automática del campo de inicio de sesión vuelve a estar disponible.

---

### <a name="new-mfa-features"></a>Nuevas características MFA


**Tipo:** nueva característica  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** seguridad y protección de identidades  



La autenticación multifactor (MFA) es una parte esencial de la protección de su organización. Para hacer que las credenciales tengan mayor capacidad de adaptación y que la experiencia resulte más sencilla, se han agregado las siguientes características: 

- Integración de los resultados del desafío multifactor directamente en el informe de inicio de sesión de Azure AD, incluido el acceso mediante programación a los resultados de MFA

- Integración más profunda de la configuración de MFA en la experiencia de configuración de Azure AD en Azure Portal

Con esta versión preliminar pública, los informes y la administración de MFA son una parte integrada de la experiencia de configuración principal de AD Azure. La incorporación de ambas características le permite administrar la funcionalidad del portal de administración de MFA dentro de la experiencia de Azure AD.

Para obtener más información, vea [Información de referencia para los informes de la autenticación multifactor en Azure Portal](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Introducción a las condiciones de uso



**Tipo:** nueva característica  
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno  



Las condiciones de uso de Azure AD proporcionan un método sencillo de presentar la información a los usuarios finales. Esto garantiza que los usuarios ven las declinaciones de responsabilidades pertinentes para los requisitos legales o de cumplimiento.

Los términos de uso de Azure AD puede utilizarse en los escenarios siguientes:

- Términos de uso generales para todos los usuarios de su organización. 

- Términos de uso específicos basados en los atributos de un usuario (por ejemplo médicos por un lado y enfermeras por otro, empleados nacionales por un lado e internacionales por otro, en función de grupos dinámicos). 

- Términos de uso específicos para el acceso a las aplicaciones de alto impacto empresarial, como Salesforce.

Para obtener más información, consulte [Términos de uso de Azure Active Directory](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Mejoras en Privileged Identity Management


**Tipo:** nueva característica  
**Categoría del servicio:** PIM  
**Funcionalidad del producto:** Privileged Identity Management  


Con Privileged Identity Management (PIM) de Azure Active Directory, puede administrar, controlar y supervisar el acceso a los recursos de Azure dentro de su organización para:

- Suscripciones
- Grupos de recursos
- Máquinas virtuales. 

Todos los recursos de Azure Portal que aprovechan la funcionalidad de control de acceso basado en rol (RBAC) de Azure también pueden aprovechar todas las funcionalidades de seguridad y administración del ciclo de vida que ofrece PIM de Azure AD.

Para obtener más información, consulte [PIM para recursos de Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Presentación de revisiones de acceso


**Tipo:** nueva característica  
**Categoría de servicio:** Revisiones de acceso  
**Funcionalidad del producto:** gobierno  



Las revisiones de acceso (versión preliminar) permiten a las organizaciones administrar de forma eficiente las pertenencias a grupos y el acceso a las aplicaciones empresariales: 

- Puede volver a certificar el acceso de usuario invitado mediante las revisiones de acceso sobre su acceso a aplicaciones y su pertenencia a grupos. La información que proporcionan las revisiones de acceso permite a los revisores decidir de manera eficiente si los invitados deben seguir teniendo acceso.

- Puede volver a certificar el acceso de los empleados a las aplicaciones y las pertenencias a grupos con las revisiones de acceso.

Puede recopilar los controles de revisiones de acceso en programas importantes para que su organización realice un seguimiento de las revisiones de aplicaciones vulnerables o de cumplimiento normativo.

Para obtener más información, consulte [Revisiones de acceso de Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Ocultación de aplicaciones de terceros de Mis aplicaciones y del iniciador de Office 365



**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  



Ahora puede administrar mejor las aplicaciones que se muestran en los portales de usuario a través de una nueva propiedad **hide app**. La ocultación de aplicaciones resulta útil en los casos en que los iconos de las aplicaciones se muestran para servicios de back-end o iconos duplicados y acaban bloqueando los iniciadores de aplicaciones del usuario. El botón de alternancia se encuentra en la sección de propiedades de la aplicación de terceros y presenta la etiqueta **Visible to user?** (¿Visible para el usuario?). También puede ocultar una aplicación mediante programación a través de PowerShell. 

Para obtener más información, consulte [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md) (Ocultar una aplicación de terceros de la experiencia del usuario en Azure Active Directory). 


**¿Qué está disponible?**

 Como parte de la transición a la nueva consola de administración, hay disponibles dos nuevas API para recuperar los registros de actividad de Azure AD. El nuevo conjunto de API proporciona más funciones de filtro y ordenación, así como actividades de auditoría e inicio de sesión. Los datos que estaban disponibles anteriormente a través de los informes de seguridad ahora son accesibles a través de la API de eventos de riesgo de Identity Protection en Microsoft Graph.


## <a name="september-2017"></a>Septiembre de 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Revisión para Microsoft Identity Manager


**Tipo:** característica modificada  
**Categoría del servicio:** Microsoft Identity Manager  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  



Un paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible desde el 25 de septiembre de 2017 para Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Este paquete acumulativo de revisiones:

- Resuelve problemas y agrega mejoras.
- Es una actualización acumulativa que reemplaza todas las actualizaciones de MIM 2016 SP1 hasta la compilación 4.4.1459.0 de Microsoft Identity Manager 2016. 
- Requiere que disponga de **Microsoft Identity Manager 2016, compilación 4.4.1302.0.** 

Para obtener más información, consulte [Paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible para Microsoft Identity Manager SP1 de 2016](https://support.microsoft.com/help/4021562). 

---
