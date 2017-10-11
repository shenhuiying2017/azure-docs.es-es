---
title: "Características de Azure Active Directory Domain Services | Microsoft Docs"
description: "Características de Servicios de dominio de Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 8005be7ded6ea005af086aeaf594963a5f2d4ac2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Características
Las siguientes características están disponibles en los dominios administrados de Azure AD Domain Services.

* **Experiencia de implementación sencilla:** puede habilitar Servicios de dominio de Azure AD para el inquilino de Azure AD mediante unos cuantos clics. Independientemente de si el inquilino de Azure AD es un inquilino en la nube o sincronizado con el directorio local, el dominio administrado se puede aprovisionar rápidamente.
* **Compatibilidad para la unión a dominio:** le resultará muy fácil unir a dominios equipos de la red virtual de Azure en la que está disponible el dominio administrado. La experiencia de unión a dominio en sistemas operativos de cliente y servidor de Windows funciona sin problemas en dominios ofrecidos por Azure AD Domain Services. También puede utilizar herramientas automatizadas de unión a dominio en esos dominios.
* **Instancia de un dominio por directorio de Azure AD:** puede crear un único dominio de Active Directory para cada directorio de Azure AD.
* **Crear dominios con nombres personalizados:** puede crear dominios con nombres personalizados (por ejemplo, contoso100.local) mediante Azure AD Domain Services. Puede utilizar nombres de dominio comprobados o sin comprobar. Si lo desea, también puede crear un dominio con el sufijo de dominio integrado (es decir, *.onmicrosoft.com) que se ofrece a través del directorio de Azure AD.
* **Integración con Azure AD:** no es necesario configurar o administrar la replicación en Servicios de dominio de Azure AD. Las cuentas de usuario, las pertenencias a grupos y las credenciales de usuario (contraseñas) del directorio de Azure AD están disponibles automáticamente con Azure AD Domain Services. Los nuevos usuarios, grupos o cambios de atributos que se producen en el inquilino de Azure AD o en el directorio local se sincronizan automáticamente con Azure AD Domain Services.
* **Autenticación Kerberos y NTLM:** con compatibilidad para la autenticación Kerberos y NTLM, puede implementar las aplicaciones que dependen de la autenticación integrada de Windows.
* **Utilizar las credenciales y contraseñas corporativas:** las contraseñas para usuarios del inquilino de Azure AD  funcionan con Servicios de dominio de Azure AD. Los usuarios pueden utilizar sus credenciales corporativas para máquinas de unión al dominio, iniciar sesión de forma interactiva o a través de escritorio remoto y autenticarse en el dominio administrado.
* **Enlace LDAP y compatibilidad con lectura LDAP**: puede utilizar las aplicaciones que dependen de enlaces LDAP para autenticar usuarios en dominios ofrecidos por Azure AD Domain Services. Además, las aplicaciones que utilizan operaciones de lectura LDAP para consultar los atributos de usuario y equipo desde el directorio también pueden trabajar con Servicios de dominio de Azure AD.
* **LDAP seguro (LDAPS):** puede habilitar el acceso al directorio sobre LDAP seguro (LDAPS). El acceso LDAP seguro está disponible de forma predeterminada dentro de la red virtual. Sin embargo, también tiene la opción de habilitar el acceso LDAP seguro a través de Internet.
* **Directiva de grupo** : puede aprovechar un único GPO integrado para los usuarios y contenedores de equipos para exigir el cumplimiento de directivas de seguridad necesarias para las cuentas de usuario, así como para los equipos unidos a dominios. También puede crear sus propios GPO personalizados y asignarlos a unidades organizativas personalizadas para [administrar la directiva de grupo](active-directory-ds-admin-guide-administer-group-policy.md).
* **Administrar DNS:** los miembros del grupo 'Administradores de controlador de dominio de AAD' pueden administrar DNS para el dominio administrado mediante herramientas de administración de DNS conocidas, como el complemento MMC de Administración de DNS.
* **Crear unidades organizativas (OU) personalizadas:** los miembros del grupo 'Administradores de controlador de dominio de AAD' pueden crear unidades organizativas personalizadas dentro del dominio administrado. A estos usuarios se les conceden privilegios administrativos completos sobre unidades organizativas personalizadas, por lo que pueden agregar o quitar cuentas de servicio, equipos, grupos, etc. dentro de estas unidades organizativas personalizadas.
* **Disponible en varias regiones de Azure:** consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que está disponible Azure AD Domain Services.
* **Alta disponibilidad** : Azure AD Domain Services proporciona alta disponibilidad para el dominio. Esta característica ofrece la garantía de mayor tiempo de actividad de servicio y resistencia a errores. La supervisión de estado integrada ofrece corrección automatiza de errores poniendo en marcha nuevas instancias para reemplazar instancias con errores y proporcionar un servicio continuado para el dominio.
* **Usar herramientas de administración familiares:** puede utilizar herramientas de administración familiares de Windows Server Active Directory, como Centro de administración de Active Directory o Active Directory PowerShell, para administrar dominios administrados.
