---
title: "Conexión de Active Directory con Azure Active Directory | Microsoft Docs"
description: "Azure AD Connect integrará sus directorios locales con Azure Active Directory. Esto le permite proporcionar una identidad común para las aplicaciones de Office 365, Azure y SaaS integradas con Azure AD."
keywords: "introducción a Azure AD Connect, información general de Azure AD Connect, qué es Azure AD Connect, instalación de active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: billmath
ms.openlocfilehash: 83654a406019c4ac3837a3e961a6de640c98f31c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Integración de los directorios locales con Azure Active Directory
Azure AD Connect integrará sus directorios locales con Azure Active Directory. Esto le permite proporcionar una identidad común para los usuarios de aplicaciones de Office 365, Azure y SaaS integradas con Azure AD. Este tema le guiará a través del planeamiento, implementación y pasos de la operación. Se ofrece una colección de vínculos a temas relacionados con esta área.

> [!IMPORTANT]
> [Azure AD Connect es la mejor manera de conectar su directorio local con Azure AD y Office 365. Es una ocasión ideal para realizar la actualización a Azure AD Connect desde la sincronización de Microsoft Azure Active Directory (DirSync) o desde la sincronización de Azure AD, ya que estas herramientas están en desuso y no se ofrece soporte técnico para ellas desde el 13 de abril de 2017.](active-directory-aadconnect-dirsync-deprecated.md)
> 
> 

