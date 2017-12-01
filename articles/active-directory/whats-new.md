---
title: "Novedades Notas de la versión de Azure Active | Microsoft Docs"
description: "Conozca las novedades de Azure Active Directory (Azure AD), incluidas las notas de versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9de535b2fb70181c68fb698e847dd8361bf54385
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?




> Para estar al día de las novedades de Azure Active Directory, suscríbase a esta [fuente](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) en su lector de fuentes RSS favorito.



Estamos mejorando Azure Active Directory de forma continuada. Para ayudarle a mantenerse al día con los avances más recientes, este tema le proporciona información acerca de:

-   Versiones más recientes 
-   Problemas conocidos 
-   Corrección de errores 
-   Funciones obsoletas 
-   Planes de cambios 

Vuelva a visitar esta página con regularidad, ya que se actualiza mensualmente.

## <a name="november-2017"></a>Noviembre de 2017
 


**Tipo:** plan de cambio  
**Categoría del servicio:** ACS  
**Capacidad de producto:** Access Control Service 

**Retirada de ACS**

Microsoft Azure Active Directory Access Control (también conocido como Access Control Service o ACS) se retirará a finales de 2018.  En las próximas semanas ofreceremos más información, como una programación detallada y una guía de migración de alto nivel. Mientras tanto, si tiene cualquier pregunta relacionada con ACS, háganosla llegar a través de los comentarios de esta página y un miembro de nuestro equipo nos ayudará a responderle.

---



**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades


**Restringir el acceso desde explorador a Intune Managed Browser** 


Con este comportamiento, podrá restringir el acceso desde explorador a Office 365 y otras aplicaciones en la nube conectadas a Azure AD mediante Intune Managed Browser como una aplicación aprobada. 

Este cambio le permite configurar la siguiente condición de acceso condicional basado en aplicaciones:

**Aplicaciones cliente:** explorador

**¿Cuál es el efecto del cambio?**

En la actualidad, el acceso está bloqueado cuando se usa esta condición. Cuando la vista previa de este comportamiento está disponible, todos los accesos requerirán el uso de la aplicación de explorador administrado. 

Busque esta funcionalidad y otra información en los próximos blogs y notas de versión. 

Para más información, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---


 
**Tipo:** plan de cambio  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades


**Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD**


