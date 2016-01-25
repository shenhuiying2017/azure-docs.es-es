<properties
   pageTitle="Actualizar el entorno de desarrollo de Service Fabric | Microsoft Azure"
   description="Actualizar el entorno de desarrollo de Service Fabric para usar el motor en tiempo de ejecución, el SDK y las herramientas más recientes."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="seanmck"/>

# Actualizar el entorno de desarrollo de Service Fabric

 Azure Service Fabric ofrece periódicamente nuevas versiones del tiempo de ejecución, el SDK y las herramientas para su uso en el desarrollo local. Al mantener el entorno de desarrollo local actualizado con estas versiones, puede asegurarse de siempre tener acceso a las últimas funciones, correcciones de errores y mejoras de rendimiento al crear y probar sus aplicaciones localmente.

## Limpiar el clúster local

 Service Fabric no admite actualmente la actualización del motor en tiempo de ejecución de Service Fabric mientras se ejecuta un clúster local. Para garantizar una actualización limpia, es importante limpiar primero el clúster local.

 >[AZURE.NOTE]Al limpiar su clúster local se quitarán todas las aplicaciones implementadas y sus datos.

 Puede limpiar su clúster local de la siguiente manera:


 1. Cierre todas las ventanas de PowerShell y abra una nueva como administrador.

 2. Desplácese hasta el directorio de configuración del clúster con `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`

 3. Ejecute `.\CleanCluster.ps1`.


## Actualizar el motor en tiempo de ejecución, el SDK y las herramientas

 Cuando haya limpiado correctamente el clúster existente, puede continuar con la actualización de la siguiente manera:


 1. Inicie el instalador de la plataforma web para [actualizar a la nueva versión][1].

 2. Al finalizar, abra una nueva ventana de PowerShell como administrador y desplácese hasta el directorio de configuración del clúster con `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"`.

 3. Ejecute `.\DevClusterSetup.ps1` para configurar su clúster local.

Eso es todo. Ahora puede iniciar Visual Studio y continuar creando aplicaciones de Service Fabric.

>[AZURE.NOTE]La estructura de proyecto predeterminada ha cambiado en esta versión. Podrá abrir y ejecutar los proyectos existentes en Visual Studio. Sin embargo, si tiene problemas con la compilación, implementación o depuración de sus aplicaciones, considere la posibilidad de crear un nuevo proyecto y migrar allí su código.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Vínculo de WebPI"

<!---HONumber=AcomDC_0114_2016-->