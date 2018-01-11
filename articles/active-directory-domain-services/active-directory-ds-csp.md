---
title: Azure Active Directory Domain Services para Azure Cloud Solution Providers | Microsoft Docs
description: Azure Active Directory Domain Services para Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 313c4853b0f585921739779bb764c50036c9ac8a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services para Azure Cloud Solution Providers (CSP)
En este artículo se explica cómo puede usar Azure AD Domain Services en una suscripción a Azure CSP.

## <a name="overview-of-azure-csp"></a>Información general sobre Azure CSP
Azure CSP es un programa para asociados de Microsoft y proporciona un canal de licencias para varios servicios en la nube de Microsoft. Azure CSP permite a los asociados administrar ventas, controlar la relación de facturación, proporcionar soporte técnico y asistencia para la facturación y ser el único punto de contacto de los clientes. Además, Azure CSP proporciona un conjunto completo de herramientas, incluido un portal de autoservicio y API complementarias. Estas herramientas permiten a los asociados de CSP aprovisionar y administrar recursos de Azure fácilmente, así como proporcionar una facturación para sus clientes y suscripciones.

El [portal del Centro de partners](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) actúa como punto de entrada para todos los asociados de Azure CSP. Proporciona funciones de administración de clientes enriquecidas, procesamiento automatizado y mucho más. Los asociados de Azure CSP pueden utilizar las funciones de Centro de partners mediante una UI basada en web o mediante PowerShell y diversas llamadas API.

El siguiente diagrama ilustra cómo funciona el modelo de CSP en un nivel superior. Contoso cuenta con un directorio de Azure AD. Tienen una asociación con un CSP, que implementa y administra los recursos de su suscripción a Azure CSP. Contoso también puede tener suscripciones regulares (directas) a Azure, que se facturan directamente a Contoso.

![Información general sobre el modelo de CSP](./media/csp/csp_model_overview.png)

El inquilino del asociado de CSP tiene tres grupos de agentes especiales: agentes de administración, agentes del departamento de soporte técnico y agentes de ventas. El grupo de agentes de administración se asigna al rol de administrador de inquilinos en el directorio de Azure AD de Contoso. Como resultado, un usuario que pertenezca al grupo de agentes de administración del asociado de CSP tiene privilegios de administrador de inquilinos en el directorio de Azure AD de Contoso. Cuando el asociado de CSP aprovisiona una suscripción a Azure CSP para Contoso, su grupo de agentes de administración se asigna al rol de propietario de dicha suscripción. Como resultado, los agentes de administración del asociado de CSP tienen los privilegios necesarios para aprovisionar recursos de Azure, como máquinas virtuales, redes virtuales y Azure AD Domain Services en nombre de Contoso.

Para más información, consulte [Azure CSP overview](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) (Introducción a Azure CSP)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Ventajas de usar Azure AD Domain Services en una suscripción a Azure CSP
Azure AD Domain Services proporciona servicios compatibles con Windows Server AD en Azure, como LDAP, autenticación Kerberos/NTLM, unión a un dominio, directiva de grupo y DNS. A lo largo de décadas, muchas aplicaciones se han compilado para funcionar con AD con estas funcionalidades. Muchos proveedores de software independientes (ISV) han creado e implementado aplicaciones en las instalaciones de sus clientes. El soporte técnico de estas aplicaciones resulta difícil, ya que, a menudo, requiere acceso a los distintos entornos en que están implementadas las aplicaciones. Con las suscripciones a Azure CSP, tiene una alternativa más sencilla gracias al escalado y la flexibilidad de Azure.

