---
title: "Implementación de un contenedor de Docker de ASP.NET Core para Linux en un host remoto de Docker | Microsoft Docs"
description: "Aprenda a usar Visual Studio Tools para Docker para implementar una aplicación web de ASP.NET Core en un contenedor de Docker que se ejecute en una máquina virtual Linux de host de Docker de Azure"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Implementación de un contenedor ASP.NET en un host remoto de Docker
## <a name="overview"></a>Información general
Docker es un motor de contenedor ligero, semejante de alguna manera a una máquina virtual, que puede utilizar para hospedar aplicaciones y servicios.
Este tutorial le guiará a través del uso de la extensión de [Visual Studio Tools para Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) para implementar una aplicación ASP.NET Core en un host de Docker en Azure mediante PowerShell.

## <a name="prerequisites"></a>Requisitos previos
El siguiente requisito es necesario para completar este tutorial:

* Crear una VM de host de Docker de Azure tal y como se describe en [Uso de una máquina de Docker con el controlador de Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Instalar la versión más reciente de [Visual Studio](https://www.visualstudio.com/downloads/)
* Descargar [SDK de Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
* Instalar [Docker para Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Cree una aplicación web ASP.NET Core
Los siguientes pasos le guían en el proceso de creación de una aplicación ASP.NET Core básica que se usará en este tutorial.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Agregue compatibilidad con Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Use el script de PowerShell DockerTask.ps1
1. Abra un símbolo del sistema de PowerShell en el directorio raíz del proyecto. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Asegúrese de que el host remoto se está ejecutando. Debería ver el estado = En ejecución 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. Compile la aplicación con el parámetro -Build
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Ejecute la aplicación mediante el parámetro -Run
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Una vez que Docker termina, debería ver resultados similares a los siguientes:
   
   ![Vea la aplicación.][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
