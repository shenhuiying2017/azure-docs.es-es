---
title: "Información general de Azure Active Directory Domain Services | Microsoft Docs"
description: "Información general de Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 2c00d324adfc1b71d436cb017214af98ff3002cf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services
## <a name="overview"></a>Información general
Los Servicios de infraestructura de Azure permiten implementar una amplia variedad de soluciones informáticas de una manera ágil. Con Azure Virtual Machines, puede realizar implementaciones casi instantáneas y pagar solo por minuto. Con el soporte técnico para Windows, Linux, SQL Server, Oracle, IBM, SAP y BizTalk, puede implementar cualquier carga de trabajo y cualquier lenguaje en casi cualquier sistema operativo. Estas ventajas permiten migrar aplicaciones heredadas que se implementan de forma local en Azure, con el fin de ahorrar en los gastos operativos.

Uno de los aspectos clave de la migración de aplicaciones locales a Azure es tratar con las necesidades de identidad de estas aplicaciones. Las aplicaciones que tienen en cuenta el directorio dependen de LDAP para el acceso de lectura o escritura al directorio corporativo, o bien se basan en la autenticación integrada de Windows (autenticación de Kerberos o NTLM) para autenticar a los usuarios finales. Las aplicaciones de línea de negocio (LOB) que se ejecutan en Windows Server se implementan normalmente en máquinas unidas a un dominio, de modo que se pueden administrar de forma segura mediante la directiva de grupo. Para subir y desplazar las aplicaciones locales a la nube, es necesario resolver estas dependencias de la infraestructura de identidad corporativa.

Para satisfacer las necesidades de la identidad de sus aplicaciones implementadas en Azure, los administradores suelen optar por una de las siguientes soluciones:

* Implemente una conexión VPN de sitio a sitio entre las cargas de trabajo que se ejecutan en los Servicios de infraestructura de Azure y el directorio corporativo local.
* Amplíe la infraestructura corporativa de dominio o bosque de AD mediante la configuración de controladores de dominio de réplica usando máquinas virtuales de Azure.
* Implemente un dominio independiente en Azure mediante controladores de dominio implementados como máquinas virtuales de Azure.

Todos estos enfoques presentan costo elevado y sobrecarga administrativa. Los administradores deben implementar controladores de dominio mediante máquinas virtuales en Azure. Además, tienen que administrar, proteger, supervisar y solucionar los problemas de estas máquinas virtuales, y también hacer una copia de seguridad de ellas y aplicarles revisiones. La dependencia de las conexiones VPN con el directorio local hace que las cargas de trabajo implementados en Azure sean vulnerables a interrupciones o problemas de red transitorios. Estas interrupciones de red, a su vez, dan lugar a un bajo tiempo de actividad y a una menor confianza en estas aplicaciones.

Hemos diseñado Azure AD Domain Services para proporcionar una alternativa mucho más sencilla.

### <a name="watch-an-introductory-video"></a>Vea el vídeo de introducción

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Presentación de los Servicios de dominio de Azure AD

Azure AD Domain Services proporciona servicios de dominio administrados como, por ejemplo, unión a un dominio, directiva de grupo, LDAP o autenticación Kerberos/NTLM, que son totalmente compatibles con Windows Server Active Directory. Puede consumir estos servicios de dominio, sin necesidad de implementar o administrar los controladores de dominio de la nube, ni de aplicarles revisiones. Azure AD Domain Services se integra con el inquilino de Azure AD existente, así los usuarios pueden iniciar sesión con sus credenciales corporativas. Además, se pueden usar los grupos y las cuentas de usuario existentes para proteger el acceso a los recursos, lo que garantiza una subida y un desplazamiento mejor de los recursos locales a los Servicios de infraestructura de Azure.

La funcionalidad de Azure AD Domain Services funciona perfectamente con independencia de si el inquilino de Azure AD es solo de nube o se sincroniza con su entorno de Active Directory local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Servicios de dominio de Azure AD para las organizaciones solo de nube

