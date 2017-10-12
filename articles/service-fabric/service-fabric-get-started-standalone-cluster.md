---
title: "Configuración de un clúster de Azure Service Fabric independiente | Microsoft Docs"
description: "Cree un clúster independiente de desarrollo con tres nodos que se ejecutan en el mismo equipo. Después de completar esta configuración, estará listo para crear un clúster de varias máquinas."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Creación del primer clúster independiente de Service Fabric
Puede crear un clúster independiente de Service Fabric en cualquier máquina virtual o equipo que ejecute Windows Server 2012 R2 o Windows Server 2016, en el entorno local o en la nube. Esta guía de inicio rápido lo ayuda a crear un clúster independiente de desarrollo en tan solo unos minutos.  Cuando haya terminado, tendrá un clúster de tres nodos que se ejecuta en un único equipo y donde puede implementar aplicaciones.

## <a name="before-you-begin"></a>Antes de empezar
Service Fabric proporciona un paquete de instalación para crear clústeres independientes de Service Fabric.  [Descargue el paquete de instalación](http://go.microsoft.com/fwlink/?LinkId=730690).  Descomprima el paquete de instalación en una carpeta en el equipo o máquina virtual que está configurando el clúster de desarrollo.  El contenido del paquete de instalación se describe detalladamente [aquí](service-fabric-cluster-standalone-package-contents.md).

El administrador de clúster que implemente y configure el clúster debe tener privilegios de administrador en el equipo. No se puede instalar Service Fabric en un controlador de dominio.

## <a name="validate-the-environment"></a>Validación del entorno
El script *TestConfiguration.ps1* del paquete independiente se usa como analizador de procedimientos recomendados para validar si se puede implementar un clúster en un entorno determinado. En la [preparación para la implementación](service-fabric-cluster-standalone-deployment-preparation.md) se enumeran los requisitos previos y los requisitos del entorno. Ejecute el script para comprobar si puede crear el clúster de desarrollo:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Creación de clústeres
Se instalan varios archivos de configuración de clúster de ejemplo con el paquete de instalación. *ClusterConfig.Unsecure.DevCluster.json* es la configuración de clúster más sencilla: un clúster no seguro de tres nodos que se ejecuta en un único equipo.  Otros archivos de configuración describen clústeres de una o varias máquinas con certificados X.509 o seguridad de Windows.  No es necesario modificar los valores de configuración predeterminados para este tutorial, pero eche un vistazo al archivo de configuración y familiarícese con la configuración.  En la sección **nodos** se describen los tres nodos del clúster: nombre, dirección IP, [tipo de nodo, dominio de error y dominio de actualización](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  En la sección **propiedades** se define la [seguridad, el nivel de confiabilidad, la recopilación de diagnósticos y los tipos de nodos](service-fabric-cluster-manifest.md#cluster-properties) para el clúster.

Este clúster no es seguro.  Cualquiera puede conectarse de forma anónima y realizar operaciones de administración, por lo que los clústeres de producción siempre deben protegerse mediante certificados X.509 o la seguridad de Windows.  La configuración solo se configura en el momento de creación del clúster y no es posible habilitar la seguridad una vez creado.  Lea [Protección de un clúster](service-fabric-cluster-security.md) para más información sobre la seguridad en los clústeres de Service Fabric.  

Para crear el clúster de desarrollo de tres nodos, ejecute el script *CreateServiceFabricCluster.ps1* desde una sesión de PowerShell de administrador:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

El paquete en tiempo de ejecución de Service Fabric se descarga e instala automáticamente cuando se crea el clúster.

## <a name="connect-to-the-cluster"></a>Conexión al clúster
Ahora se está ejecutando el clúster de desarrollo de tres nodos. El módulo ServiceFabric PowerShell está instalado con el paquete en tiempo de ejecución.  Puede comprobar que el clúster se ejecute desde el mismo equipo o desde un equipo remoto con el paquete en tiempo de ejecución de Service Fabric.  El cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) establece una conexión con el clúster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Consulte [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md) para ver ejemplos de conexión a un clúster. Después de conectarse al clúster, use el cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar una lista de nodos en el clúster e información sobre el estado de cada uno. **HealthState** debe ser *OK* para cada nodo.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualización del clúster mediante Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) es una buena herramienta para visualizar el clúster y administrar aplicaciones.  Service Fabric Explorer es un servicio que se ejecuta en el clúster, al que puede acceder mediante un explorador desde [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

El panel del clúster proporciona información general del clúster, incluido un resumen de la aplicación y del estado del nodo. En la vista de nodos se muestra el diseño físico del clúster. Para un nodo determinado, puede comprobar qué aplicaciones tienen el código implementado en ese nodo.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Eliminación del clúster
Para quitar un clúster, ejecute el script de PowerShell *RemoveServiceFabricCluster.ps1* desde la carpeta del paquete y proporcione la ruta al archivo de configuración JSON. También puede especificar una ubicación para el registro que se va a eliminar.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Para quitar el paquete en tiempo de ejecución de Service Fabric del equipo, ejecute el siguiente script de PowerShell desde la carpeta del paquete.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha configurado un clúster independiente de desarrollo, consulte los artículos siguientes:
* [Configure un clúster independiente con varias máquinas](service-fabric-cluster-creation-for-windows-server.md) y habilite la seguridad.
* [Implemente aplicaciones mediante PowerShell](service-fabric-deploy-remove-applications.md).

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