Se prevé que las siguientes aplicaciones se agreguen a la lista de [aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para más información, consulte:

- [Requisito de aplicación cliente aprobada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md)


---


**Tipo:** nueva característica    
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno y cumplimiento



**Términos de soporte técnico de uso para varios idiomas**


Los administradores ahora pueden crear nuevas condiciones de uso (CDU) que contengan varios documentos PDF. Puede etiquetar estos documentos PDF con el idioma correspondiente. Los usuarios que se encuentran en el ámbito se muestran en el PDF con el idioma correspondiente según sus preferencias. Si no hay ninguna coincidencia, se muestra el idioma predeterminado.


---
 


**Tipo:** nueva característica  
**Categoría del servicio:** SSPR  
**Capacidad de producto:** autenticación de usuarios


**Estado del cliente de escritura diferida de contraseñas en tiempo real**
 

Ahora puede revisar el estado de su cliente de escritura diferida de contraseñas local. Esta opción está disponible en la sección **Integración local** de la página **[Restablecimiento de contraseña](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

Si hay problemas con la conexión con el cliente de escritura diferida local, verá un mensaje de error que le proporciona:

- Información sobre por qué no puede conectarse a su cliente de escritura diferida local 
- Un vínculo a documentación que le ayuda a resolver el problema. 


Para más información, vea [Integración local](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---
 
**Tipo:** nueva característica  
**Categoría del servicio:** Azure AD  
**Funcionalidad del producto:** seguridad y protección de identidades



**Acceso condicional basado en aplicaciones de Azure AD** 


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



**Tipo:** nueva característica  
**Categoría del servicio:** administración y registro de dispositivos  
**Funcionalidad del producto:** seguridad y protección de identidades

 

**Administración de dispositivos de Azure AD en Azure Portal**


Ahora puede encontrar todos los dispositivos conectados a Azure AD y las actividades relacionadas con dispositivos en un solo lugar. Hay una nueva experiencia de administración para administrar todas las configuraciones e identidades de dispositivos en Azure Portal. En esta versión puede:

- Ver todos los dispositivos que están disponibles para el acceso condicional en Azure AD

- Ver propiedades, incluidos los dispositivos unidos a Azure AD híbrido

- Encontrar las claves de BitLocker para los dispositivos unidos a Azure AD, administrar el dispositivo con Intune y mucho más.

- Administrar la configuración relacionada con dispositivos de Azure AD


Para más información, consulte [Administración de dispositivos con Azure Portal](device-management-azure-portal.md).



 
---


**Tipo:** nueva característica    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades 



**Compatibilidad con macOS como plataforma de dispositivos para acceso condicional de Azure AD** 
 

Ahora puede incluir (o excluir) macOS como condición de plataforma de dispositivos en la directiva de acceso condicional de Azure AD. Con la incorporación de macOS a las plataformas de dispositivos compatibles, puede:

- **Inscribir y administrar dispositivos macOS mediante Intune**: similar a otras plataformas como iOS y Android, hay disponible una aplicación de portal de empresa para macOS para realizar inscripciones unificadas. La nueva aplicación de portal de empresa para macOS permite inscribir un dispositivo con Intune y registrarlo con Azure AD.
 
- **Asegurarse de que los dispositivos macOS siguen las directivas de cumplimiento de su organización definidas en Intune**: en Intune, en Azure Portal, ahora puede configurar directivas de cumplimiento para dispositivos macOS. 
  
- **Restringir el acceso a las aplicaciones de Azure AD a solo los dispositivos compatibles con macOS**: la creación de directivas de acceso condicional tiene macOS como una opción de plataforma de dispositivo independiente. Esta opción permite crear directivas de acceso condicional específicas de macOS para el conjunto de aplicaciones de destino en Azure.

Para más información, consulte:

- [Creación de una directiva de cumplimiento para dispositivos macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---


**Tipo:** nueva característica    
**Categoría del servicio:** MFA  
**Capacidad de producto:** autenticación de usuarios


**Extensión NPS para Azure MFA** 


La extensión de Servidor de directivas de redes (NPS) para Azure MFA agrega funcionalidades de MFA basadas en la nube a la infraestructura de autenticación mediante los servidores existentes. Con la extensión NPS, puede agregar verificación de llamadas de teléfono, mensaje de texto o de aplicaciones de teléfono al flujo de autenticación existente sin tener que instalar, configurar ni mantener servidores nuevos. 

Esta extensión se creó para las organizaciones que desean proteger las conexiones VPN sin tener que implementar el servidor de Azure MFA. La extensión de NPS actúa como un adaptador entre RADIUS y Azure MFA basada en la nube para proporcionar un segundo factor de autenticación para usuarios federados o sincronizados.


Para más información, consulte [Integración de la infraestructura existente de NPS con Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md).

 
---


**Tipo:** nueva característica    
**Categoría del servicio:** administración de usuarios  
**Funcionalidad del producto:** directorio 


**Restauración o eliminación permanente de usuarios eliminados**


En el centro de administración de Azure AD, ahora puede:

- Restaurar un usuario eliminado 
- Eliminar un usuario permanentemente 


**Para probarlo:**

1. En el centro de administración de Azure AD, seleccione [**Todos los usuarios**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) en la sección **Administrar**. 

2. En la lista **Mostrar**, seleccione **Usuarios eliminados recientemente**. 

4. Seleccione uno o varios usuarios recién eliminados y después restáurelos o elimínelos permanentemente.

 
---



 
**Tipo:** característica modificada  
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades

**Nuevas aplicaciones cliente aprobadas para el acceso condicional basado en aplicaciones de Azure AD**


Las siguientes aplicaciones se han agregado a la lista de [aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


Para más información, consulte:

- [Requisito de aplicación cliente aprobada](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acceso condicional basado en aplicaciones de Azure Active Directory](active-directory-conditional-access-mam.md)


---



**Tipo:** característica modificada    
**Categoría del servicio:** acceso condicional  
**Funcionalidad del producto:** seguridad y protección de identidades


**Posibilidad de usar 'O' entre controles en una directiva de acceso condicional** 
 
Se ha implementado la posibilidad de usar 'O' (requiere uno de los controles seleccionados) en controles de acceso condicional. Esta característica permite crear directivas con un operador **O** entre controles de acceso. Por ejemplo, puede usar esta característica para crear una directiva que requiere que un usuario inicie sesión mediante autenticación multifactor **O** que esté en un dispositivo compatible.

Para más información, consulte [Controles en el acceso condicional de Azure Active Directory](active-directory-conditional-access-controls.md).

 
---



**Tipo:** característica modificada    
**Categoría del servicio:** protección de identidad  
**Funcionalidad del producto:** seguridad y protección de identidades

**Agregación de eventos de riesgo en tiempo real**

Para mejorar la experiencia de administración, en Azure AD Identity Protection, todos los eventos de riesgo en tiempo real que se han originado desde la misma dirección IP en un día determinado ahora se agregan para cada tipo de evento de riesgo. Este cambio limita el volumen de los eventos de riesgo que se muestran sin ningún cambio en la seguridad del usuario.

La detección en tiempo real subyacente funciona cada vez que el usuario inicia sesión. Si tiene una directiva de seguridad de inicio de sesión de riesgo configurada para MFA o bloquear el acceso, todavía se desencadena durante cada inicio de sesión con riesgo.

 
---
 




## <a name="october-2017"></a>Octubre de 2017

**Tipo:** plan de cambio  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  


**Dejar de usar informes de Azure AD**

Azure Portal proporciona lo siguiente:

- Una nueva consola de administración de Azure Active Directory 
- Nuevas API de informes de actividades y de seguridad
 
Debido a estas nuevas funcionalidades, las API de informes bajo el punto de conexión **/reports** se retirará el 10 de diciembre de 2017. 

---

**Tipo:** fijo   
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  


**Detección automática de campos de inicio de sesión**


Azure Active Directory admite la detección automática de campos de inicio de sesión para las aplicaciones que presentan un campo de nombre de usuario y contraseña HTML.  Estos pasos se documentan en [Captura automática de campos de inicio de sesión para una aplicación](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Puede encontrar esta funcionalidad mediante la adición de una aplicación *situada fuera de la galería* en la página **Aplicaciones empresariales** en [Azure Portal](http://aad.portal.azure.com). Además, en esta nueva aplicación puede configurar el modo **Inicio de sesión único** en **Inicio de sesión único basado en contraseña**. Para ello, especifique una URL web y, a continuación, guarde la página.
 
Debido a un problema del servicio, esta funcionalidad se deshabilitó temporalmente. El problema está resuelto y la detección automática del campo de inicio de sesión vuelve a estar disponible.

---

**Tipo:** nueva característica  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** seguridad y protección de identidades  


**Nuevas características MFA**

La autenticación multifactor (MFA) es una parte esencial de la protección de su organización. Para hacer que las credenciales tengan mayor capacidad de adaptación y que la experiencia resulte más sencilla, se han agregado las siguientes características: 

- Integración de los resultados del desafío multifactor directamente en el informe de inicio de sesión de Azure AD, incluido el acceso mediante programación a los resultados de MFA

- Integración más profunda de la configuración de MFA en la experiencia de configuración de Azure AD en Azure Portal

Con esta versión preliminar pública, los informes y la administración de MFA son una parte integrada de la experiencia de configuración principal de AD Azure. La incorporación de ambas características le permite administrar la funcionalidad del portal de administración de MFA dentro de la experiencia de Azure AD.

Para obtener más información, vea [Información de referencia para los informes de la autenticación multifactor en Azure Portal](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Tipo:** nueva característica  
**Categoría de servicio:** Términos de uso  
**Funcionalidad del producto:** gobierno  


**Introducción a las condiciones de uso**

Las condiciones de uso de Azure AD proporcionan un método sencillo de presentar la información a los usuarios finales. Esto garantiza que los usuarios ven las declinaciones de responsabilidades pertinentes para los requisitos legales o de cumplimiento.

Los términos de uso de Azure AD puede utilizarse en los escenarios siguientes:

- Términos de uso generales para todos los usuarios de su organización. 

- Términos de uso específicos basados en los atributos de un usuario (por ejemplo médicos por un lado y enfermeras por otro, empleados nacionales por un lado e internacionales por otro, en función de grupos dinámicos). 

- Términos de uso específicos para el acceso a las aplicaciones de alto impacto empresarial, como Salesforce.

Para obtener más información, consulte [Términos de uso de Azure Active Directory](active-directory-tou.md).


---
**Tipo:** nueva característica  
**Categoría del servicio:** PIM  
**Funcionalidad del producto:** Privileged Identity Management  

**Mejoras en Privileged Identity Management**

Con Privileged Identity Management (PIM) de Azure Active Directory, puede administrar, controlar y supervisar el acceso a los recursos de Azure dentro de su organización para:

- Suscripciones
- Grupos de recursos
- Máquinas virtuales. 

Todos los recursos de Azure Portal que aprovechan la funcionalidad de control de acceso basado en rol (RBAC) de Azure también pueden aprovechar todas las funcionalidades de seguridad y administración del ciclo de vida que ofrece PIM de Azure AD.

Para obtener más información, consulte [PIM para recursos de Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** nueva característica  
**Categoría de servicio:** Revisiones de acceso  
**Funcionalidad del producto:** gobierno  

**Presentación de revisiones de acceso**


Las revisiones de acceso (versión preliminar) permiten a las organizaciones administrar de forma eficiente las pertenencias a grupos y el acceso a las aplicaciones empresariales: 

- Puede volver a certificar el acceso de usuario invitado mediante las revisiones de acceso sobre su acceso a aplicaciones y su pertenencia a grupos. La información que proporcionan las revisiones de acceso permite a los revisores decidir de manera eficiente si los invitados deben seguir teniendo acceso.

- Puede volver a certificar el acceso de los empleados a las aplicaciones y las pertenencias a grupos con las revisiones de acceso.

Puede recopilar los controles de revisiones de acceso en programas importantes para que su organización realice un seguimiento de las revisiones de aplicaciones vulnerables o de cumplimiento normativo.

Para obtener más información, consulte [Revisiones de acceso de Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** nueva característica  
**Categoría del servicio:** Mis aplicaciones  
**Funcionalidad del producto:** SSO  


**Ocultación de aplicaciones de terceros de Mis aplicaciones y del iniciador de Office 365**

Ahora puede administrar mejor las aplicaciones que se muestran en los portales de usuario a través de una nueva propiedad **hide app**. La ocultación de aplicaciones resulta útil en los casos en que los iconos de las aplicaciones se muestran para servicios de back-end o iconos duplicados y acaban bloqueando los iniciadores de aplicaciones del usuario. El botón de alternancia se encuentra en la sección de propiedades de la aplicación de terceros y presenta la etiqueta **Visible to user?** (¿Visible para el usuario?). También puede ocultar una aplicación mediante programación a través de PowerShell. 

Para obtener más información, consulte [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md) (Ocultar una aplicación de terceros de la experiencia del usuario en Azure Active Directory). 


**¿Qué está disponible?**

 Como parte de la transición a la nueva consola de administración, hay disponibles dos nuevas API para recuperar los registros de actividad de Azure AD. El nuevo conjunto de API proporciona más funciones de filtro y ordenación, así como actividades de auditoría e inicio de sesión. Los datos que estaban disponibles anteriormente a través de los informes de seguridad ahora son accesibles a través de la API de eventos de riesgo de Identity Protection en Microsoft Graph.


## <a name="september-2017"></a>Septiembre de 2017

**Tipo:** característica modificada  
**Categoría del servicio:** Microsoft Identity Manager  
**Funcionalidad del producto:** administración del ciclo de vida de las identidades  


**Revisión para Microsoft Identity Manager**

Un paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible desde el 25 de septiembre de 2017 para Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Este paquete acumulativo de revisiones:

- Resuelve problemas y agrega mejoras.
- Es una actualización acumulativa que reemplaza todas las actualizaciones de MIM 2016 SP1 hasta la compilación 4.4.1459.0 de Microsoft Identity Manager 2016. 
- Requiere que disponga de **Microsoft Identity Manager 2016, compilación 4.4.1302.0.** 

Para obtener más información, consulte [Paquete acumulativo de revisiones (compilación 4.4.1642.0) está disponible para Microsoft Identity Manager SP1 de 2016](https://support.microsoft.com/en-us/help/4021562). 

---
