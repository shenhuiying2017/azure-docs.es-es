<properties
   pageTitle="Implementación de un contenedor ASP.NET en un host remoto de Docker | Microsoft Azure"
   description="Aprenda a usar Visual Studio Tools para Docker para publicar una aplicación web ASP.NET 5 en un contenedor de Docker que se ejecute en una máquina de host de Docker de Azure"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="tomarcher"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/26/2016"
   ms.author="tarcher"/>

# Implementación de un contenedor ASP.NET en un host remoto de Docker

## Información general
Docker es un motor de contenedor ligero, semejante de alguna manera a una máquina virtual, que puede utilizar para hospedar aplicaciones y servicios. Visual Studio admite Docker en Ubuntu, CoreOS y Windows. Este tutorial le guiará a través del uso de la extensión de [Visual Studio 2015 Tools para Docker](http://aka.ms/DockerToolsForVS) para publicar una aplicación ASP.NET 5 en un host de Docker en Azure.

## 1\. Requisitos previos
Necesita lo siguiente para completar este tutorial:

- Crear una VM de host de Docker de Azure tal y como se describe en [How to use docker-machine with Azure](./virtual-machines/virtual-machines-linux-classic-docker-machine.md) (Uso de las máquinas de Docker con Azure)
- Instalar [Visual Studio 2015](https://www.visualstudio.com/es-ES/downloads/download-visual-studio-vs.aspx)
- Instalar [Visual Studio 2015 Tools para Docker - Preview](http://aka.ms/DockerToolsForVS)

## 2\. Cree una aplicación web ASP.NET 5
Los siguientes pasos le guiarán a través del proceso de creación de una aplicación ASP.NET 5 básica que se usará en este tutorial.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Agregue compatibilidad con Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. Apunte al host remoto de Docker

1.  En el **Explorador de soluciones** de Visual Studio busque la carpeta **Propiedades** y expándala.
1.  Abra el archivo *Docker.props*.

    ![Abra el archivo Docker.props.][0]

1.  Cambie el valor de **DockerMachineName** por el nombre del host de Docker remoto. Si no conoce el nombre del host de Docker remoto, ejecute ```docker-machine ls``` en el símbolo del sistema de Windows PowerShell. Utilice el valor que aparece bajo la columna **Nombre** para el host deseado.

    ![Cambie el nombre de la máquina de Docker.][1]

1.  Reinicie Visual Studio.

## 5\. Configure el punto de conexión del host de Docker de Azure
Antes de implementar la aplicación de Visual Studio en Azure, agregue el punto de conexión 80 a la máquina virtual del host de Docker para que pueda ver la aplicación desde el explorador más adelante. Esto puede hacerse bien a través del Portal de Azure clásico bien a través de Windows PowerShell:

- **Si utiliza el Portal de Azure clásico para configurar el punto de conexión del host de Docker de Azure:**

    1.  Entre en el [Portal de Azure clásico](https://manage.windowsazure.com/). 
    
    1.  Pulse en **MÁQUINAS VIRTUALES**.
    
    1.  Seleccione la máquina virtual del host de Docker.
    
    1.  Pulse la pestaña **ENDPOINTS** (EXTREMOS).
    
    1.  Haga clic en **AGREGAR** (en la parte inferior de la página).
    
    1.  Siga las instrucciones para exponer el puerto 80, que lo usa el script de implementación de manera predeterminada.

- **Si utiliza Windows PowerShell para configurar el punto de conexión del host de Docker de Azure**

    1. Abrir Windows PowerShell
    1. Escriba el siguiente comando en el símbolo del sistema de Windows PowerShell (cambiando los valores entre corchetes angulares para que coincida con su entorno):  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. Compile y ejecute la aplicación
Al realizar implementaciones en hosts remotos, la característica de asignación de volumen usada para el desarrollo de edición y actualización no funcionará. Por lo tanto, deberá utilizar la *configuración Release* al compilar la aplicación para evitar la configuración de la asignación de volumen. Siga estos pasos para ejecutar la aplicación.

1.  En la barra de herramientas de Visual Studio, seleccione la configuración **Release**.

1.  Cambie el destino de inicio a **Docker**.

1.  Pulse el icono de **Docker** para compilar y ejecutar la aplicación.

![Inicie la aplicación.][2]

Debería ver resultados similares a los siguientes:

![Vea la aplicación.][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0330_2016-->