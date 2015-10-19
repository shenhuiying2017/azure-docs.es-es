<properties
   pageTitle="Configurar el entorno de desarrollo de Service Fabric | Microsoft Azure"
   description="Instalar las herramientas, el SDK y el motor en tiempo de ejecución de Service Fabric y cree un clúster de desarrollo local."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/07/2015"
   ms.author="seanmck"/>

# Configurar el entorno de desarrollo de Service Fabric
 Este artículo abarca todo lo que necesita para empezar a crear las aplicaciones de [Service Fabric][1], incluida la instalación del tiempo de ejecución, el SDK y las herramientas, y la configuración de un clúster local.

 >[AZURE.NOTE]Estas instrucciones están pensadas para configurar nuevos equipos. Si ha instalado una versión anterior de Service Fabric en su PC, siga las [instrucciones para actualizar su entorno de desarrollo](service-fabric-update-your-development-environment.md).

## Requisitos previos
### Versiones de sistema operativo compatibles
Se admiten las siguientes versiones de sistemas operativos:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Las herramientas de Service Fabric dependen de Visual Studio 2015, que encontrará [aquí][2].

> [AZURE.NOTE]Si no está ejecutando una de las versiones de sistema operativo compatibles o prefiere no instalar Visual Studio 2015 en su PC, puede configurar una máquina virtual de Azure con Windows Server 2012 R2 y Visual Studio 2015 preinstalados usando una imagen de la Galería de máquinas virtuales.

## Instalar el motor en tiempo de ejecución, el SDK y las herramientas

La instalación de los componentes de Service Fabric se realiza mediante el instalador de plataforma web. Siga estas instrucciones para instalar:

1. [Descargue el SDK][3] con el instalador de plataforma web.

2. Haga clic en **Instalar** para comenzar el proceso de instalación.

3. Revise y acepte el CLUF.

La instalación continuará automáticamente.

## Habilitar la ejecución del script de PowerShell

Service Fabric usa scripts de Windows PowerShell para crear un clúster de desarrollo local e implementar aplicaciones desde Visual Studio. De forma predeterminada, Windows bloqueará la ejecución de estos scripts. Para habilitarlos, debe modificar la directiva de ejecución de PowerShell. Abra PowerShell como administrador y escriba el siguiente comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Instalar e iniciar un clúster local
Un clúster local representa la topología de varias máquina que usará eventualmente en producción en una máquina de desarrollo única. Para configurar el clúster local, siga estos pasos:


1. Cierre todas las demás ventanas de PowerShell e inicie una nueva como administrador.

2. Desplácese hasta el directorio de instalación del clúster.

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```

3. Ejecute

    ```powershell
    .\DevClusterSetup.ps1
    ```

En unos instantes debería ver la salida que muestra información de nodo y la confirmación de que el clúster se creó correctamente. En algunos casos, puede ver advertencias mientras se inician el servicio de host de Service Fabric y los servicios de nomenclatura. Estos son normales e irán seguidos momentáneamente de cierta información básica sobre el clúster.

> [AZURE.NOTE]Su clúster local usa exactamente el mismo tiempo de ejecución que lo que se ejecutará en Azure. No es simula o emula de ningún modo. La única diferencia es que todos sus nodos se ejecutan en una máquina única, en lugar de distribuirse en varias máquinas como estarán en Azure.

## Validar su configuración de clúster

Puede comprobar que su clúster se ha creado correctamente mediante la herramienta Explorador de Service Fabric que se distribuye con el SDK.

1. Inicie el Explorador de Service Fabric mediante la ejecución de

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. Expanda el nodo Onebox/Local Cluster en la esquina superior izquierda.

3. Asegúrese de que las vistas de Aplicación y Nodo son verdes.

Si cualquier elemento no es de color verde o si ve un error, espere unos instantes y haga clic en el botón Actualizar. Si sigue teniendo problemas, consulte los [pasos para solucionar problemas de instalación](service-fabric-troubleshoot-local-cluster-setup.md).

## Pasos siguientes
Ahora que está configurado su entorno de desarrollo, puede iniciar la creación y ejecución de aplicaciones.

- [Más información sobre los modelos de programación: actores confiables y servicios de confianza](service-fabric-choose-framework.md)
- [Introducción a la API de servicios de confianza](service-fabric-reliable-services-quick-start.md)
- [Introducción a la API de servicios confiables](service-fabric-reliable-actors-get-started.md)
- [Consultar los ejemplos de Service Fabric en GitHub](https://github.com/azure/servicefabric-samples)
- [Visualizar el clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página de campaña de Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Vínculo de WebPI"

<!---HONumber=Oct15_HO2-->