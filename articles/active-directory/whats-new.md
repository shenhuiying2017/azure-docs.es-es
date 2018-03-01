---
title: "Novedades Notas de la versión de Azure Active | Microsoft Docs"
description: "Obtenga información acerca de las novedades de Azure Active Directory (Azure AD), como, por ejemplo, las notas de la versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 7cdeb0a996835f976e6c80e9831479a2ff917434
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?


> Para estar al día de las novedades de Azure Active Directory (Azure AD), suscríbase a esta [![fuente](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



En Azure AD se realizan mejoras de forma continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

-   Versiones más recientes
-   Problemas conocidos
-   Corrección de errores
-   Funciones obsoletas
-   Planes de cambios

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia.


## <a name="january-2018"></a>Enero de 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD 

**Tipo:** nueva característica  
**Categoría del servicio:** aplicaciones empresariales  
**Capacidad del producto:** integración de terceros
 

En enero de 2018, se agregaron a la galería de aplicaciones las siguientes nuevas aplicaciones compatibles con la federación:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) y [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Para obtener información general completa acerca de todos los tutoriales disponibles, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Inicio de sesión con riesgo adicional detectado

**Tipo:** nueva característica  
**Categoría del servicio:** protección de la identidad  
**Funcionalidad del producto:** protección y seguridad de la identidad
 

La perspectiva que se obtiene de un evento de riesgo detectado está asociada a su suscripción de Azure AD. Con la edición de Azure AD Premium P2, obtiene la información más detallada acerca de todas las detecciones subyacentes.

Con la versión de Azure AD Premium P1, las detecciones que no están cubiertas por su licencia aparecen como el evento de riesgo Inicio de sesión con riesgo adicional detectado.

Para más información, consulte [Eventos de riesgo de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Ocultación de aplicaciones de Office 365 de los paneles de acceso del usuario final

**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO
 

Ahora puede administrar mejor el modo en que se muestran las aplicaciones de Office 365 en los paneles de acceso de sus usuarios mediante una nueva configuración de usuario. Esta opción le resultará útil para reducir la cantidad de aplicaciones de los paneles de acceso de los usuarios si prefiere mostrar solo las aplicaciones de Office en el Portal de Office. La configuración se encuentra en **Configuración de usuario** y tiene la etiqueta **Los usuarios solo pueden ver las aplicaciones de Office 365 en el Portal de Office 365**.
 

Para obtener más información, consulte [Ocultación de una aplicación de la experiencia del usuario en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Inicio de sesión sin problemas en aplicaciones habilitadas para SSO de contraseña directamente desde la dirección URL de la aplicación 

**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO
 

La extensión del explorador Mis aplicaciones ahora está disponible a través de una cómoda herramienta que ofrece la funcionalidad de inicio de sesión único Mis aplicaciones como acceso directo del explorador. Tras la instalación, los usuarios verán un icono de gofre en el explorador que proporciona acceso rápido a las aplicaciones. Ahora, los usuarios podrán disfrutar de las ventajas siguientes:

- Capacidad para iniciar sesión directamente en aplicaciones de SSO de contraseña desde la página de inicio de sesión de la aplicación.
- Inicio de cualquier aplicación mediante la característica de búsqueda rápida.
- Accesos directos a aplicaciones usadas recientemente desde la extensión.
- La extensión está disponible para Edge, Chrome y Firefox.
 
Para obtener más información, consulte [Extensión de inicio de sesión seguro de Mis aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Retirada de la experiencia de administración de Azure AD del Portal de Azure clásico

**Tipo:** obsoleto   
**Categoría del servicio:** Azure AD  
**Funcionalidad del producto:** directorio
 

El 8 de enero de 2018, la experiencia de administración de Azure AD se retiró del Portal de Azure clásico. Esto tuvo lugar junto con la retirada del Portal de Azure clásico. En el futuro, deberá usar el [Centro de administración de Azure AD](https://aad.portal.azure.com) para todas las tareas de administración de Azure AD basadas en el portal.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>El portal web PhoneFactor se ha retirado.

**Tipo:** obsoleto  
**Categoría del servicio:** Azure AD  
**Funcionalidad del producto:** directorio
 

El 8 de enero de 2018 se retiró el portal web PhoneFactor. Este portal se usaba para la administración del servidor MFA; sin embargo, sus funciones se han movido a Azure Portal en portal.azure.com. 

La configuración de MFA se encuentra en: **Azure Active Directory \> Servidor MFA**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Informes de Azure AD obsoletos


**Tipo:** obsoleto  
**Categoría del servicio:** informes  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  


Con la disponibilidad general de la nueva consola de administración de Azure Active Directory y las nuevas API disponibles para los informes de actividad y seguridad, las API de informes que se encontraban en el punto de conexión "/reports" se retiraron el 31 de diciembre de 2017.


**¿Qué está disponible?**

Como parte de la transición a la nueva consola de administración, existen dos nuevas API disponibles para recuperar los registros de actividad de Azure AD. El nuevo conjunto de API proporciona más funciones de filtro y ordenación, así como actividades de auditoría e inicio de sesión. Los datos que estaban disponibles anteriormente a través de los informes de seguridad ahora son accesibles a través de la API de eventos de riesgo de Identity Protection en Microsoft Graph.

Para más información, consulte:

- [Introducción a la API de informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Introducción a Azure Active Directory Identity Protection y Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>Diciembre de 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Términos de uso del panel de acceso

**Tipo:** nueva característica  
**Categoría del servicio:** términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento
 
Ahora puede ir al panel de acceso y ver los términos de uso previamente aceptados.

Siga estos pasos:

1. Abra el [portal de Mis aplicaciones](https://myapps.microsoft.com) e inicie sesión.

2. En la esquina superior derecha, seleccione su nombre y seleccione **Perfil** en la lista. 

3. En su **Perfil**, seleccione **Revisar los términos de uso**. 

4. Ahora podrá revisar los términos de uso que aceptó. 

Para obtener más información, consulte [Característica Términos de uso de Azure Active Directory (versión preliminar)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nueva experiencia de inicio de sesión de Azure AD

**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Capacidad del producto:** autenticación de usuarios
 
Azure AD y las interfaces de usuario del sistema de identidades de cuentas de Microsoft se han rediseñado para ofrecer un aspecto más homogéneo. Además, la página de inicio de sesión de Azure AD primero recopila el nombre de usuario, seguido de la credencial en una segunda pantalla.

Para obtener más información, consulte [The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (Nueva experiencia de inicio de sesión de Azure AD disponible en versión preliminar pública).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menos solicitudes de inicio de sesión: nueva experiencia "Mantener la sesión iniciada" para el inicio de sesión de Azure AD

**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Capacidad del producto:** autenticación de usuarios
 
Hemos sustituido la casilla **Mantener la sesión iniciada** de la página de inicio de sesión de Azure AD por un nuevo mensaje que se muestra al realizar la autenticación correctamente. 

Si se responde **Sí** a este mensaje, el servicio proporciona un token de actualización persistente. Este es el mismo comportamiento que se produce cuando se activa la casilla **Mantener la sesión iniciada** en la experiencia antigua. Para los inquilinos federados, este mensaje se muestra al autenticarse correctamente en el servicio federado.

Para obtener más información, consulte [Fewer login prompts: The new “Keep me signed in” experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Menos solicitudes de inicio de sesión: nueva experiencia "Mantener la sesión iniciada" de Azure AD disponible en versión preliminar). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Agregar configuración para exigir expandir los términos de uso antes de su aceptación

**Tipo:** nueva característica  
**Categoría del servicio:** términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento
 
Se ha agregado una opción para los administradores con el fin de exigir a usuarios finales que expandan los términos de uso antes de aceptarlos.

Seleccione **Activado** o **Desactivado** para exigir que los usuarios expandan los términos de uso. El valor **Activado** exige a usuarios que lean los términos de uso antes de aceptarlos.

Para obtener más información, consulte [Característica Términos de uso de Azure Active Directory (versión preliminar)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Activación de ámbito para las asignaciones de roles válidos

**Tipo:** nueva característica  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
Puede usar la activación con ámbito para activar las asignaciones de roles de recursos de Azure válidas con menos autonomía que los valores predeterminados de las asignaciones originales. Un ejemplo de esto es si se le asigna como propietario de una suscripción en su inquilino. Con la activación con ámbito, podrá activar el rol de propietario para hasta cinco de los recursos incluidos en la suscripción (por ejemplo, los grupos de recursos y las máquinas virtuales). La definición del ámbito de la activación puede reducir la posibilidad de ejecutar cambios no deseados en recursos importantes de Azure.

Para más información, vea [¿Qué es Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuevas aplicaciones federadas en la galería de aplicaciones de Azure AD

**Tipo:** nueva característica  
**Categoría del servicio:** aplicaciones empresariales  
**Capacidad del producto:** integración de terceros
 
En diciembre de 2017, se agregaron a la galería de aplicaciones las siguientes nuevas aplicaciones compatibles con la federación:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [Integración de Azure AD con MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integration.

Para obtener información general completa acerca de todos los tutoriales disponibles, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flujos de trabajo de aprobación de roles de directorio de Azure AD

**Tipo:** característica modificada  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
El flujo de trabajo de aprobación de roles de directorio de Azure AD está disponible de manera general.

Con el flujo de trabajo de aprobación, los administradores de roles con privilegios pueden exigir que los miembros de roles aptos soliciten la activación de roles para poder usar el rol con privilegios. Ahora es posible delegar responsabilidades de aprobación en múltiples usuarios y grupos. Los miembros del rol aptos recibirán notificaciones cuando termine la aprobación y sus roles estén activos.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticación de paso a través: soporte técnico de Skype Empresarial

**Tipo:** característica modificada  
**Categoría del servicio:** autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** autenticación de usuarios


Ahora, la autenticación de paso a través admite inicios de sesión de usuarios en aplicaciones cliente de Skype Empresarial compatibles con la autenticación moderna, incluidas las topologías híbridas y en línea. 

Para obtener más información, consulte [Topologías de Skype Empresarial compatibles con la autenticación moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Actualizaciones de Azure AD Privileged Identity Management para RBAC de Azure (versión preliminar)

**Tipo:** característica modificada  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management
 
Con la actualización de versión preliminar pública de Azure AD Privileged Identity Management (PIM) para el control de acceso basado en roles (RBAC) de Azure, ahora podrá realizar lo siguiente:

* Usar Just Enough Administration.
* Solicitar aprobación para activar roles de recursos.
* Programar la activación futura de un rol que requiere la aprobación de roles de Azure AD y de RBAC de Azure.

 
Para obtener más información, consulte [PIM para recursos de Azure (versión preliminar)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>Noviembre de 2017
 
### <a name="access-control-service-retirement"></a>Retirada del servicio de control de acceso



**Tipo:** plan de cambio  
**Categoría del servicio:** Access Control Service  
**Funcionalidad del producto:** Access Control Service 


 Azure Active Directory Access Control (también conocido como Access Control Service) se retirará a finales de 2018. En las próximas semanas se ofrecerá más información, como, por ejemplo, una programación detallada y una guía de migración de alto nivel. Puede dejar comentarios en esta página con preguntas acerca de Access Control Service y un miembro del equipo le responderá.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restringir el acceso desde el explorador a Intune Managed Browser 


**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de la identidad




El uso de Intune Managed Browser como aplicación aprobada le permite restringir el acceso desde el explorador a Office 365 y otras aplicaciones en la nube conectadas a Azure AD. 

Ahora podrá configurar la siguiente condición para el acceso condicional basado en aplicaciones:

**Aplicaciones cliente:** explorador

**¿Cuál es el efecto del cambio?**

En la actualidad, el acceso está bloqueado cuando se usa esta condición. Cuando la vista previa está disponible, todos los accesos requerirán el uso de la aplicación Manager Browser. 

Busque esta funcionalidad y más información en las próximas entradas de blogs y notas de versión. 

Para obtener más información, consulte [Acceso condicional en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD

 
**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de la identidad




Se prevé que las siguientes aplicaciones se agreguen a la lista de [aplicaciones cliente aprobadas](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para más información, consulte:

- [Requisito de aplicación cliente aprobada](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acceso condicional basado en aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Disponibilidad de los términos de uso en varios idiomas



**Tipo:** nueva característica    
**Categoría del servicio:** términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento





Ahora, los administradores pueden crear nuevos términos de uso que contengan varios documentos PDF. Puede etiquetar estos documentos PDF con el idioma correspondiente. De este modo, se mostrará a los usuarios el documento PDF en el idioma correspondiente a sus preferencias. Si no hay ninguna coincidencia, se muestra el idioma predeterminado.


---
 

### <a name="real-time-password-writeback-client-status"></a>Estado del cliente de escritura diferida de contraseñas en tiempo real



**Tipo:** nueva característica  
**Categoría del servicio:** autoservicio de restablecimiento de contraseña  
**Funcionalidad del producto:** autenticación de usuarios


 

Ahora podrá revisar el estado de su cliente de escritura diferida de contraseñas local. Esta opción está disponible en la sección **Integración local** de la página [Restablecimiento de contraseña](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Si hay problemas con la conexión al cliente de escritura diferida local, se mostrará un mensaje de error con los elementos siguientes:

- Información del motivo por el que no puede conectarse a su cliente de escritura diferida local.
- Un vínculo a documentación que le ayuda a resolver el problema. 


Para obtener más información, consulte [Integración local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Acceso condicional basado en aplicaciones de Azure AD 



 
**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Funcionalidad del producto:** seguridad y protección de la identidad





Ahora puede restringir el acceso a Office 365 y a otras aplicaciones de Azure en la nube conectadas a Azure AD para [aplicaciones cliente aprobadas](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) que admiten directivas de Intune App Protection mediante el [acceso condicional basado en aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Las directivas de Intune App Protection se utilizan para configurar y proteger los datos de empresa en estas aplicaciones cliente.

Mediante la combinación de directivas de acceso condicional [basado en aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) con las directivas de acceso condicional [basado en dispositivos](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications), tiene la flexibilidad necesaria para proteger los datos de dispositivos personales y de la empresa.

Ahora tiene a su disposición los siguientes controles y condiciones para su uso con el acceso condicional basado en aplicaciones:

**Condición de plataforma admitida**

- iOS
- Android

**Condición de aplicaciones cliente**

- Aplicaciones móviles y aplicaciones de escritorio

**Control de acceso**

- Requerir aplicación cliente aprobada


Para obtener más información, consulte [Acceso condicional basado en aplicaciones de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Administración de dispositivos de Azure AD en Azure Portal



**Tipo:** nueva característica  
**Categoría del servicio:** administración y registro de dispositivos  
**Funcionalidad del producto:** seguridad y protección de la identidad

 



Ahora podrá encontrar todos los dispositivos conectados a Azure AD y las actividades relacionadas con dispositivos en un solo lugar. Hay una nueva experiencia de administración para administrar todas las configuraciones e identidades de dispositivos en Azure Portal. En esta versión podrá hacer lo siguiente:

- Ver todos los dispositivos que están disponibles para el acceso condicional en Azure AD.
- Ver propiedades, incluidos los dispositivos unidos a Azure AD híbrido.
- Encontrar las claves de BitLocker para los dispositivos unidos a Azure AD, administrar el dispositivo con Intune y mucho más.
- Administrar la configuración relacionada con dispositivos de Azure AD.

Para obtener más información, consulte [Administración de dispositivos con Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Compatibilidad con macOS como plataforma de dispositivos para el acceso condicional de Azure AD 



**Tipo:** nueva característica    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de la identidad 
 

Ahora puede incluir (o excluir) macOS como condición de plataforma de dispositivos en la directiva de acceso condicional de Azure AD. Con la incorporación de macOS a las plataformas de dispositivos compatibles, puede:

- **Inscribir y administrar dispositivos Mac OS con Intune.** Al igual que en otras plataformas, como iOS y Android, existe una aplicación de portal de empresa para macOS que permite realizar inscripciones unificadas. Utilice la nueva aplicación de portal de empresa para macOS para inscribir un dispositivo con Intune y registrarlo con Azure AD.
- **Asegurarse de que los dispositivos macOS se ajustan a las directivas de cumplimiento de su organización definidas en Intune.** En Intune en Azure Portal, ahora podrá configurar directivas de cumplimiento para dispositivos macOS. 
- **Restringir el acceso a las aplicaciones de Azure AD a solo los dispositivos macOS que cumplan las directivas.** La creación de directivas de acceso condicional usa macOS como una opción de plataforma de dispositivos independiente. Ahora podrá crear directivas condicionales específicas para macOS para el conjunto de aplicaciones de destino en Azure.

Para más información, consulte:

- [Creación de una directiva de cumplimiento para dispositivos macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Acceso condicional en Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Extensión Servidor de directivas de redes para Azure Multi-Factor Authentication 


**Tipo:** nueva característica    
**Categoría del servicio:** autenticación multifactor  
**Funcionalidad del producto:** autenticación de usuarios




La extensión Servidor de directivas de redes para Azure Multi-Factor Authentication incorpora funcionalidades de autenticación multifactor basadas en la nube para su infraestructura de autenticación mediante los servidores existentes. Con la extensión Servidor de directivas de redes, podrá agregar mecanismos de verificación mediante llamadas de teléfono, mensajes de texto o aplicaciones de teléfono al flujo de autenticación existente. Para ello, no tendrá que instalar, configurar ni mantener servidores nuevos. 

Esta extensión se creó para las organizaciones que quieren proteger las conexiones de redes privadas virtuales sin tener que implementar el Servidor Microsoft Azure Multi-Factor Authentication. La extensión Servidor de directivas de redes actúa como un adaptador entre RADIUS y Azure Multi-Factor Authentication basada en la nube para proporcionar un segundo factor de autenticación a los usuarios federados o sincronizados.


Para obtener más información, consulte [Integración de la infraestructura existente de NPS con Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restauración o eliminación permanente de usuarios eliminados


**Tipo:** nueva característica    
**Categoría del servicio:** administración de usuarios  
**Funcionalidad del producto:** directorio 



En el centro de administración de Azure AD, ahora puede:

- Restaurar un usuario eliminado. 
- Eliminar un usuario permanentemente.


**Para probarlo:**

1. En el centro de administración de Azure AD, seleccione [Todos los usuarios](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) en la sección **Administrar**. 

2. En la lista **Mostrar**, seleccione **Usuarios eliminados recientemente**. 

3. Seleccione uno o varios usuarios eliminados recientemente y después restáurelos o elimínelos permanentemente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD

 
**Tipo:** característica modificada  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de la identidad


Las siguientes aplicaciones se agregaron a la lista de [aplicaciones cliente aprobadas](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Para más información, consulte:

- [Requisito de aplicación cliente aprobada](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Acceso condicional basado en aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Posibilidad de usar el operador "OR" entre controles de una directiva de acceso condicional 


**Tipo:** característica modificada    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de la identidad

 
Ahora es posible utilizar el operador "OR" (requerir uno de los controles seleccionados) en los controles de acceso condicional. Puede usar esta característica para crear directivas con el operador "OR" entre controles de acceso. Por ejemplo, puede usar esta característica para crear una directiva que requiera que un usuario inicie sesión mediante autenticación multifactor "OR" que esté en un dispositivo compatible.

Para obtener más información, consulte [Controles en el acceso condicional de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregación de eventos de riesgo en tiempo real


**Tipo:** característica modificada    
**Categoría del servicio:** protección de la identidad  
**Funcionalidad del producto:** seguridad y protección de la identidad


En Azure AD Identity Protection, todos los eventos de riesgo en tiempo real que se han originado desde la misma dirección IP en un día determinado ahora se agregan para cada tipo de evento de riesgo. Este cambio limita el volumen de los eventos de riesgo que se muestran sin ningún cambio en la seguridad del usuario.

La detección en tiempo real subyacente funciona cada vez que el usuario inicia sesión. Si tiene configurada una directiva de seguridad de riesgo de inicio de sesión para la autenticación multifactor o para bloquear el acceso, esta se desencadena durante cada inicio de sesión con riesgo.

 
---
 




## <a name="october-2017"></a>Octubre de 2017


### <a name="deprecate-azure-ad-reports"></a>Informes de Azure AD obsoletos


**Tipo:** plan de cambio  
**Categoría del servicio:** informes  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  



Azure Portal proporciona lo siguiente:

- Una nueva consola de administración de Azure AD.
- Nuevas API de informes de actividades y de seguridad.
 
Debido a estas nuevas funcionalidades, las API de informes que se encuentran en el punto de conexión /reports se retiraron el 10 de diciembre de 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detección automática de campos de inicio de sesión


**Tipo:** fijo   
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** inicio de sesión único  



Azure AD admite la detección automática de campos de inicio de sesión para las aplicaciones que presentan un campo de nombre de usuario y contraseña HTML. Estos pasos se documentan en [Captura automática de campos de inicio de sesión para una aplicación](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Puede encontrar esta funcionalidad mediante la adición de una aplicación *situada fuera de la galería* en la página **Aplicaciones empresariales** en [Azure Portal](http://aad.portal.azure.com). Además, en esta nueva aplicación podrá configurar el modo **Inicio de sesión único** en **Inicio de sesión único basado en contraseña**, especificar una URL web y, a continuación, guardar la página.
 
Debido a un problema del servicio, esta funcionalidad se deshabilitó temporalmente. El problema se ha resuelto y la detección automática del campo de inicio de sesión vuelve a estar disponible.

---

### <a name="new-multi-factor-authentication-features"></a>Nuevas características de autenticación multifactor


**Tipo:** nueva característica  
**Categoría del servicio:** autenticación multifactor  
**Funcionalidad del producto:** seguridad y protección de la identidad  



Multi-Factor Authentication (MFA) es un componente esencial para la protección de su organización. Para hacer que las credenciales tengan mayor capacidad de adaptación y que la experiencia resulte más sencilla, se han agregado las siguientes características: 

- Integración de los resultados del desafío multifactor directamente en el informe de inicio de sesión de Azure AD, incluido el acceso mediante programación a los resultados de MFA.
- Integración más profunda de la configuración de MFA en la experiencia de configuración de Azure AD en Azure Portal.

Con esta versión preliminar pública, los informes y la administración de MFA son una parte integrada de la experiencia de configuración principal de AD Azure. Ahora podrá administrar la funcionalidad del portal de administración de MFA en la experiencia de Azure AD.

Para obtener más información, consulte [Referencia para los informes de la autenticación multifactor en Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Términos de uso



**Tipo:** nueva característica  
**Categoría del servicio:** términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento  



Puede usar los términos de uso de Azure AD para presentar información a los usuarios, como, por ejemplo, renuncias relevantes para los requisitos legales o de cumplimiento.

Los términos de uso de Azure AD puede utilizarse en los escenarios siguientes:

- Términos de uso generales para todos los usuarios de su organización.
- Términos de uso específicos basados en los atributos de un usuario (por ejemplo, médicos frente a enfermeras o empleados nacionales frente a internacionales, creados por grupos dinámicos).
- Términos de uso específicos para el acceso a aplicaciones empresariales de alto impacto, como, por ejemplo, Salesforce.

Para obtener más información, consulte [Términos de uso de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Mejoras en Privileged Identity Management


**Tipo:** nueva característica  
**Categoría del servicio:** Privileged Identity Management  
**Funcionalidad del producto:** Privileged Identity Management  


Con Privileged Identity Management (PIM) de Azure AD, podrá administrar, controlar y supervisar el acceso a los recursos de Azure (vista preliminar) dentro de su organización por parte de:

- Suscripciones
- Grupos de recursos
- Máquinas virtuales 

Todos los recursos de Azure Portal que usan la funcionalidad RBAC de Azure pueden usar las funcionalidades de seguridad y administración del ciclo de vida que ofrece Privileged Identity Management de Azure AD.

Para obtener más información, consulte [PIM para recursos de Azure (versión preliminar)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Revisiones de acceso


**Tipo:** nueva característica  
**Categoría del servicio:** revisiones de acceso  
**Funcionalidad del producto:** gobierno y cumplimiento  



Las organizaciones pueden usar las revisiones de acceso (versión preliminar) para administrar de manera eficaz las pertenencias a grupos y el acceso a las aplicaciones empresariales: 

- Puede volver a certificar el acceso de usuario invitado mediante las revisiones de acceso a las aplicaciones y la pertenencia a grupos. La información que proporcionan las revisiones de acceso permite a los revisores decidir de manera eficaz si deben permitir el acceso continuado a los invitados.
- Puede volver a certificar el acceso de los empleados a las aplicaciones y la pertenencia a grupos con las revisiones de acceso.

Puede recopilar los controles de revisiones de acceso en programas importantes para que su organización realice un seguimiento de las revisiones de aplicaciones vulnerables o de cumplimiento normativo.

Para obtener más información, consulte [Revisiones de acceso de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Ocultación de aplicaciones de terceros de Mis aplicaciones y del iniciador de aplicaciones de Office 365



**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** inicio de sesión único  



Ahora podrá administrar mejor las aplicaciones que se muestran en los portales de usuario a través de la nueva propiedad **hide app**. Puede ocultar aplicaciones para ayudar en casos en los que se muestran iconos de aplicaciones para servicios back-end o iconos duplicados que se acumulan en los iniciadores de aplicaciones de usuarios. El botón de alternancia se encuentra en la sección **Propiedades** de la aplicación de terceros con la etiqueta **Visible to user?** (¿Visible para el usuario?). También puede ocultar una aplicación mediante programación a través de PowerShell. 

Para obtener más información, consulte [Ocultación de una aplicación de la experiencia del usuario en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**¿Qué está disponible?**

 Como parte de la transición a la nueva consola de administración, hay disponibles dos nuevas API que permiten recuperar los registros de actividad de Azure AD. El nuevo conjunto de API proporciona más funciones de filtro y ordenación, así como actividades de auditoría e inicio de sesión. Los datos que estaban disponibles anteriormente a través de los informes de seguridad ahora se proporcionan a través de la API de eventos de riesgo de Identity Protection en Microsoft Graph.


## <a name="september-2017"></a>Septiembre de 2017

### <a name="hotfix-for-identity-manager"></a>Revisión para Identity Manager


**Tipo:** característica modificada  
**Categoría del servicio:** Identity Manager  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  



Un paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible desde el 25 de septiembre de 2017 para Microsoft Identity Manager 2016 Service Pack 1. Este paquete acumulativo de revisiones:

- Resuelve problemas e incorpora mejoras.
- Es una actualización acumulativa que reemplaza a todas las actualizaciones de Identity Manager 2016 Service Pack 1 hasta la compilación 4.4.1459.0 de Identity Manager 2016. 
- Requiere disponer de Identity Manager 2016, compilación 4.4.1302.0. 

Para obtener más información, consulte [Paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible para Microsoft Identity Manager SP1 de 2016](https://support.microsoft.com/help/4021562). 

---
