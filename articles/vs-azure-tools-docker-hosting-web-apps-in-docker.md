---
title: Implementación de un contenedor de Docker de ASP.NET Core para Linux en un host remoto de Docker | Microsoft Docs
description: Aprenda a usar Visual Studio Tools para Docker para implementar una aplicación web de ASP.NET Core en un contenedor de Docker que se ejecute en una máquina virtual Linux de host de Docker de Azure
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''

ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned

---
# Implementación de un contenedor ASP.NET en un host remoto de Docker
## Información general
Docker es un motor de contenedor ligero, semejante de alguna manera a una máquina virtual, que puede utilizar para hospedar aplicaciones y servicios. Este tutorial le guiará a través del uso de la extensión de [Visual Studio 2015 Tools para Docker](http://aka.ms/DockerToolsForVS) para implementar una aplicación ASP.NET Core en un host de Docker en Azure mediante PowerShell.

## Requisitos previos
Necesita lo siguiente para completar este tutorial:

* Crear una VM de host de Docker de Azure tal y como se describe en [Uso de una máquina de Docker con el controlador de Azure](virtual-machines/virtual-machines-linux-docker-machine.md).
* Instalar [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)
* [SDK de Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
* Instalar [Visual Studio 2015 Tools para Docker - Preview](http://aka.ms/DockerToolsForVS)

## 1\. Cree una aplicación web ASP.NET Core
Los siguientes pasos lo guiarán en el proceso de creación de una aplicación ASP.NET Core básica que se usará en este tutorial.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Agregue compatibilidad con Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3\. Use el script de PowerShell DockerTask.ps1
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
   
   > [!NOTE]
   > Si utiliza la versión beta de Docker, el host no aparecerá aquí.
   > 
   > 
3. Compile la aplicación con el parámetro -Build
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  
   
   > [!NOTE]
   > Si está utilizando la versión beta de Docker, omita el argumento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Ejecute la aplicación mediante el parámetro -Run
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > [!NOTE]
   > Si está utilizando la versión beta de Docker, omita el argumento -Machine
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Una vez que Docker termina, debería ver resultados similares a los siguientes:
   
   ![Vea la aplicación.][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0921_2016-->