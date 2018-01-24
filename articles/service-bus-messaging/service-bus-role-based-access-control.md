---
title: "Versión preliminar del control de acceso basado en rol (RBAC) de Azure Service Bus | Microsoft Docs"
description: Control de acceso basado en rol de Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Control de acceso basado en rol de Active Directory (versión preliminar)

Microsoft Azure proporciona una administración integrada del control de acceso para recursos y aplicaciones basados en Azure Active Directory (Azure AD). Con Azure AD, puede administrar las cuentas y aplicaciones del usuario específicamente para sus aplicaciones basadas en Azure, o puede federar la infraestructura existente de Active Directory con Azure AD para un inicio de sesión único para toda la empresa que también abarque los recursos de Azure y las aplicaciones hospedadas en Azure. Posteriormente, puede asignar esas identidades de usuario y de aplicación a roles globales y específicos del servicio para otorgar acceso a los recursos de Azure.

Para Azure Service Bus, la administración de los espacios de nombres y de todos los recursos relacionados mediante Azure Portal y la API de administración de recursos de Azure, ya se ha protegido mediante el modelo de *control de acceso basado en rol* (RBAC). El control de acceso basado en rol de las operaciones en tiempo de ejecución es una característica que está ahora en versión preliminar pública. 

Una aplicación que use el control de acceso basado en rol de Azure AD no necesita controlar las reglas y claves de SAS ni ningún otro token de acceso. La aplicación cliente permite interactuar con Azure AD para establecer un contexto de autenticación y permite adquirir un token de acceso para Service Bus. Con respecto a las cuentas de usuario de dominio que requieren un inicio de sesión interactivo, la aplicación no controla nunca ninguna credencial directamente.

## <a name="service-bus-roles-and-permissions"></a>Roles y permisos de Service Bus

Para la versión preliminar pública inicial, solo puede agregar cuentas de Azure AD y entidades de servicio a los roles de "Propietario" o "Colaborador" de un espacio de nombres de mensajería de Service Bus. Esta operación concede el control total de identidades sobre todas las entidades del espacio de nombres. Las operaciones de administración que cambian la topología del espacio de nombres se admiten inicialmente solo mediante la administración de recursos de Azure y no mediante la interfaz de administración de REST para Service Bus. Esto significa también que no se puede utilizar el objeto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del cliente de .NET Framework con una cuenta de Azure AD.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Uso de Service Bus con una cuenta de usuario de dominio de Azure AD

La siguiente sección describe los pasos necesarios para crear y ejecutar una aplicación de ejemplo que solicita un usuario interactivo de Azure AD para iniciar sesión, cómo conceder acceso a Service Bus para esa cuenta de usuario y cómo utilizar esa identidad para acceder a Event Hubs. 

Esta introducción describe una aplicación de consola simple, y el [código para esta aplicación se encuentra en Github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Creación de una cuenta de usuario de Active Directory

El primer paso es opcional. Cada suscripción de Azure se empareja automáticamente con un inquilino de Azure Active Directory y, si tiene acceso a una suscripción de Azure, su cuenta de usuario ya está registrada. Esto significa que solamente puede usar su cuenta. 

Si desea crear una cuenta específica para este escenario, [siga estos pasos](../automation/automation-create-aduser-account.md). Debe tener permiso para crear cuentas en el inquilino de Azure Active Directory, lo cual puede que no sea el caso para escenarios empresariales más grandes.

### <a name="create-a-service-bus-namespace"></a>Creación de un espacio de nombres de Service Bus

A continuación, [cree un espacio de nombres de mensajería de Service Bus](service-bus-create-namespace-portal.md) en una de las regiones de Azure que tienen compatibilidad con la versión preliminar de RBAC: **Este de EE. UU.**, **Este de EE. UU. 2** o **Europa Occidental**. 

Una vez creado el espacio de nombres, vaya a su página de **Control de acceso (IAM)** en el portal y, a continuación, haga clic en **Agregar** para agregar la cuenta de usuario de Azure AD al rol de propietario. Si usa su propia cuenta de usuario y ha creado el espacio de nombres, ya está en el rol de propietario. Para agregar una cuenta diferente al rol, busque el nombre de la aplicación web en el campo **Seleccionar** del panel **Agregar permisos** y, a continuación, haga clic en la entrada. A continuación, haga clic en **Guardar**.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

Ahora, la cuenta de usuario ya tiene acceso al espacio de nombres de Service Bus y a la cola que creó anteriormente.
 
### <a name="register-the-application"></a>Registro de la aplicación

Antes de poder ejecutar la aplicación de ejemplo, regístrela en Azure AD y apruebe la petición de consentimiento que permite que la aplicación pueda acceder a Azure Service Bus en su nombre. 

Dado que la aplicación de ejemplo es una aplicación de consola, debe registrar una aplicación nativa y agregar permisos de API para **Microsoft.ServiceBus** en el conjunto de "permisos necesarios". Las aplicaciones nativas también necesitan un **URI de redireccionamiento** en Azure AD que actúe como identificador. No es necesario que este URI sea un destino de red. Use `http://servicebus.microsoft.com` para este ejemplo, dado que el ejemplo de código ya utiliza ese URI.

Se proporciona información detallada sobre los pasos de registro en [este tutorial](../active-directory/develop/active-directory-integrating-applications.md). Siga los pasos para registrar una aplicación **nativa** y, a continuación, siga las instrucciones de actualización para agregar **Microsoft.ServiceBus** API a los permisos necesarios. A medida que vaya realizando los pasos, tome nota del valor de **TenantId** y **ApplicationId**, ya que los necesitará para ejecutar la aplicación.

### <a name="run-the-app"></a>Ejecución de la aplicación

Antes de poder ejecutar el ejemplo, edite el archivo App.config y, según el escenario, establezca los siguientes valores:

- `tenantId`: establézcalo en el valor de **TenantId**.
- `clientId`: establézcalo en el valor de **ApplicationId**. 
- `clientSecret`: si desea iniciar sesión mediante el secreto de cliente, créelo en Azure AD. Además, utilice una aplicación web o API en lugar de una aplicación nativa. Además, agregue la aplicación en **Control de acceso (IAM)** en el espacio de nombres que creó anteriormente.
- `serviceBusNamespaceFQDN`: establézcalo en el nombre DNS completo del espacio de nombres de Service Bus recién creado. Por ejemplo, `example.servicebus.windows.net`.
- `queueName`: establézcalo en el nombre de la cola que creó.
- El URI de redireccionamiento que especificó en la aplicación en los pasos anteriores.
 
Al ejecutar la aplicación de consola, deberá seleccionar un escenario. Haga clic en **Interactive User Login** (Inicio de sesión de usuario interactivo). Para ello, escriba su número y pulse ENTRAR. La aplicación muestra una ventana de inicio de sesión, solicita su consentimiento para acceder a Service Bus y, a continuación, utiliza el servicio para ejecutarse en el escenario de envío o recepción mediante la identidad de inicio de sesión.

## <a name="next-steps"></a>pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas.

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)