Un inquilino de Azure AD solo de nube (lo que a menudo se conoce como 'inquilinos administrados') carece de superficie de identidad local. En otras palabras, las cuentas de usuarios, sus contraseñas y pertenencias a grupos son todos nativos para la nube: se crean y administran en Azure AD. Considere por un momento que Contoso es un inquilino de Azure AD solo de nube. Como se muestra en la siguiente ilustración, el administrador de Contoso ha configurado una red virtual en los Servicios de infraestructura de Azure. Las aplicaciones y las cargas de trabajo de servidor se implementan en esta red virtual en máquinas virtuales de Azure. Puesto que Contoso es un inquilino solo de nube, todas las identidades de usuario, sus credenciales y pertenencias a grupos se crean y administran en Azure AD.

![Información general de los Servicios de dominio de Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

El administrador de TI de Contoso puede habilitar los Servicios de dominio de Azure AD para su inquilino de Azure AD y elegir que estos estén disponibles en esta red virtual. Después, Azure AD Domain Services aprovisiona un dominio administrado y permiten que esté disponible en la red virtual. Todas las cuentas de usuario, pertenencias a grupos y credenciales de usuario disponibles en el inquilino de Azure AD de Contoso también están disponibles en este dominio recién creado. Esta característica permite a los usuarios de la organización iniciar sesión en el dominio con sus credenciales corporativas; por ejemplo, al conectarse de forma remota a las máquinas unidas al dominio a través de Escritorio remoto. Los administradores pueden proporcionar acceso a los recursos del dominio mediante la pertenencia a grupos existente. Las aplicaciones implementadas en las máquinas virtuales dentro de la red virtual pueden usar características como, por ejemplo, unión a un dominio, lectura LDAP, enlace LDAP, autenticación NTLM y Kerberos, y directiva de grupo.

Algunos aspectos destacables del dominio administrado que aprovisionan los Servicios de dominio de Azure AD son los siguientes:

* El administrador de TI de Contoso no necesita administrar ni supervisar este dominio ni ningún controlador de dominio de este dominio administrado, ni tampoco aplicarles revisiones.
* No es necesario administrar la replicación de AD en este dominio. Las cuentas de usuario, las pertenencias a grupos y las credenciales del inquilino de Azure AD de Contoso están disponibles automáticamente dentro de este dominio administrado.
* Dado que los Servicios de dominio de Azure AD administran el dominio, el administrador de TI de Contoso no tiene privilegios de administrador de dominio o administrador de organización.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Servicios de dominio de Azure AD para organizaciones híbridas
Las organizaciones con una infraestructura de TI híbrida consumen una combinación de recursos de nube y locales. Tales organizaciones sincronizan la información de identidad de su directorio local con su inquilino de Azure AD. Como las organizaciones híbridas buscan migrar más de sus aplicaciones locales a la nube, en especial las aplicaciones heredadas que tienen en cuenta el directorio, Azure AD Domain Services puede resultarles muy útiles.

Litware Corporation ha implementado [Azure AD Connect](../active-directory/active-directory-aadconnect.md), con el fin de sincronizar la información de identidad de su directorio local con su inquilino de Azure AD. La información de identidad que se sincroniza incluye las cuentas de usuario, sus valores de hash de credenciales para la autenticación (sincronización de contraseñas) y las pertenencias a grupos.

> [!NOTE]
> **La sincronización de contraseñas es obligatoria para las organizaciones híbridas para usar Azure AD Domain Services**. El motivo de este requisito es que se necesitan las credenciales de los usuarios en el dominio administrado proporcionado por Azure AD Domain Services para autenticar a estos usuarios mediante los métodos de autenticación NTLM o Kerberos.
>
>

![Servicios de dominio de Azure AD para Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

La ilustración anterior muestra cómo las organizaciones con una infraestructura de TI híbrida, como Litware Corporation, pueden usar Azure AD Domain Services. Las aplicaciones y cargas de trabajo de servidor de Litware que requieren servicios de dominio, se implementan en una red virtual en los Servicios de infraestructura de Azure. El administrador de TI de Litware puede habilitar los Servicios de dominio de Azure AD para su inquilino de Azure AD y elegir que esté disponible un dominio administrado en esta red virtual. Puesto que Litware es una organización con una infraestructura de TI híbrida, las cuentas de usuario, los grupos y las credenciales se sincronizan con su inquilino de Azure AD desde su directorio local. Esta característica permite a los usuarios iniciar sesión en el dominio con sus credenciales corporativas; por ejemplo, al conectarse de forma remota a las máquinas unidas al dominio a través de Escritorio remoto. Los administradores pueden proporcionar acceso a los recursos del dominio mediante la pertenencia a grupos existente. Las aplicaciones implementadas en las máquinas virtuales dentro de la red virtual pueden usar características como, por ejemplo, unión a un dominio, lectura LDAP, enlace LDAP, autenticación NTLM y Kerberos, y directiva de grupo.

Algunos aspectos destacables del dominio administrado que aprovisionan los Servicios de dominio de Azure AD son los siguientes:

* El dominio administrado es un dominio independiente. No es una extensión del dominio local de Litware.
* El administrador de TI de Litware no necesita administrar ni supervisar controladores de dominio de este dominio administrado, ni tampoco aplicarles revisiones.
* No es necesario administrar la replicación de AD en este dominio. Las cuentas de usuario, las pertenencias a grupos y las credenciales del directorio local de Litware se sincronizan con Azure AD mediante Azure AD Connect. Estas cuentas de usuario, las pertenencias a grupos y las credenciales están disponibles automáticamente dentro de este dominio administrado.
* Dado que los Servicios de dominio de Azure AD administran el dominio, el administrador de TI de Litware no tiene privilegios de administrador de dominio o administrador de organización.

## <a name="benefits"></a>Ventajas
Con los Servicios de dominio de Azure AD, puede disfrutar de las siguientes ventajas:

* **Simple** : puede satisfacer las necesidades de identidad de las máquinas virtuales implementadas en los servicios de infraestructura de Azure con unos pocos clics. No es necesario implementar y administrar la infraestructura de identidades en Azure o volver a la configuración de conectividad en su infraestructura de identidad local.
* **Integración** : los Servicios de dominio de Azure AD están profundamente integrados con su inquilino de Azure AD. Ahora puede usar Azure AD como un directorio de integración empresarial basado en la nube que satisface tanto las necesidades de sus aplicaciones modernas y las aplicaciones tradicionales que tienen en cuenta el directorio.
* **Compatible** : Azure AD Domain Services se basa en la infraestructura de grado empresarial comprobada de Windows Server Active Directory. Por tanto, las aplicaciones pueden depender de un mayor grado de compatibilidad con características de Windows Server Active Directory. No todas las características disponibles en Windows Server AD están actualmente disponibles en Azure AD Domain Services. Sin embargo, las características disponibles son compatibles con las características de Windows Server AD correspondientes en las que confía en su infraestructura local. Las funcionalidades de LDAP, Kerberos, NTLM, directiva de grupo y unión a un dominio constituyen una oferta acabada que se ha probado y perfeccionado a través de diversas versiones de Windows Server.
* **Rentabilidad** : con los Servicios de dominio de Azure AD, puede evitar el inconveniente de administración e infraestructura asociado con la administración de infraestructuras de identidad compatibles con las aplicaciones tradicionales que tienen en cuenta el directorio. Puede mover estas aplicaciones a los Servicios de infraestructura de Azure y beneficiarse de mayores ahorros en los gastos operativos.


## <a name="next-steps"></a>pasos siguientes
### <a name="learn-more-about-azure-ad-domain-services"></a>Más información sobre Azure AD Domain Services
* [Características](active-directory-ds-features.md)
* [Escenarios de implementación](active-directory-ds-scenarios.md)
* [Averiguar si Azure AD Domain Services se adapta a sus casos de uso](active-directory-ds-comparison.md)
* [Información sobre la sincronización de Azure AD Domain Services con el directorio Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introducción a Azure AD Domain Services
* [Habilitación de Azure AD Domain Services mediante Azure Portal](active-directory-ds-getting-started.md)
