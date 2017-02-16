---
title: "¿Qué es Azure Active Directory?"
description: Usar Azure Active Directory para ampliar sus identidades locales existentes en la nube o desarrollar aplicaciones integradas de Azure AD.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 21cd9fa752f6f3bf2e3aab572ad3336fbd2bee92
ms.openlocfilehash: 067bef695d8d6a340f7974478e6580b10a1e8dc8


---
# <a name="what-is-azure-active-directory"></a>¿Qué es Azure Active Directory?
Azure Active Directory (Azure AD) es el directorio basado en la nube multiempresa y el servicio de administración de identidades de Microsoft.

Para los administradores de TI, Azure AD proporciona una solución asequible y fácil de usar para dar a los empleados y asociados comerciales acceso de inicio de sesión único (SSO) a [miles de aplicaciones SaaS en la nube](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx) como Office365, Salesforce.com, DropBox y Concur.

Para los desarrolladores de aplicaciones, Azure AD permite al usuario centrarse en la creación de su aplicación facilitando y acelerando la integración de la misma con una solución de administración de identidades de clase mundial usada por millones de organizaciones de todo el mundo.

Azure AD también incluye una serie completa de capacidades de administración de identidades, incluida Multi-Factor Authentication, el registro de dispositivos, la administración de contraseñas de autoservicio, la administración de grupos de autoservicio, la administración de cuentas con privilegios, el control de acceso basado en roles, la supervisión del uso de aplicaciones, la auditoría enriquecida y la supervisión y alertas de seguridad. Estas funcionalidades pueden ayudar a proteger aplicaciones basadas en la nube, optimizar los procesos de TI, reducir costos y asegurar que se cumplen los objetivos de cumplimiento normativo corporativos.

Además, con solo [cuatro clics](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), Azure AD puede integrarse con un Windows Server Active Directory existente, lo que da a las organizaciones la capacidad de aprovechar sus inversiones en identidad locales existentes para administrar el acceso a aplicaciones SaaS basadas en la nube.

Si es un cliente de Office365, Azure o Dynamics CRM Online, es posible que no se dé cuenta de que ya está usando Azure AD. Cada inquilino de Office365, Azure y Dynamics CRM ya es en realidad un inquilino de Azure AD. ¡Siempre que lo desee, puede empezar a usar ese inquilino para administrar el acceso a miles de otras aplicaciones en la con las que se integra Azure AD!

