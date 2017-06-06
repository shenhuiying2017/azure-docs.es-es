---
title: "Bibliotecas de administración de Azure Service Bus | Microsoft Docs"
description: "Administración de espacios de nombres y entidades de mensajería de Service Bus desde .NET"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: ec9f2fa3d88f59172d320b58287208deb084856f
ms.contentlocale: es-es
ms.lasthandoff: 04/11/2017


---

# <a name="service-bus-management-libraries"></a>Bibliotecas de administración de Service Bus

Las bibliotecas de administración de Service Bus pueden aprovisionar dinámicamente las entidades y los espacios de nombres de Service Bus. De esta manera, se pueden tener escenarios complejos de implementación y mensajería, lo que permite determinar mediante programación qué entidades se deben aprovisionar. Estas bibliotecas están actualmente disponibles para .NET.

## <a name="supported-functionality"></a>Funcionalidad admitida

* Creación, actualización y eliminación de espacios de nombres
* Creación, actualización y eliminación de colas
* Creación, actualización y eliminación de temas
* Creación, actualización y eliminación de suscripciones

## <a name="prerequisites"></a>Requisitos previos

Para comenzar a usar las bibliotecas de administración de Service Bus, debe autenticarse con Azure Active Directory (AAD). AAD requiere que se autentique como una entidad de servicio que proporciona acceso a los recursos de Azure. Para más información sobre cómo crear una entidad de servicio, consulte uno de los siguientes artículos:  

* [Uso de Azure Portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estos tutoriales le proporcionan valores para `AppId` (identificador de cliente), `TenantId` y `ClientSecret` (clave de autenticación), que usan las bibliotecas de administración con fines de autenticación. Debe tener permisos de "propietario" en el grupo de recursos en el que desea realizar la ejecución.

## <a name="programming-pattern"></a>Modelo de programación

El patrón para manipular los recursos de Service Bus sigue un protocolo común:

1. Obtenga un token de Azure Active Directory utilizando la biblioteca **Microsoft.IdentityModel.Clients.ActiveDirectory**.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Cree el objeto `ServiceBusManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Establezca los parámetros de `CreateOrUpdate` en los valores especificados.
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. Ejecute la llamada.
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Ejemplo de administración de .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Referencia de Microsoft.Azure.Management.ServiceBus ](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

