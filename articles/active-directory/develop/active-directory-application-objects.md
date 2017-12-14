---
title: "Objetos de aplicación y de entidad de servicio en Azure Active Directory"
description: "Una descripción de la relación entre los objetos de aplicación y de entidad de servicio en Azure Active Directory"
documentationcenter: dev-center-name
author: bryanla
manager: mtillman
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 85731afd18304e848f8577d8a6665dca3f9ee5d8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Objetos de aplicación y de entidad de servicio de Azure Active Directory (Azure AD)
En ocasiones, puede malinterpretarse el significado del término "aplicación" cuando se utiliza en el contexto de Azure AD. El objetivo de este artículo es aclarar aspectos concretos y conceptuales sobre la integración de la aplicación Azure AD con un ejemplo de registro y su consentimiento para una [aplicación multiinquilino](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Información general
Una aplicación que se ha integrado con Azure AD tiene implicaciones que van más allá de los aspectos de software. "Aplicación" se usa con frecuencia como un término conceptual que hace referencia no solo a la aplicación de software, sino también a su registro de Azure AD y rol en “conversaciones” de autenticación o autorización en tiempo de ejecución. Por definición, una aplicación puede funcionar en un rol de [cliente](active-directory-dev-glossary.md#client-application) (al consumir un recurso), un rol de [servidor de recursos](active-directory-dev-glossary.md#resource-server) (al exponer las API a los clientes), o incluso ambos. El protocolo de conversación se define mediante un [flujo de concesión de autorizaciones de OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), que permite que el cliente o recurso acceda o proteja los datos de un recurso respectivamente. Ahora vamos a profundizar un poco más y ver cómo el modelo de aplicaciones de Azure AD representa una aplicación. 

## <a name="application-registration"></a>Registro de la aplicación
Al registrar una aplicación de Azure AD en [Azure Portal][AZURE-Portal], se crean dos objetos en el inquilino de Azure AD: un objeto de aplicación y un objeto de entidad de servicio.

#### <a name="application-object"></a>Objeto de aplicación
Una aplicación de Azure AD se define por su único objeto de aplicación, que reside en el inquilino de Azure AD donde se registró la aplicación, conocido como inquilino "principal" de la aplicación. La [entidad de aplicación][AAD-Graph-App-Entity] de Azure AD Graph define el esquema para las propiedades de un objeto de aplicación. 

#### <a name="service-principal-object"></a>Objeto de entidad de servicio
Para acceder a los recursos que están protegidos por un inquilino de Azure AD, la entidad que requiere acceso debe estar representada por una entidad de seguridad. Esto es cierto para los usuarios (entidad de seguridad de usuario) y para las aplicaciones (entidad de servicio). La entidad de seguridad define la directiva de acceso y los permisos para el usuario o aplicación de ese inquilino. Esto habilita características básicas como la autenticación del usuario o de la aplicación durante el inicio de sesión y la autorización durante el acceso a los recursos.

Cuando una aplicación tiene permiso para acceder a los recursos de un inquilino (tras el registro o [consentimiento](active-directory-dev-glossary.md#consent)), se crea un objeto de entidad de seguridad de servicio. La [entidad ServicePrincipal][AAD-Graph-Sp-Entity] de Azure AD Graph define el esquema para las propiedades de un objeto de entidad de servicio.  

#### <a name="application-and-service-principal-relationship"></a>Relación entre la aplicación y la entidad de servicio
Considere el objeto de aplicación como una representación *global* de la aplicación para su uso en todos los inquilinos y la entidad de servicio como una representación *local* para su uso en un inquilino específico. El objeto de aplicación actúa como la plantilla a partir de la cual se *derivan* las propiedades comunes y predeterminadas para su uso en la creación de objetos de entidad de servicio correspondientes. Por tanto, un objeto de aplicación tiene una relación 1:1 con la aplicación de software y una relación 1:muchos con sus objetos de entidad de servicio correspondientes.

Debe crearse una entidad de servicio en cada inquilino donde se usa la aplicación. Esto permite establecer una identidad para el inicio de sesión o el acceso a los recursos que va a proteger el inquilino. Una aplicación de inquilino único tendrá solo una entidad de servicio (en su inquilino principal), que normalmente se crea y se consiente para su uso durante el registro de la aplicación. Una API o aplicación web multiinquilino también tiene una entidad de servicio creada en cada inquilino donde un usuario de ese inquilino ha dado su consentimiento para su uso.  

> [!NOTE]
> Los cambios que realice en el objeto de aplicación también se reflejan en el objeto de entidad de servicio, solo en el inquilino principal de la aplicación (es decir, el inquilino en donde se registró). Para aplicaciones multiinquilino, los cambios realizados en el objeto de aplicación no se reflejan en los objetos de entidad de servicio del inquilino consumidor hasta que el acceso se quita a través del [panel de acceso de la aplicación](https://myapps.microsoft.com) y se concede de nuevo.
><br>  
> Tenga en cuenta también que las aplicaciones nativas se registran como multiinquilino de forma predeterminada.
> 
> 

## <a name="example"></a>Ejemplo
En el siguiente se muestra la relación entre un objeto de aplicación de la aplicación y los objetos de entidad de servicio correspondientes, en el contexto de una aplicación multiinquilino de ejemplo denominada **HR app**. En este escenario hay tres inquilinos de Azure AD: 

* **Adatum**: el inquilino usado por la empresa que desarrolló la **aplicación de recursos humanos**
* **Contoso**: el inquilino usado por la organización Contoso, que es un consumidor de la **aplicación de recursos humanos**
* **Fabrikam**: el inquilino usado por la organización Fabrikam, que también consume la **aplicación de recursos humanos**

![Relación entre un objeto de aplicación y un objeto de entidad de servicio](./media/active-directory-application-objects/application-objects-relationship.png)

En el diagrama anterior, el paso 1 es el proceso de creación de los objetos de aplicación y de entidad de servicio del inquilino principal de la aplicación.

En el paso 2, cuando los administradores de Contoso y Fabrikam completan el consentimiento, se crea un objeto de entidad de servicio en el inquilino de Azure AD de la empresa y se asignan los permisos que concede el administrador. Tenga en cuenta también que la aplicación de recursos humanos podría estar configurada o diseñada para dar consentimiento a los usuarios para el uso individual.

En el paso 3, los inquilinos consumidores de la aplicación HR (Contoso y Fabrikam) tienen cada uno de ellos su propio objeto de entidad de servicio. Cada uno representa su uso de una instancia de la aplicación en tiempo de ejecución, que se rige por los permisos que consiente el administrador correspondiente.

## <a name="next-steps"></a>Pasos siguientes
Se puede acceder al objeto de aplicación de la aplicación a través de la API de Azure AD Graph, el editor de manifiestos de aplicación de [Azure Portal][AZURE-Portal] o de [cmdlets de Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tal como y como se representa mediante su [entidad Application][AAD-Graph-App-Entity] de OData.

Se puede acceder al objeto de entidad de servicio de la aplicación a través de la API de Azure AD Graph o de [cmdlets de Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tal como se representa mediante su entidad [ServicePrincipal][AAD-Graph-Sp-Entity] de OData.

[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) es útil para consultar tanto objetos de aplicación como objetos de entidad de servicio.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