![Pila de Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>¿Cuál es el grado de confiabilidad Azure AD?
El diseño multiempresa, distribuido geográficamente y de alta disponibilidad de Azure AD significa que puede confiar en él para sus necesidades empresariales más críticas. Ejecutándose en 28 centros de datos en todo el mundo con conmutación por error automatizada, tendrá la tranquilidad de saber que Azure AD es muy confiable y que incluso si un centro de datos deja de funcionar, hay copias de su directorio de datos activas en al menos dos centros de datos más dispersos por regiones y disponibles para acceso instantáneo.

Para más información, consulta los [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="what-are-the-benefits-of-azure-ad"></a>¿Cuáles son las ventajas de Azure AD?
Su organización puede usar Azure AD para mejorar la productividad de los empleados, optimizar los procesos de TI, mejorar la seguridad y reducir los costos de muchas formas:

* Adopte rápidamente servicios en la nube, proporcionando a los empleados y asociados una sencilla experiencia de inicio de sesión único gracias a las capacidades automatizadas de aprovisionamiento de servicios y administración del acceso a aplicaciones SaaS de Azure AD.
* Proporcione a los empleados acceso a aplicaciones en la nube de clase mundial y a funcionalidades de servicio y autoservicio desde dondequiera que necesiten para trabajar en los dispositivos que les encanta usar.
* Administre fácilmente y de forma segura el acceso de los empleados y proveedores a sus cuentas de medios sociales corporativas.
* Mejore la seguridad de las aplicaciones con la autenticación multifactor y acceso condicional de Azure AD.
* Implemente una administración de acceso a las aplicaciones constante y de autoservicio, proporcionando a los propietarios de empresas los medios para avanzar rápidamente, reduciendo al mismo tiempo los costos de TI y la sobrecarga.
* Supervise el uso de las aplicaciones y proteja su empresa contra amenazas avanzadas con informes y supervisión de seguridad.
* Acceso móvil (remoto) seguro a aplicaciones locales.

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>¿Cómo se comparan Azure AD con Active Directory Domain Services (AD DS) locales?

Azure Active Directory (Azure AD) y Active Directory local (Active Directory Domain Services o AD DS) son los sistemas que almacenan datos de directorios y administran la comunicación entre los usuarios y los recursos, incluidos los procesos de inicio de sesión de usuario, la autenticación y las búsquedas de directorios.

AD DS es un rol del servidor en Windows Server, lo que significa que se pueden implementar en máquinas físicas o virtuales. Tiene una estructura jerárquica basada en X.500. Utiliza DNS para localizar objetos, se puede interactuar usando LDAP y principalmente usa Kerberos para la autenticación. Active Directory habilita las unidades organizativas (OU) y los objetos de directiva de grupo (GPO), además de unir máquinas al dominio, y se crean confianzas entre dominios.

Azure AD es un servicio de directorio público de varios clientes, lo que significa que en Azure AD puede crear un inquilino para los servidores de la nube y aplicaciones como Office 365. Los usuarios y los grupos se crean en una estructura plana sin unidades organizativas ni objetos de directiva de grupo. La autenticación se realiza mediante protocolos como SAML, WS-Federation y OAuth. Es posible consultar Azure AD, pero en lugar de usar LDAP debe usar una API de REST denominada API Graph de AD. Todos funcionan mediante HTTP y HTTPS.

Puede usar Azure AD Connect para sincronizar las identidades locales con Azure AD.

### <a name="authentication-and-authorization-details"></a>Más detalles sobre la autenticación y la autorización

| Azure AD                                   | AD DS local |
|--------------------------------------------|-------------------|
| <li>   SAML      <li>   El certificado del proveedor de identidades de WS-Federation    <li>   Interactiva con credenciales admitidas  <li>   OAuth 2.0   <li>   OpenID Connect                          | <li>   SAML   <li>   El certificado del proveedor de identidades de WS-Federation  <li>   NTLM  <li>   Kerberos   <li>   MD5   <li>   Básica          |

### <a name="object-repository-details"></a>Detalles del repositorio de objetos

| Azure AD                                          | AD DS local |
|---------------------------------------------------|-------------------|
| Acceso a través de Azure AD Graph y Microsoft Graph | X.500 LDAP    |

### <a name="programmatic-access-details"></a>Más detalles sobre el acceso mediante programación

| Azure AD                        | AD DS local |
|---------------------------------|-------------------|
| API de REST de MS/Azure AD Graph | LDAP          |

### <a name="sso-to-applications-details"></a>Más detalles sobre SSO para aplicaciones

| Azure AD           | AD DS local   |
|--------------------|---------------------|
| <li>   OpenID Connect  <li>   SAML          | <li>   SAML      <li>   WS-Fed   <li>   OpenID Connect  |

### <a name="access-management-details"></a>Más detalles sobre la administración de acceso

| Azure AD         | AD DS local     |
|------------------------------------|------------------------------------------------------------------------|
| <li>   Ámbito definido por los recursos y control de acceso basado en rol   <li>   Permisos delegados definidos por el cliente y de aplicación        <li>   Marco de trabajo del consentimiento (exige el consentimiento de usuario/administrador apropiado de la forma definida/solicitada por el cliente y el recurso)  <li>   Mediante el rol de aplicación, se puede aplicar individualmente o a través de grupos, es compatible con:   <ul> <li>   Administración administrada       <li>   Acceso de la aplicación de autoservicio  <li>   Consentimiento del usuario </ul>         | <li>   Mediante ACL, se puede aplicar individualmente o a través de grupos, es compatible con:  <ul><li>   Administración administrada  </ul>                                                 |

### <a name="group-management-details"></a>Más detalles sobre la administración de grupos

| Azure AD                          | AD DS local                                 |
|-----------------------------------|---------------------------------------------------|
| <li>   Administración administrada    <li>   Regla/administración dinámica   <li>   Administración de grupos de autoservicio  | <li>   Administración administrada      <li>   Sistema externo (FIM u otros) necesario para:  <ul><li>   Regla/administración dinámica      </ul>                 |

### <a name="supported-credentials-details"></a>Más detalles sobre credenciales admitidas

| Azure AD           | AD DS local  |
|--------------------|--------------------|
| <li>   Nombre de usuario y contraseña  <li>   Tarjeta inteligente     | <li>   Nombre de usuario y contraseña  <li>   Tarjeta inteligente     |


## <a name="how-can-i-get-started"></a>¿Cómo puedo comenzar?
* Si es un administrador de TI:
  * [¡Pruébalo!](https://azure.microsoft.com/trial/get-started-active-directory/)  Puedes suscribirte a una evaluación gratuita de 30 días hoy e implementar la primera solución en la nube en menos de 5 minutos desde este vínculo
  * Lea nuestra Introducción a Azure AD para obtener sugerencias y trucos sobre cómo preparar un inquilino de Azure AD y tenerlo en funcionamiento rápidamente.
* Si es un desarrollador:
  * Consulta las [Instrucciones para desarrolladores](active-directory-developers-guide.md) de Azure Active Directory
  * [Inicie una evaluación](https://azure.microsoft.com/trial/get-started-active-directory/): suscríbase hoy mismo a una prueba gratuita de 30 días y comience a integrar sus aplicaciones con Azure AD.

## <a name="where-can-i-learn-more"></a>¿Dónde puedo obtener más información?
Tenemos una gran cantidad de excelentes recursos en línea para ayudarle a aprender todo sobre Azure AD. Aquí se proporciona una lista de artículos excelentes para comenzar:

* [Habilitación del directorio para la administración híbrida con Azure AD Connect](active-directory-aadconnect.md)
* [Seguridad adicional en un mundo conectado permanentemente](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introducción a los informes de Azure AD](active-directory-reporting-getting-started.md)
* [Administración de contraseñas desde cualquier lugar](active-directory-passwords.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)
* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [¿Qué es la licencia de Microsoft Azure Active Directory?](active-directory-licensing-what-is.md)
* [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md)



<!--HONumber=Nov16_HO4-->


