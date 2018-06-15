---
title: Cambios realizados en un proyecto de ASP.NET al conectarse a Azure Key Vault | Microsoft Docs
description: Describe lo que sucede a su proyecto de ASP.NET cuando se conecta a Key Vault mediante los servicios conectados de Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781564"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>¿Qué le ha ocurrido a mi proyecto de ASP.NET (servicio conectado a Key Vault de Visual Studio)?

> [!div class="op_single_selector"]
> - [Introducción](vs-key-vault-aspnet-get-started.md)
> - [¿Qué ha ocurrido?](vs-key-vault-aspnet-what-happened.md)

En este artículo se identifican los cambios exactos realizados en el proyecto de ASP.NET cuando se agrega el [servicio conectado de Key Vault mediante Visual Studio](vs-key-vault-add-connected-service.md).

Para más información sobre cómo trabajar con el servicio conectado, consulte la [introducción](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Referencias agregadas

Afecta al archivo de proyecto de *referencias de .NET y `packages.config` (referencias de NuGet).

| Escriba | Referencia |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Archivos agregados

- ConnectedService.json agregado, que registra información sobre el proveedor de servicio conectado, la versión y un vínculo a la documentación.

## <a name="project-file-changes"></a>Cambios del archivo de proyecto

- Agrega el archivo ConnectedServices.json y ItemGroup de los servicios conectados.
- Las referencias a los ensamblados de .NET se describen en la sección de [referencias agregadas](#added-references).

## <a name="webconfig-or-appconfig-changes"></a>Cambios de web.config o app.config

- Se agregaron las siguientes entradas de configuración:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Cambios en Azure

- Se crea un grupo de recursos o se usa uno existente.
- Se crea una instancia de Key Vault en el grupo de recursos especificado.

