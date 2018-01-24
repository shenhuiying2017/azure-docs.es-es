---
title: "Identidad de servicio administrada con Azure Event Hubs en versión preliminar | Microsoft Docs"
description: Uso de identidades de servicio administradas con Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Identidad de servicio administrada (versión preliminar)

Una identidad de servicio administrada (MSI) es una característica de Azure que permite crear una identidad segura asociada a la implementación en la que se ejecuta el código de la aplicación. A continuación, puede asociar esa identidad con los roles de control de acceso que conceden permisos personalizados para acceder a recursos específicos de Azure que la aplicación necesita. 

Con MSI, la plataforma Azure administra esta identidad en tiempo de ejecución. No es necesario almacenar y proteger las claves de acceso en la configuración o el código de la aplicación, ya sea para la propia identidad o para los recursos a los que necesita acceder. Una aplicación cliente de Event Hubs que se ejecuta dentro de una aplicación de Azure App Service o en una máquina virtual con la compatibilidad de MSI habilitada no necesita controlar las reglas y claves SAS ni cualquier otro token de acceso. La aplicación cliente solo necesita la dirección del punto de conexión del espacio de nombres de Event Hubs. Cuando se conecta la aplicación, Event Hubs enlaza el contexto de MSI con el cliente en una operación que se muestra en un ejemplo más adelante en este artículo.

Una vez creada la asociación con una identidad de servicio administrada, un cliente de Event Hubs puede realizar todas las operaciones autorizadas. La autorización se concede mediante la asociación de una identidad de servicio administrada con los roles de Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Roles y permisos de Event Hubs

Para la versión preliminar pública inicial, solo puede agregar una identidad de servicio administrada a los roles de "Propietario" o "Colaborador" de un espacio de nombres de Event Hubs, que concede el control total de identidad de todas las entidades del espacio de nombres. Sin embargo, las operaciones de administración que cambian la topología del espacio de nombres se admiten inicialmente solo mediante Azure Resource Manager y no mediante la interfaz de administración nativa de REST para Event Hubs. Esta compatibilidad también significa que no puede usar el objeto [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) del cliente .NET Framework dentro de una identidad de servicio administrada. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Uso de Event Hubs con una identidad de servicio administrada

En la siguiente sección se describen los pasos necesarios para crear e implementar una aplicación de ejemplo que se ejecuta en una identidad de servicio administrada, cómo conceder a dicha identidad acceso a un espacio de nombres de Event Hubs y cómo la aplicación interactúa con Event Hubs mediante dicha identidad.

En esta introducción se describe una aplicación web hospedada en [Azure App Service](https://azure.microsoft.com/services/app-service/). Los pasos necesarios para una aplicación hospedada en máquinas virtuales son similares.

### <a name="create-an-app-service-web-application"></a>Creación de una aplicación web de App Service

El primer paso es crear una aplicación ASP.NET de App Service. Si no está familiarizado con cómo hacer esto en Azure, siga [esta guía de procedimientos](../app-service/app-service-web-get-started-dotnet-framework.md). Sin embargo, en lugar de crear una aplicación de MVC, tal como se muestra en el tutorial, cree una aplicación de formularios Web Forms.

### <a name="set-up-the-managed-service-identity"></a>Configuración de la identidad de servicio administrada

Una vez creada la aplicación, vaya a la aplicación web recién creada en Azure Portal (también se muestra en la guía de procedimientos); a continuación, vaya a la página **Identidad de servicio administrada** y habilite la característica: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Una vez habilitada la característica, se crea una identidad de servicio en Azure Active Directory y se configura en el host de App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs

A continuación, [cree un espacio de nombres de Event Hubs](event-hubs-create.md) en una de las regiones de Azure compatibles con la versión preliminar para MSI: **Este de EE. UU.**, **Este de EE. UU. 2** o **Europa Occidental**. 

Vaya a la página **Control de acceso (IAM)** del espacio de nombres en Azure Portal y, a continuación, haga clic en **Agregar** para agregar la identidad de servicio administrada al rol de **Propietario**. Para ello, busque el nombre de la aplicación web en el campo **Seleccionar** del panel **Agregar permisos** y, a continuación, haga clic en la entrada. A continuación, haga clic en **Guardar**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
La identidad de servicio administrada de la aplicación web ya tiene acceso al espacio de nombres de Event Hubs y al centro de eventos que creó anteriormente. 

### <a name="run-the-app"></a>Ejecución de la aplicación

Ahora, modifique la página predeterminada de la aplicación de ASP.NET que ha creado. También puede utilizar el código de la aplicación web de [este repositorio de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Una vez iniciada la aplicación, apunte el explorador a EventHubsMSIDemo.aspx. Existe la alternativa de establecerla como la página de inicio. El código puede encontrarse en el archivo EventHubsMSIDemo.aspx.cs. El resultado es una aplicación web básica con unos cuantos campos de entrada y con los botones **enviar** y **recibir** que se conectan a Event Hubs para enviar o recibir mensajes. 

Tenga en cuenta cómo se inicializa el objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). En lugar de usar el proveedor de tokens de Token de acceso compartido (SAS), el código crea un proveedor de tokens para la identidad de servicio administrada con la llamada a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Por lo tanto, no hay ningún secreto para conservar y usar. El proveedor de tokens controla automáticamente el flujo del contexto de la identidad de servicio administrada para Event Hubs y el protocolo de enlace de autorización, que es un modelo más sencillo que el uso de SAS.

Una vez realizados estos cambios, publique y ejecute la aplicación. Una manera fácil de obtener los datos de publicación correctos es descargar e importar un perfil de publicación en Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Para enviar o recibir mensajes, escriba el nombre del espacio de nombres y el nombre de la entidad que creó y, a continuación, haga clic en **enviar** o **recibir**. 
 
Tenga en cuenta que la identidad de servicio administrada solo funciona dentro del entorno de Azure y únicamente en la implementación de App Service en que la configuró. Tenga en cuenta también que las identidades de servicio administradas no funcionan con las ranuras de implementación de App Service en este momento.

## <a name="next-steps"></a>pasos siguientes

Para obtener más información acerca de Event Hubs, visite los vínculos siguientes:

* Empiece por un [tutorial de Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
* [Detalles de precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicaciones de ejemplo que usan Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)