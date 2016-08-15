<properties
pageTitle="Objetos de aplicación y de entidad de servicio de Azure Active Directory | Microsoft Azure"
description="Una descripción de la relación entre los objetos de aplicación y de entidad de servicio en Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="07/14/2016"
ms.author="bryanla;mbaldwin"/>

# Objetos de aplicación y de entidad de servicio de Azure Active Directory
Cuando lee sobre una "aplicación" de Azure Active Directory (AD), no siempre es claro saber exactamente a qué hace referencia el autor. El objetivo de este artículo intentar aclararlo, definiendo los aspectos concretos y conceptuales de integración de aplicaciones de Azure AD, seguido por un ejemplo de registro y su consentimiento para una [aplicación multiinquilino](active-directory-dev-glossary.md#multi-tenant-application).

## Información general
El concepto de aplicación de Azure AD es más amplio que simplemente una parte del software. Es un término conceptual, que hace referencia no solo al software de la aplicación, sino también a su registro (también conocido como configuración de identidad) con Azure AD, que permite participar en las "conversaciones" de autenticación y autorización en tiempo de ejecución. Por definición, una aplicación puede funcionar en un rol de [cliente](active-directory-dev-glossary.md#client-application) (al consumir un recurso), un rol de [servidor de recursos](active-directory-dev-glossary.md#resource-server) (al exponer las API a los clientes), o incluso ambos. El protocolo de conversación se define mediante un [flujo de concesión de autorizaciones de OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), con el objetivo de permitir que el cliente o recurso acceda o proteja los datos de un recurso respectivamente. Ahora vamos a profundizar un poco más y ver cómo representa una aplicación internamente el modelo de aplicaciones de Azure AD.

## Registro de la aplicación
Al registrar una aplicación en el [Portal de Azure clásico](https://manage.windowsazure.com), se crean dos objetos en el inquilino de Azure AD: un objeto de aplicación y un objeto de entidad de servicio.

#### Objeto de aplicación
Una aplicación de Azure AD se *define* por su único objeto de aplicación, que reside en el inquilino de Azure AD donde se registró la aplicación, conocido como inquilino "principal" de la aplicación. El objeto de aplicación proporciona información relacionada con la identidad de una aplicación y es la plantilla desde la que *proceden* sus correspondientes objetos de entidad de servicio para su uso en tiempo de ejecución.

Se puede pensar en la aplicación como una representación *global* de la aplicación (para su uso en todos los inquilinos) y la entidad de servicio como una representación *local* (para su uso en un inquilino específico). La [entidad de aplicación][AAD-Graph-App-Entity] de Azure AD Graph define el esquema de un objeto de aplicación. Por lo tanto, un objeto de aplicación tiene una relación 1:1 con la aplicación de software y una relación 1:*n* con sus correspondientes *n* objetos de entidad de servicio.

#### Objeto de entidad de servicio
El objeto de entidad de servicio define la directiva y los permisos de una aplicación, que proporciona la base para una entidad de seguridad para representar la aplicación en el acceso a los recursos en tiempo de ejecución. La [entidad ServicePrincipal][AAD-Graph-Sp-Entity] de Azure AD Graph define el esquema de un objeto de entidad de servicio.

Es necesario un objeto de entidad de servicio en cada inquilino para el que se debe representar una instancia de uso de la aplicación, lo que permite el acceso seguro a los recursos que pertenecen a las cuentas de usuario de dicho inquilino. Una aplicación de un solo inquilino tendrá solo una entidad de servicio (en su inquilino principal); una [aplicación web](active-directory-dev-glossary.md#web-client) multiinquilino tendrá lo mismo, además de una entidad de servicio en cada inquilino donde un administrador o usuarios hayan dado su consentimiento en la aplicación desde ese inquilino para acceder a sus recursos. Después de consentimiento, se consultará el objeto de entidad de servicio para las solicitudes de autorización futuras.

> [AZURE.NOTE] Los cambios que realice en el objeto de aplicación también se reflejan en el objeto de entidad de servicio, solo en el inquilino principal de la aplicación (es decir, el inquilino en donde se registró). Si la aplicación está configurada para el acceso multiinquilino, los cambios realizados en el objeto de aplicación no se reflejan en los objetos de entidad de servicio del inquilino consumidor hasta que este quite el acceso y lo conceda de nuevo.

## Ejemplo
En el diagrama siguiente se muestra la relación entre un objeto de aplicación de la aplicación y los objetos de entidad de servicio correspondientes, en el contexto de una aplicación multiinquilino de ejemplo denominada **HR app**. En este escenario hay tres inquilinos de Azure AD:

- **Adatum**: el inquilino usado por la empresa que desarrolló la **aplicación de recursos humanos**
- **Contoso**: el inquilino usado por la organización Contoso, que es un consumidor de la **aplicación de recursos humanos**
- **Fabrikam**: el inquilino usado por la organización Fabrikam, que también consume la **aplicación de recursos humanos**

![Relación entre un objeto de aplicación y un objeto de entidad de servicio](./media/active-directory-application-objects/application-objects-relationship.png)

En el diagrama anterior, el paso 1 es el proceso de creación de los objetos de aplicación y de entidad de servicio del inquilino principal de la aplicación.

En el paso 2, cuando los administradores de Contoso y Fabrikam completan el consentimiento y conceden el acceso a la aplicación, se crea un objeto de entidad de servicio en el inquilino de Azure AD de la empresa y se asignan los permisos que concede el administrador de empresa. Tenga en cuenta también que la aplicación de recursos humanos podría estar configurada o diseñada para dar consentimiento a los usuarios para el uso individual.

En el paso 3, los inquilinos consumidores de la aplicación de Recursos Humanos (por ejemplo, Contoso y Fabrikam) cuentan con su propio objeto de entidad de servicio que representa el uso de una instancia de la aplicación en tiempo de ejecución, que se rige por los permisos a los que el administrador ha dado su consentimiento.

## Pasos siguientes
Se puede acceder al objeto de aplicación de la aplicación a través de la API de Azure AD Graph, tal como está representada por su [entidad Application](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ApplicationEntity) de OData.

Se puede acceder al objeto de entidad de servicio de la aplicación a través de la API de Azure AD Graph, tal como está representada por su [entidad ServicePrincipal](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference?branch=master#ServicePrincipalEntity) de OData.



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipalentity
[AZURE-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0803_2016-->