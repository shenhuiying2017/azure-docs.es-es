---
title: "Implementación de la guía POC de Azure Active Directory | Microsoft Docs"
description: "Explorar e implementar rápidamente escenarios de Administración de identidades y acceso"
services: active-directory
keywords: "azure active directory, guía, Prueba de concepto, POC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Guía Prueba de concepto de Azure Active Directory: Implementación | Microsoft Docs

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation: sincronización de AD con Azure AD 

Una identidad híbrida es la base de la mayoría de los clientes empresariales que ya disponen de un directorio local. El objetivo es, deliberadamente, pasar el menos tiempo posible aquí para mostrar el valor de los escenarios de acceso e identidad reales. 

| Escenario | Bloques de creación| 
| --- | --- |  
| [Extensión de la identidad local a la nube](#extending-your-on-premises-identity-to-the-cloud) | [Sincronización de directorios: sincronización de hash de contraseñas](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Nota**: Si ya dispone de DirSync/ADSync o versiones anteriores de Azure AD Connect, este paso es opcional. Puede que algunos escenarios de esta guía requieran una versión más reciente de Azure AD Connect.  <br/>[Personalización de marca](active-directory-playbook-building-blocks.md#branding) | 
| [Asignación de licencias de Azure AD mediante grupos](#assigning-azure-ad-licenses-using-groups) | [Licencias basadas en grupos](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Extensión de la identidad local a la nube 

1. Bob es el administrador de Active Directory de Contoso. Tiene que habilitar la identidad como servicio para un conjunto de usuarios. Después de la ejecución del asistente de Azure AD Connect, la identidad de los usuarios de destino está disponible en la nube. 
2. Bob pide a Susie, uno de los usuarios de destino, que acceda al panel de acceso de Azure Active Directory y confirme que puede autenticarse. Susie ve una página de inicio de sesión con la marca y un panel de acceso vacío que está preparado para habilitar el acceso futuro a la aplicación.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Asignación de licencias de Azure AD mediante grupos 

1. Bob es el administrador global de Azure AD y quiere asignar licencias de Azure AD a un conjunto concreto de usuarios como parte del lanzamiento inicial de Azure AD.
2. Bob crea un grupo para los usuarios piloto. 
3. Bob asigna las licencias al grupo.
4. Susie, una de los trabajadores de la información, se agrega al grupo de seguridad como parte de sus funciones de trabajo.
5. Transcurrido algún tiempo, Susie tiene acceso a la licencia de Azure AD Premium. Esto habilitará algunos otros escenarios de POC más adelante.

## <a name="theme---lots-of-apps-one-identity"></a>Tema: muchas aplicaciones y una sola identidad

| Escenario | Bloques de creación| 
| --- | --- |  
| [Integración de aplicaciones SaaS: SSO federado](#integrate-saas-applications---federated-sso) | [Configuración de SSO federado en SaaS](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Grupos: propiedad delegada](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integración de aplicaciones SaaS: SSO con contraseña](#integrate-saas-applications---password-sso) | [Configuración de SSO con contraseña en SaaS](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Eventos de ciclo de vida de identidad y SSO](#sso-and-identity-lifecycle-events) | [Ciclo de vida de identidad y SaaS](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Acceso seguro a cuentas compartidas](#secure-access-to-shared-accounts) | [Configuración de cuentas compartidas de SaaS](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Acceso remoto seguro a aplicaciones locales](#secure-remote-access-to-on-premises-applications) | [Configuración de proxy de aplicación](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Sincronización de identidades LDAP en Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Configuración genérica del conector LDAP](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integración de aplicaciones SaaS: SSO federado 

1. Bob es el administrador global de Azure AD y recibe una solicitud del departamento de Marketing para que habilite acceso a su instancia de ServiceNow. Bob busca el tutorial paso a paso en la documentación de Azure AD y lo sigue, y delega la asignación de usuarios a la aplicación en Kevin, el jefe del equipo de Marketing. 
2. Kevin inicia sesión como propietario de los derechos de ServiceNow y asigna a Susie a la aplicación. Kevin advierte también que el perfil de Susie se ha creado automáticamente en ServiceNow.
3. Susie es una trabajadora de la información del departamento de Marketing. Inicia sesión en Azure AD y busca todas las aplicaciones SaaS a las que está asignada en el portal Mis aplicaciones. A partir de ese momento, obtiene acceso sin problemas a ServiceNow.
4. El departamento de Marketing quiere auditar quién accede a ServiceNow. Bob descarga un informe de actividad y lo comparte con Kevin por correo electrónico.  

### <a name="sso-and-identity-lifecycle-events"></a>Eventos de ciclo de vida de identidad y SSO

1. Susie toma un permiso para ausentarse y, mediante la directiva corporativa, la cuenta de AD local se deshabilita temporalmente. Susie no puede acceder ahora en Azure AD y, por lo tanto, no puede acceder a ServiceNow. 
2. Susie se traslada de Marketing a Ventas. Kevin quia su acceso de ServiceNow. Susie inicia sesión en Mis aplicaciones de Azure AD y ya no ve el icono de ServiceNow. Diez minutos después, Kevin confirma que la cuenta de Susie se ha deshabilitado en la consola de administración de ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integración de aplicaciones SaaS: SSO con contraseña

1. Bob configura el acceso al HipChat de Atlassian. HipChat tiene integración de SSO con contraseña y concede acceso a Susie.
2. Susie inicia sesión en el portal Mis aplicaciones y ve un vínculo para descargar la extensión del explorador IE en Azure AD, que descarga.
3. Después de hacer clic en el vínculo, se le piden sus credenciales de nombre de usuario y contraseña de HipChat. Se trata de una operación de una sola vez y, tras completarla, tiene acceso HipChat.
4. Unos días después, Susie abre el portal Mis aplicaciones y hace clic de nuevo en HipChat. En esta ocasión, obtiene acceso sin problemas.
5. Kevin, el propietario de la aplicación HipChat, quiere auditar quién accede a la aplicación. Bob descarga un informe de auditoría y lo comparte con Kevin por correo electrónico. 

### <a name="secure-access-to-shared-accounts"></a>Acceso seguro a cuentas compartidas 

1. Bob tiene como tarea proteger el nombre de usuario de Twitter compartido para los miembros del equipo de Ventas. Agrega Twitter como aplicación de SSO y la asigna al grupo de seguridad del equipo de Ventas. Ha recibido las credenciales para la cuenta compartida y las especifica en el sistema. 
2. Compartir las credenciales de Twitter ya no es de confianza debido a que muchas personas las conocen. Bob habilita la sustitución automática de la contraseña de Twitter.
3. Susie, miembro del equipo de Ventas, inicia sesión en el portal Mis aplicaciones y ve un vínculo para descargar la extensión del explorador IE en Azure AD. La instala.
4. Después de hacer clic en el vínculo, obtiene acceso directamente a Twitter. Ella no conoce la contraseña.
5. Arnold forma parte también del equipo de Ventas. Tiene la misma experiencia que Susie en los pasos 3 y 4.
6. El departamento de Ventas quiere auditar quién accede a Twitter. Bob descarga un informe de actividad y lo comparte con Kevin por correo electrónico. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Acceso remoto seguro a aplicaciones locales

1. Bob, el administrador global de Azure AD, ha recibido numerosas solicitudes para que permita que los empleados accedan a varios recursos locales útiles tales como la aplicación de gastos, mientras trabajan de forma remota. Sigue la [documentación del proxy de aplicación](active-directory-application-proxy-enable.md) para instalar un conector y publicar Gastos como aplicación del proxy de aplicación. 
2. Bob comparte la URL de la aplicación Gastos con Susie, una de los empleados que necesita acceso remoto. Ella accede al vínculo y, después de autenticarse en AAD, puede acceder a la aplicación Gastos y seguir siendo productiva mientras esté en una ubicación remota. 
3. Bob continúa luego publicando más aplicaciones locales siguiendo el mismo proceso y proporcionado acceso a los usuarios cuando sea necesario. Agrega acceso condicional y Multi-Factor Authentication para las aplicaciones más sensibles que publica, para garantizar mayor seguridad.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Sincronización de identidades LDAP en Azure AD

1. La compañía de Bob tiene una infraestructura de identidades compleja. La mayoría de los usuarios se mantienen dentro de Windows Server Active Directory Domain Services (ADDS). Algunos de ellos se administran en el sistema RR. HH que se encuentra en Active Directory Lightweight Directory Services (ADLDS).
2. Bob se encarga de habilitar acceso a las aplicaciones SaaS para todos los usuarios (también los que no están presentes en ADDS).
3. Bob configura el conector LDAP genérico para extraer datos de ADLDS en Azure AD Connect.
4. Bob crea reglas de sincronización para que los usuarios de LDAP se rellenen en metaverso y en Azure AD.
5. Susie, como usuario de LDAP, acede a su aplicación SaaS con identidad sincronizada.



> [!IMPORTANT] 
> Se trata de una configuración avanzada que requiere cierta familiaridad con FIM o MIM. Si se usa en producción, se recomienda plantear las preguntas sobre esta configuración a través de [soporte técnico Premier](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Tema: aumento de la seguridad 

| Escenario | Bloques de creación| 
| --- | --- |  
| [Acceso seguro a la cuenta de administrador](#secure-administrator-account-access) | [Azure MFA con llamadas telefónicas](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Acceso seguro a aplicaciones](#secure-access-to-applications) | [Acceso condicional para aplicaciones SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Habilitar administración Just-In-Time (JIT)](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Protección de identidades basadas en riesgo](#protect-identities-based-on-risk) | [Detección de eventos de riesgo](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Implementación de directivas de riesgo de inicio de sesión](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Autenticación sin contraseñas mediante autenticación basada en certificados](#authenticate-without-passwords-using-certificate-based-authentication) | [Configuración de autenticación basada en certificados](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Acceso seguro a la cuenta de administrador

1. Bob es el administrador global de Azure AD. Ha designado a Stuart como coadministrador del servicio. 
2. Bob configura la cuenta de Stuart para que siempre exija MFA para mejorar la posición de seguridad.
3. Stuart inicia sesión en Azure Portal y se da cuenta de que tiene que registrar su número de teléfono para proseguir con el inicio de sesión.
4. Los inicios de sesión posteriores de Stuart están ahora protegidos con Multi-Factor Authentication, y él ahora recibe una llamada telefónica para verificar su identidad.

### <a name="secure-access-to-applications"></a>Acceso seguro a aplicaciones

1. Kevin es el propietario de la empresa ServiceNow. La compañía quiere ahora que los usuarios inicien sesión con MFA cuando acedan desde el exterior de la red corporativa.
2. Bob, nuestro administrador global de Azure AD, agrega una directiva de acceso condicional a la aplicación ServiceNow que habilita MFA para el acceso exterior.
3. Susie, nuestra trabajadora de la información, inicia sesión en el portal Mis aplicaciones y hace clic en el icono de ServiceNow. Ahora tiene que enfrentarse a MFA.

### <a name="enable-just-in-time-jit-administration"></a>Habilitar administración Just-In-Time (JIT)

1. Bob y Stuart son administradores globales de Azure AD. Quieren habilitar acceso JIT a los roles de administración y también mantener registros sobre el uso de los roles con privilegios.
2. Bob habilita PIM en el inquilino de Azure AD y se convierte en el administrador de seguridad. Cambia su pertenencia y la de Stuart al rol de administrador global de permanente a apto.
3. Bob y Stuart tienen ahora que activar su rol en Azure Portal antes de efectuar ningún cambio en la configuración de Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Protección de identidades basadas en riesgo 

1. Susie, trabajadora de la información, intenta iniciar sesión en un navegador TOR. 
2. Bob consulta el panel de Azure AD Identity Protection y ve el inicio de sesión de Susie desde una dirección IP anónima. El equipo de seguridad quiere combatir el acceso de estos usuarios con MFA.
3. Bob habilita una directiva de Azure AD Identity Protection para que MFA impida eventos de riesgo medio y alto.
4. Pasado un tiempo, Susie inicia sesión de nuevo desde un navegador Tor. Esta vez, verá el desafío de MFA.

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Autenticación sin contraseñas mediante autenticación basada en certificados

1. Bob es administrador global de una entidad financiera que prohíbe el uso de contraseñas como elemento de autenticación en sus aplicaciones.
2. Bob habilita y exige la autenticación de certificado en ADFS y en Azure AD.
3. Cuando Susie intenta acceder a la aplicación se le pide que se autentique con un certificado.

## <a name="theme---scale-with-self-service"></a>Tema: escala con autoservicio

| Escenario | Bloques de creación| 
| --- | --- |  
| [Restablecimiento de contraseñas de autoservicio](#self-service-password-reset) | [Restablecimiento de contraseñas de autoservicio](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Acceso de autoservicio a aplicaciones](#self-service-access-to-applications) | [Acceso de autoservicio a aplicaciones](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Restablecimiento de contraseñas de autoservicio 

1. Bob es el administrador global de Azure AD y habilita la administración de contraseñas de autoservicio para un conjunto de usuarios, entre los que se encuentra Susie. 
2. Susie inicia sesión en el portal Mis aplicaciones y ve un mensaje que le indica que registre su información de seguridad para futuros eventos de restablecimiento de contraseña.
3. Días después, Susie olvida su contraseña y la restablece a través del portal de Azure AD.

### <a name="self-service-access-to-applications"></a>Acceso de autoservicio a aplicaciones 

1. Kevin es el propietario de la empresa ServiceNow. Quiere que los usuarios se "registren" en ella a petición, en lugar de agregalos de una vez.
2. Bob, nuestro administrador global de Azure AD, modifica la aplicación ServiceNow para habilitar solicitudes de autoservicio.
3. Susie, nuestra trabajadora de la información, inicia sesión en el portal Mis aplicaciones y hace clic en el botón "Add more applications" (Agregar más aplicaciones) y ve ServiceNow como una de las aplicaciones recomendadas. Luego vuelve al portal Mis aplicaciones y ve la aplicación ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]