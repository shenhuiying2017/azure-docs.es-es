---
title: Paquete independiente de Azure Service Fabric para Windows Server | Microsoft Docs
description: "Descripción y contenido del paquete independiente de Azure Service Fabric para Windows Server."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik
ms.openlocfilehash: 6a6bacedde04419449a061554d5ce9ad37259a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Contenido del paquete independiente de Service Fabric para Windows Server
En el paquete independiente de Service Fabric [descargado](http://go.microsoft.com/fwlink/?LinkId=730690), encontrará los siguientes archivos:

| **Nombre de archivo** | **Descripción breve** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Un script de PowerShell que crea el clúster mediante la configuración de ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Un script de PowerShell que elimina el clúster mediante la configuración de ClusterConfig.json. |
| AddNode.ps1 |Un script de PowerShell para agregar un nodo a un clúster implementado existente en el equipo actual. |
| RemoveNode.ps1 |Un script de PowerShell para quitar un nodo de un clúster implementado existente del equipo actual. |
| CleanFabric.ps1 |Un script de PowerShell para limpiar una instalación independiente de Service Fabric de la máquina actual. Las instalaciones anteriores de MSI se deben quitar mediante sus propios desinstaladores asociados. |
| TestConfiguration.ps1 |Un script de PowerShell para analizar la infraestructura como se especifica en Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Un script de PowerShell que se utiliza para descargar el paquete en tiempo de ejecución más reciente fuera de banda, para situaciones en las que el equipo donde se vaya a implementar no esté conectado a Internet. |
| DeploymentComponentsAutoextractor.exe |Archivo autoextraíble que contiene los componentes de implementación que usan los scripts del paquete independiente. |
| EULA_ENU.txt |Términos de licencia para usar el paquete independiente de Windows Server para Microsoft Azure Service Fabric. Puede [descargar una copia de los términos de licencia](http://go.microsoft.com/fwlink/?LinkID=733084) ahora. |
| Readme.txt |Un vínculo a las notas de la versión e instrucciones de instalación básica. Se trata de un subconjunto de las instrucciones de este documento. |
| ThirdPartyNotice.rtf |Aviso de software de terceros que está en el paquete. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, que se ejecuta a petición para recopilar y cargar registros de seguimiento en Microsoft para soporte técnico. |
| Tools\ServiceFabricUpdateService.zip |Herramienta que se usa para habilitar la actualización automática de código en los clústeres que no tienen acceso a Internet. [aquí](service-fabric-cluster-upgrade-windows-server.md)|

**Plantillas** 
| **Nombre de archivo** | **Descripción breve** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster de desarrollo no seguro con una sola máquina (o máquina virtual) y tres nodos, incluidos los datos de cada nodo del clúster. |
| ClusterConfig.Unsecure.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster no seguro con varias máquinas (o máquinas virtuales), incluida la información para cada máquina del clúster. |
| ClusterConfig.Windows.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina (o máquina virtual) y tres nodos, incluidos los datos de cada nodo del clúster. El clúster está protegido mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene la configuración de un clúster seguro con varias máquinas (o máquinas virtuales) mediante la utilización de la seguridad de Windows, incluida la información para cada máquina del clúster seguro. El clúster está protegido mediante [identidades de Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster de desarrollo seguro con una sola máquina (o máquina virtual) y tres nodos, incluida la información de cada nodo del clúster. El clúster se protege mediante certificados X509. |
| ClusterConfig.x509.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster seguro con varias máquinas (o máquinas virtuales), incluida la información para cada nodo del clúster seguro. El clúster se protege mediante certificados X509. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Archivo de ejemplo de configuración de clúster que contiene toda la configuración de un clúster seguro con varias máquinas (o máquinas virtuales), incluida la información para cada nodo del clúster seguro. El clúster se protege mediante [cuentas de servicio administradas de grupo](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Ejemplos de configuración del clúster
Las últimas versiones de las plantillas de configuración de clústeres se encuentran en la página de GitHub dedicada a los [ejemplos de configuración de clústeres independientes](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Paquete del entorno en tiempo de ejecución independiente
El paquete del entorno en tiempo de ejecución más reciente se descarga automáticamente durante la implementación del clúster de [Vínculo de descarga - Entorno en tiempo de ejecución de Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Temas relacionados
* [Creación de un clúster de Azure Service Fabric independiente](service-fabric-cluster-creation-for-windows-server.md)
* [Escenarios de seguridad de los clústeres de Service Fabric](service-fabric-windows-cluster-windows-security.md)