Ahora, Azure AD Domain Services admite suscripciones a Azure CSP. Ya puede implementar la aplicación en una suscripción a Azure CSP enlazada al directorio de Azure AD de su cliente. Como resultado, los empleados (personal de soporte técnico) pueden administrar y realizar el mantenimiento de las máquinas virtuales en las que se implementa la aplicación mediante credenciales corporativas de la organización. Además, puede aprovisionar un dominio administrado de Azure AD Domain Services para el directorio de Azure AD de su cliente. La aplicación está conectada al dominio administrado del cliente. Por lo tanto, las funciones de la aplicación dependan de Kerberos/NTLM, LDAP o la [API System.DirectoryServices](https://msdn.microsoft.com/library/system.directoryservices) funcionan sin problemas en el directorio de su cliente. Los clientes finales se beneficiarán enormemente de consumir la aplicación como servicio sin tener que preocuparse del mantenimiento de la infraestructura en que se implementó la aplicación.

Se le cobrará la facturación de todos los recursos de Azure que consuma en dicha suscripción, incluido Azure AD Domain Services. Tiene control completo sobre la relación con el cliente en lo que respecta a ventas, facturación, soporte técnico, etc. Con la flexibilidad de la plataforma Azure CSP, un pequeño equipo de agentes de soporte técnico puede ofrecer servicio a muchos clientes que tengan instancias de la aplicación implementadas.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelos de implementación de CSP para Azure AD Domain Services
Hay dos maneras de usar Azure AD Domain Services con una suscripción a Azure CSP. Seleccione el adecuado en función de las consideraciones de seguridad y simplicidad de los clientes.

### <a name="direct-deployment-model"></a>Modelo de implementación directa
En este modelo de implementación, Azure AD Domain Services se habilita en una red virtual que pertenece a la suscripción a Azure CSP. Los agentes de administración del asociado de CSP tienen los privilegios siguientes:
* Privilegios de administrador global en el directorio de Azure AD del cliente.
* Privilegios de propietario de la suscripción en la suscripción a Azure CSP.

![Modelo de implementación directa](./media/csp/csp_direct_deployment_model.png)

En este modelo de implementación, los agentes de administración del proveedor de CSP pueden administrar identidades para el cliente. Estos agentes de administración tienen la capacidad de aprovisionar nuevos usuarios y grupos, agregar aplicaciones en el directorio de Azure AD del cliente, etc. Este modelo de implementación puede ser adecuado para organizaciones pequeñas que no tienen un administrador de identidades dedicado o prefieren que el asociado de CSP administre identidades en su nombre.


### <a name="peered-deployment-model"></a>Modelo de implementación emparejado
En este modelo de implementación, Azure AD Domain Services se habilita en una red virtual que pertenece al cliente, es decir, una suscripción directa a Azure que paga el cliente. A continuación, el asociado de CSP puede implementar aplicaciones en una red virtual que pertenece a la suscripción a CSP del cliente. A continuación, las redes virtuales se pueden conectar mediante el emparejamiento de redes virtuales de Azure. Como resultado, las cargas de trabajo o aplicaciones que implementa el asociado de CSP en la suscripción a Azure CSP pueden conectar con el dominio administrado del cliente aprovisionado en la suscripción a Azure directa del cliente.

![Modelo de implementación emparejado](./media/csp/csp_peered_deployment_model.png)

Este modelo de implementación proporciona una separación de privilegios y permite a los agentes del departamento de soporte técnico del asociado de CSP administrar la suscripción a Azure, e implementar y administrar los recursos que contiene. Sin embargo, los agentes del departamento de soporte técnico del asociado de CSP no necesitan tener privilegios de administrador global en el directorio de Azure AD del cliente. Los administradores de identidades del cliente pueden seguir administrando identidades para su organización.

Este modelo de implementación puede ser adecuado para escenarios en que un ISV (proveedor de software independiente) proporciona una versión hospedada de su aplicación local, que también se debe poder conectar a la instancia de AD del cliente.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administración de dominios administrados de Azure AD Domain Services en suscripciones a CSP
Cuando se administra un dominio administrado en una suscripción a Azure CSP, se aplican las siguientes consideraciones importantes:

* **Los agentes de administración de CSP pueden aprovisionar un dominio administrado con sus credenciales:** Azure AD Domain Services admite suscripciones a Azure CSP. Por lo tanto, los usuarios que pertenecen al grupo de agentes de administración de asociado CSP pueden aprovisionar un nuevo dominio administrado de Azure AD Domain Services.

* **Los CSP pueden generar scripts de creación de nuevos dominios administrados para sus clientes con PowerShell:** vea [Habilitación de Azure Active Directory Domain Services mediante PowerShell](active-directory-ds-enable-using-powershell.md) para obtener más información.

* **Los agentes de administración de CSP no pueden realizar tareas de administración continuas en el dominio administrado usando sus credenciales:** los usuarios de administración de CSP no pueden realizar tareas de administración rutinarias dentro del dominio administrado con sus credenciales. Estos usuarios son externos al directorio de Azure AD del cliente y sus credenciales no están disponibles en el directorio de Azure AD del cliente. Por lo tanto, Azure AD Domain Services no tiene acceso a los valores de hash de contraseña de Kerberos y NTLM para estos usuarios. Como resultado, estos usuarios no se pueden autenticar en dominios administrados de Azure AD Domain Services.

  > [!WARNING]
  > **Debe crear una cuenta de usuario en el directorio del cliente para realizar tareas de administración en curso en el dominio administrado.**
  > No puede iniciar sesión en el dominio administrado usando credenciales de usuario de administrador de CSP. Para ello, use las credenciales de una cuenta de usuario que pertenezca al directorio de Azure AD. Necesita estas credenciales para tareas como unir máquinas virtuales al dominio administrado, administrar DNS, administrar directivas de grupo, etc.
  >

* **La cuenta de usuario creada para la administración continuada debe agregarse al grupo "Administradores de controladores de dominio de AAD":** el grupo "Administradores de controladores de dominio de AAD" tiene privilegios para realizar ciertas tareas de administración delegada en el dominio administrado. Estas tareas incluyen la configuración de DNS, la creación de unidades organizativas, la administración de directivas de grupos, etc. Para que un asociado de CSP realice estas tareas en un dominio administrado, es necesario crear una cuenta de usuario en el directorio de Azure AD del cliente. Las credenciales para esta cuenta se deben compartir con los agentes de administración del asociado de CSP. Además, esta cuenta de usuario debe agregarse al grupo "Administradores de controladores de dominio de AAD" para permitir que las tareas de configuración del dominio administrado se realicen mediante esta cuenta de usuario.


## <a name="next-steps"></a>Pasos siguientes
* [Inscríbase en el programa Azure CSP](https://partnercenter.microsoft.com/partner/programs) y empiece a generar negocio mediante Azure CSP.
* Revise la lista de [servicios de Azure disponibles en Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Habilitar Azure AD Domain Services mediante PowerShell](active-directory-ds-enable-using-powershell.md)
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