![Qué es Azure AD Connect](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>¿Por qué usar Azure AD Connect?
la integración de directorios locales con Azure AD hace que los usuarios sean más productivos proporcionando una identidad común para tener acceso a recursos de nube y locales. Los usuarios y las organizaciones pueden aprovechar lo siguiente:

* Los usuarios pueden usar una única identidad para acceder a aplicaciones locales y servicios en la nube, como Office 365.
* Una sola herramienta que proporciona una experiencia de implementación simplificada para la sincronización y el inicio de sesión.
* Proporciona las funcionalidades más novedosas para sus escenarios de trabajo. Azure AD Connect sustituye a las versiones anteriores de las herramientas de integración de identidades, como DirSync y Sincronización de Azure AD. Para más información, consulte [Identidades híbridas: comparación de las herramientas para la integración de directorios de identidades híbridas](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Cómo funciona Azure AD Connect
Azure Active Directory Connect tiene tres componentes principales: los servicios de sincronización, el componente opcional de los servicios de federación de Active Directory y el componente de supervisión denominado [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Pila de Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Sincronización: este componente es responsable de la creación de usuarios, grupos y otros objetos. También es responsable de asegurarse de que la información de identidad de los usuarios y los grupos de su entorno local coincide con la de la nube.
* AD FS: la federación es una parte opcional de Azure AD Connect y puede utilizarse para configurar un entorno híbrido mediante una infraestructura local de AD FS. Las organizaciones pueden utilizar esto para abordar implementaciones complejas como un inicio de sesión único de unión a dominio, la aplicación de la directiva de inicio de sesión de AD y MFA mediante tarjeta inteligente o de terceros.
* Supervisión de estado: Azure AD Connect Health puede ofrecer una sólida supervisión y proporcionar una ubicación central en el Portal de Azure para ver esta actividad. Para más información, consulte [Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Instalación de Azure AD Connect
Puede encontrar la descarga de Azure AD Connect en el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).

| Solución | Escenario |
| --- | --- |
| Antes de empezar: [Requisitos previos de Azure AD Connect](active-directory-aadconnect-prerequisites.md) |<li>Pasos que hay que completar antes de iniciar la instalación de Azure AD Connect.</li> |
| [Configuración rápida](active-directory-aadconnect-get-started-express.md) |<li>Si tiene un único bosque de AD se recomienda que use esta opción.</li> <li>Inicio de sesión de usuario con la misma contraseña mediante la sincronización de contraseñas.</li> |
| [Configuración personalizada](active-directory-aadconnect-get-started-custom.md) |<li>Se usa cuando se tienen varios bosques. Admite muchas [topologías](active-directory-aadconnect-topologies.md) locales.</li> <li>Personalice la opción de inicio de sesión, como ADFS para la federación, o bien use un proveedor de identidades de terceros.</li> <li>Personalice las características de sincronización, como el filtrado y la escritura diferida.</li> |
| [Actualización desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Se usa cuando se tiene un servidor de DirSync existente que ya se está ejecutando.</li> |
| [Actualización desde Azure AD Sync o Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Hay varios métodos diferentes entre los que puede elegir en función de sus preferencias.</li> |

[Después de la instalación](active-directory-aadconnect-whats-next.md) , debe comprobar que funciona según lo previsto y asignar licencias a los usuarios.

### <a name="next-steps-to-install-azure-ad-connect"></a>Pasos siguientes para instalar Azure AD Connect
|Tema. |Vínculo|  
| --- | --- |
|Descarga de Azure AD Connect | [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalación mediante configuración rápida | [Instalación rápida de Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalación mediante configuración personalizada | [Instalación personalizada de Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Actualización desde DirSync | [Actualización desde la herramienta de sincronización de Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Después de la instalación | [Comprobación de la instalación y asignación de licencias ](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Más información sobre la instalación de Azure AD Connect
También quiere prepararse para los problemas [operativos](active-directory-aadconnectsync-operations.md) . Puede que desee tener un servidor independiente que le permita conmutar por error fácilmente en caso de [desastre](active-directory-aadconnectsync-operations.md#disaster-recovery). Si planea realizar cambios frecuentes en la configuración, debe preparar un servidor en [modo provisional](active-directory-aadconnectsync-operations.md#staging-mode) .

|Tema. |Vínculo|  
| --- | --- |
|Topologías admitidas | [Topologías de Azure AD Connect](active-directory-aadconnect-topologies.md)|
|Conceptos de diseño | [Conceptos de diseño de Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Cuentas usadas para la instalación | [Más información sobre las credenciales y los permisos de Azure AD Connect](./active-directory-aadconnect-accounts-permissions.md)|
|Planeación operativa | [Sincronización de Azure AD Connect: tareas y consideraciones operativas](active-directory-aadconnectsync-operations.md)|
|Opciones de inicio de sesión de usuario | [Opciones para el inicio de sesión de los usuarios en Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Configuración de características de sincronización
Azure AD Connect incluye varias características que puede activar de manera opcional o que están habilitadas de forma predeterminada. En algunas ocasiones, ciertas características pueden requerir configuración adicional en determinados escenarios y topologías.

[filtrado](active-directory-aadconnectsync-configure-filtering.md) se usa cuando se quieren limitar los objetos que se sincronizan con Azure AD. De forma predeterminada todos los usuarios, contactos, grupos y equipos de Windows 10 están sincronizados. Puede cambiar el filtrado basado en dominios, unidades organizativas o atributos.

[sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) sincroniza el hash de contraseña en Active Directory con Azure AD. El usuario final puede usar la misma contraseña de forma local y en la nube, pero solo puede administrarla en una ubicación. Como se usa su Active Directory local como autoridad, también podrá usar su propia directiva de contraseñas.

[escritura diferida de contraseñas](../active-directory-passwords-getting-started.md) permite a los usuarios cambiar y restablecer sus contraseñas en la nube y aplicar su directiva local de contraseñas.

[reescritura de dispositivos](active-directory-aadconnect-feature-device-writeback.md) permite que un dispositivo registrado en Azure AD se reescriba en Active Directory local para que se pueda usar para el acceso condicional.

La característica para [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) está activada de forma predeterminada y protege su directorio en la nube de muchas eliminaciones al mismo tiempo. De forma predeterminada permite 500 eliminaciones por cada ejecución. Puede cambiar esta configuración según el tamaño de la organización.

[actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) está habilitada de forma predeterminada para las instalaciones de configuración rápida y garantiza que Azure AD Connect está siempre actualizado con la versión más reciente.

### <a name="next-steps-to-configure-sync-features"></a>Pasos siguientes para configurar características de sincronización
|Tema. |Vínculo|  
| --- | --- |
|Configuración del filtrado | [Azure AD Connect Sync: configuración del filtrado](active-directory-aadconnectsync-configure-filtering.md)|
|sincronización de contraseñas | [Sincronización de Azure AD Connect: implementación de la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md)|
|escritura diferida de contraseñas | [Introducción a la administración de contraseñas](../active-directory-passwords-getting-started.md)|
|Escritura diferida de dispositivos | [Habilitación de escritura diferida de dispositivos en Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|evitar eliminaciones accidentales | [Sincronización de Azure AD Connect: cómo evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Actualización automática | [Azure AD Connect: Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Personalización de Azure AD Connect Sync
Azure AD Connect Sync incluye una configuración predeterminada diseñada para funcionar en la mayoría de clientes y topologías. Pero siempre hay situaciones en las que la configuración predeterminada no funciona y debe ajustarse. Se permiten los cambios que se documentan en esta sección y en los temas vinculados.

Si no ha trabajado con una topología de sincronización antes, querrá comenzar a comprender los aspectos básicos y los términos usados que se describen en los [conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect es la evolución de MIIS2003, ILM2007 y FIM2010. Aunque algunas cosas son idénticas, muchas también han cambiado.

En la [configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md) se considera que puede haber más de un bosque. En esas topologías un objeto de usuario se puede representar como un contacto de otro bosque. El usuario puede tener también vinculado un buzón en otro bosque de recursos. El comportamiento de la configuración predeterminada se describe en la sección sobre [usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

El modelo de configuración sincronizado se denomina [aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Los flujos de atributos avanzados usan [funciones](active-directory-aadconnectsync-functions-reference.md) para expresar transformaciones de atributo. Puede ver y examinar toda la configuración con las herramientas que se incluyen en Azure AD Connect. Si tiene que realizar cambios en la configuración, asegúrese de seguir los [procedimientos recomendados](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) para que sea más fácil adoptar nuevas versiones.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Pasos siguientes para personalizar Azure AD Connect Sync
|Tema. |Vínculo|  
| --- | --- |
|Todos los artículos de sincronización de Azure AD Connect | [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md)|
|conceptos técnicos | [Azure AD Connect Sync: conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md)|
|Introducción a la configuración predeterminada | [Azure AD Connect Sync: descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Descripción de usuarios y contactos | [Azure AD Connect Sync: descripción de usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|aprovisionamiento declarativo | [Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Cambio de la configuración predeterminada | [Prácticas recomendadas de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Configuración de características de federación

Azure AD Connect proporciona varias características que simplifican la federación con Azure AD mediante AD FS y la administración de la confianza de federación. Azure AD Connect es compatible con AD FS en Windows Server 2012R2 o posterior.

[Actualice el certificado SSL de la granja de AD FS](active-directory-aadconnectfed-ssl-update.md) incluso si no usa Azure AD Connect para administrar la confianza de federación.

[Agregue un servidor de AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) a la granja para expandirla según sea necesario.

[Repare la confianza](active-directory-aadconnect-federation-management.md#repairthetrust) con Azure AD en unos pocos pasos sencillos.

Se puede configurar AD FS para que admita [varios dominios](active-directory-aadconnect-multiple-domains.md). Por ejemplo, puede tener varios dominios principales que necesita utilizar para la federación.

Si no se configuró el servidor de AD FS para actualizar automáticamente los certificados de Azure AD o si utiliza una solución que no es AD FS, se le notificará cuando tenga que [actualizar los certificados](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Pasos siguientes para configurar características de federación
|Tema. |Vínculo|  
| --- | --- |
|Todos los artículos de AD FS | [Azure AD Connect y la federación](active-directory-aadconnectfed-whatis.md)|
|Configuración de AD FS con subdominios | [Compatibilidad con varios dominios para la federación con Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Administrar una granja de servidores de AD FS | [Administración y personalización de AD FS con Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Actualización manual de certificados de federación | [Renovación de certificados de federación para Office 365 y Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Más información y referencias
|Tema. |Vínculo|  
| --- | --- |
|Historial de versiones | [Historial de versiones](active-directory-aadconnect-version-history.md)|
|Comparación de DirSync, Sincronización de Azure AD y Azure AD Connect | [Comparación de las herramientas para la integración de directorios](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Lista de compatibilidad no de AD FS para Azure AD | [Lista de compatibilidad de federación de AD Azure](active-directory-aadconnect-federation-compatibility.md)|
|Configuración de un IdP de SAML 2.0|[Uso de un proveedor de identidades (IdP) de SAML 2.0 para el inicio de sesión único](active-directory-aadconnect-federation-saml-idp.md)|
|Atributos sincronizados | [Atributos sincronizados](active-directory-aadconnectsync-attributes-synchronized.md)|
|Supervisión del uso de Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Preguntas frecuentes | [Preguntas más frecuentes sobre Azure AD Connect](active-directory-aadconnect-faq.md)|

**Recursos adicionales**

Presentación de Ignite 2015 sobre cómo extender los directorios locales a la nube.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

