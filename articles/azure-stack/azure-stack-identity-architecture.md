---
title: Arquitectura de identidad para Azure Stack | Microsoft Docs
description: Conozca la arquitectura de identidad que se puede usar con Azure Stack.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 0f3e28f7726afab02211902b5ba2e478ae8065df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Arquitectura de identidad para Azure Stack
Antes de elegir un proveedor de identidad para usarlo con Azure Stack, conozca las diferencias importantes entre las opciones de Azure Active Directory (Azure AD) y Servicios de federación de Active Directory (AD FS). 

## <a name="capabilities-and-limitations"></a>Funcionalidades y limitaciones 
El proveedor de identidad que elija puede limitar las opciones, incluida la compatibilidad con la arquitectura multiinquilino. 

  

|Funcionalidad o escenario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Conectado a Internet     |Sí       |Opcional|
|Compatibilidad con arquitectura multiinquilino     |Sí       |Sin       |
|Redifusión de Marketplace       |Sí       |Sí, requiere el uso de la herramienta [Marketplace Syndication sin conexión](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity).|
|Compatibilidad con la biblioteca de autenticación de Active Directory (ADAL) |Sí |Sí|
|Compatibilidad con herramientas como la interfaz de línea de comandos (CLI) de Azure, Visual Studio (VS) y PowerShell  |Sí |Sí|
|Creación de entidades de servicio mediante el portal     |Sí |Sin |
|Creación de entidades de servicio con certificados      |Sí |Sí|
|Creación de entidades de servicio con secretos (claves)    |Sí |Sin |
|Las aplicaciones pueden usar el servicio Graph           |Sí |Sin |
|Las aplicaciones pueden utilizar el proveedor de identidades para iniciar sesión |Sí |Sí: requiere que las aplicaciones se federen con AD FS local. |

## <a name="topologies"></a>Topologías
Las secciones siguientes proporcionan información acerca de las topologías de identidad que se pueden usar.

### <a name="azure-ad--single-tenant"></a>Azure AD: inquilino único 
De forma predeterminada, si instala Azure Stack y usa Azure AD, Azure Stack usa una topología de un único inquilino. 

Una topología de inquilino único resulta útil cuando:
- Todos los usuarios forman parte del mismo inquilino.
- Un proveedor de servicios hospeda una instancia de Azure Stack para una organización.  

![Topología de Azure Stack que usa un solo inquilino con Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Con esta topología:
- Azure Stack registra todas las aplicaciones y servicios en el mismo directorio del inquilino de Azure AD. 
- Azure Stack autentica solo los usuarios y las aplicaciones desde ese directorio, incluidos los Tokens. 
- Las identidades de los administradores (operadores en la nube) y los usuarios del inquilino están en el mismo inquilino de directorio. 
- Para permitir que un usuario de otro directorio acceda e este entorno de Azure Stack, debe [invitar al usuario como invitado](azure-stack-identity-overview.md#guest-users) al directorio de inquilino.  

### <a name="azure-ad--multi-tenant"></a>Azure AD: arquitectura multiinquilino
Los operadores de la nube pueden configurar Azure Stack para permitir el acceso a las aplicaciones por parte de inquilinos de una o varias organizaciones. Los usuarios acceden a las aplicaciones mediante el portal de usuario. En esta configuración, el portal de administración (usado por el operador en la nube) se limita a los usuarios desde un único directorio. 

Una topología mutiinquilino resulta útil cuando:
- Un proveedor de servicios desea permitir a los usuarios de varias organizaciones tener acceso a Azure Stack.

![Topología de Azure Stack que usa una topología multiinquilino con Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Con esta topología:
- El acceso a los recursos debe tener lugar para cada organización. 
- Los usuarios de una organización no deberían poder conceder acceso a los recursos a los usuarios que están fuera de ella.  
- Las identidades de los administradores (operadores de la nube) pueden estar en un inquilino de un directorio distinto del de las identidades de los usuarios. Esta separación proporciona el aislamiento de la cuenta en el nivel del proveedor de identidad. 
 
### <a name="ad-fs"></a>AD FS  
La topología de AD FS es obligatoria cuando alguna de las siguientes condiciones es verdadera:
- Azure Stack no se conecta a Internet.
- Azure Stack puede conectarse a Internet, pero se decide usar AD FS para el proveedor de identidad.
  
![Topología de Azure Stack con AD FS](media/azure-stack-identity-architecture/adfs.png)

Con esta topología:
- Para poderse usar en un entorno de producción, es necesario integrar la instancia de AD FS de Azure Stack integrada con una instancia existente de AD FS respaldada por Active Directory (AD), mediante una confianza de federación. 
- También puede integrar el servicio Graph en Azure Stack con su instancia de Active Directory actual.  Igualmente, puede usar el servicio Graph API basado en OData que admite API coherentes con Graph API de Azure AD.  

  Para interactuar con la instancia de AD, Graph API requiere credenciales de usuario de su instancia de AD que tengan permiso de solo lectura a esta. 
  - La versión integrada de AD FS se basa en Server 2016. 
  - Sus instancias de AD FS y AD deben basarse en Server 2012 o versiones anteriores. 
  
  Entre la instancia de AD y la de AD FS integrada, las interacciones no se restringen a OpenID Connect y pueden usar cualquier protocolo que ambas admitan.  
  - Las cuentas de usuario se crean y se administran en la instancia de AD local.
  - Las entidades de servicio y los registros de las aplicaciones se administran en la instancia de AD integrada.



## <a name="next-steps"></a>pasos siguientes
- [Introducción a las identidades](azure-stack-identity-overview.md)   
- [Integración del centro de datos: identidad](azure-stack-integrate-identity.md)