---
title: Uso de una fuente NuGet personalizada en Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Use una fuente NuGet personalizada para acceder a paquetes NuGet y utilizarlos en Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contenedores
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199116"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Uso de una fuente NuGet personalizada en Azure Dev Spaces

Una fuente NuGet proporciona una manera cómoda de incluir orígenes de paquetes en un proyecto. Azure Dev Spaces deberá poder acceder a esta fuente para que las dependencias se instalen correctamente en el contenedor de Docker.

## <a name="set-up-a-nuget-feed"></a>Configuración de una fuente NuGet

Para configurar una fuente NuGet:
1. Agregue una [referencia de paquete](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) en el archivo `*.csproj` bajo en nodo `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Crear un archivo [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) en la carpeta del proyecto.
     * Use la sección `packageSources` para hacer referencia a la ubicación de la fuente NuGet. Importante: La fuente NuGet debe ser de acceso público.
     * Use la sección `packageSourceCredentials` para configurar las credenciales de nombre de usuario y contraseña. 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. Si usa control de código fuente:
    - Haga referencia a `NuGet.Config` en el archivo `.gitignore` para no confirmar accidentalmente las credenciales en el repositorio de origen.
    - Abra el archivo `azds.yaml` del proyecto, busque la sección `build` e inserte el fragmento de código siguiente para asegurarse de que el archivo `NuGet.Config` se sincronizará con Azure de forma que se utilice durante el proceso de creación de la imagen de contenedor. (De forma predeterminada, Azure Dev Spaces no sincroniza los archivos que coinciden con las reglas `.gitignore` y `.dockerignore`).

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Pasos siguientes

Una vez completados los pasos anteriores, la próxima vez que ejecute `azds up` (o presione `F5` en VSCode o Visual Studio), Azure Dev Spaces sincronizará el archivo `NuGet.Config` en Azure, que, a continuación, lo utilizará `dotnet restore` para instalar las dependencias de paquete en el contenedor.

