---
title: "Bibliotecas de administración de Azure Service Bus| Microsoft Docs"
description: "Administración de espacios de nombres y entidades de Service Bus desde .NET"
services: service-bus-messaging
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 7b04227327235f788ecf4018b6c159d33113b63d


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

* [Uso de Azure Portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estos tutoriales le proporcionarán valores para `AppId` (id. de cliente), `TenantId` y `ClientSecret` (clave de autenticación), los cuales se usan para la autenticación mediante las bibliotecas de administración. Debe tener permisos de "propietario" en el grupo de recursos en el que desea realizar la ejecución.

## <a name="programming-pattern"></a>Modelo de programación

El patrón para manipular los recursos de Service Bus sigue un protocolo común:

1. Obtenga un token de Azure Active Directory mediante la biblioteca `Microsoft.IdentityModel.Clients.ActiveDirectory`.
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

1. Establezca los parámetros CreateOrUpdate en los valores especificados.
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



<!--HONumber=Jan17_HO3-->


