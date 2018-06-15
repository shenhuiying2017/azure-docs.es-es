---
title: Cambios realizados en un proyecto de ASP.NET Core al conectarse a Azure Key Vault | Microsoft Docs
description: Describe lo que sucede a su proyecto de ASP.NET Core cuando se conecta a Key Vault mediante los servicios conectados de Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781584"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>¿Qué le ha ocurrido a mi proyecto de ASP.NET Core (servicio conectado a Key Vault de Visual Studio)?

> [!div class="op_single_selector"]
> - [Introducción](vs-key-vault-aspnet-core-get-started.md)
> - [¿Qué ha ocurrido?](vs-key-vault-aspnet-core-what-happened.md)

En este artículo se identifican los cambios exactos realizados en el proyecto de ASP.NET cuando se agrega el [servicio conectado de Key Vault mediante Visual Studio](vs-key-vault-add-connected-service.md).

Para más información sobre cómo trabajar con el servicio conectado, consulte la [introducción](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Referencias agregadas

Afecta a las referencias de paquetes de NuGet y a las referencias de *.NET del archivo de proyecto.

| Escriba | Referencia |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Archivos agregados

- ConnectedService.json agregado, que registra información sobre el proveedor de servicio conectado, la versión y un vínculo a la documentación.

## <a name="project-file-changes"></a>Cambios del archivo de proyecto

- Agrega el archivo ConnectedServices.json y ItemGroup de los servicios conectados.

## <a name="launchsettingsjson-changes"></a>Cambios en launchsettings.json

- Agrega las siguientes entradas de la variable de entorno al perfil de IIS Express y perfil que coincide con el nombre del proyecto web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Cambios en Azure

- Se crea un grupo de recursos o se usa uno existente.
- Se crea una instancia de Key Vault en el grupo de recursos especificado.

