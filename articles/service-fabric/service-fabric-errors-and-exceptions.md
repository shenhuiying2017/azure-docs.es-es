---
title: Excepciones de FabricClient frecuentes | Microsoft Docs
description: "Describe las excepciones y errores frecuentes que pueden producir las API FabricClient al realizar operaciones de administración de aplicaciones y clústeres."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/16/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 491f61afe899c746c193f0d3703d3212f9258e63
ms.openlocfilehash: 578046d6939b90cb58d457087bb465006630b4c1


---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Excepciones y errores frecuentes cuando se trabaja con las API FabricClient
Las API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) permiten a los administradores de clústeres y aplicaciones realizar tareas administrativas en un clúster, servicio o aplicación de Service Fabric. Por ejemplo, la implementación, actualización o eliminación de aplicaciones, la comprobación del estado de un clúster o la prueba de un servicio. Los desarrolladores de aplicaciones y los administradores de clústeres pueden usar las API FabricClient para desarrollar herramientas para la administración de aplicaciones y clústeres de Service Fabric.

Hay muchos tipos diferentes de operaciones que pueden realizarse mediante FabricClient.  Cada método puede producir excepciones para errores debido a una entrada incorrecta, errores en tiempo de ejecución o problemas de infraestructura transitorios.  Consulte la documentación de referencia de la API para buscar las excepciones que produce un método específico. Sin embargo, existen algunas excepciones que muchas API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) diferentes pueden producir. En la tabla siguiente se muestran las excepciones que son frecuentes en las API FabricClient.

| Excepción | Se produce cuando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |El objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) está en un estado cerrado. Deseche el objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) que está usando y cree instancias de un nuevo objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient). |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |La operación ha agotado el tiempo de espera. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) se devuelve cuando la operación tarda más que MaxOperationTimeout en completarse. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Se genera un error de comprobación de acceso de la operación. Se devuelve E_ACCESSDENIED. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Se generó un error en tiempo de ejecución al realizar la operación. Potencialmente, cualquier método FabricClient puede producir [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException); la propiedad [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) indica la causa exacta de la excepción. Los códigos de error se definen en la enumeración [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) . |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |Se genera un error en la operación debido a una condición de error transitorio de algún tipo. Por ejemplo, se puede producir un error en la operación porque no se puede obtener acceso temporalmente a un cuórum de réplicas. Las excepciones transitorias corresponden a las operaciones erróneas que se pueden reintentar. |

Algunos errores [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) frecuentes que pueden devolverse en [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Error | Condición |
| --- |:--- |
| CommunicationError |Un error de comunicación ha provocado que se produzca un error en la operación; vuelva a intentar la operación. |
| InvalidCredentialType |El tipo de credencial no es válido. |
| InvalidX509FindType |X509FindType no es válido. |
| InvalidX509StoreLocation |La ubicación del almacén X509 no es válida. |
| InvalidX509StoreName |El nombre del almacén X509 no es válido. |
| InvalidX509Thumbprint |La cadena de huella digital de certificado X509 no es válida. |
| InvalidProtectionLevel |El nivel de protección no es válido. |
| InvalidX509Store |El almacén de certificados X509 no se puede abrir. |
| InvalidSubjectName |El nombre de sujeto no es válido. |
| InvalidAllowedCommonNameList |El formato de la cadena de lista de nombres frecuente no es válido. Debe ser una lista separada por comas. |




<!--HONumber=Nov16_HO3-->


