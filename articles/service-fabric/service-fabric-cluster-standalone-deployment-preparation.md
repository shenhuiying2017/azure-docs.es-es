---
title: "Preparación de la implementación de un clúster independiente de Azure Service Fabric | Microsoft Docs"
description: "Documentación relacionada con la preparación del entorno y la creación de la configuración del clúster, que se debe tener en cuenta antes de implementar un clúster dirigido a administrar una carga de trabajo de producción."
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
ms.date: 1/17/2017
ms.author: maburlik;chackdan
translationtype: Human Translation
ms.sourcegitcommit: c525925cc581b3c95db330a24799056d6aade92d
ms.openlocfilehash: 35504d3885c09c142502edf35efc36286b3f9024
ms.lasthandoff: 02/16/2017


---

<a id="preparemachines"></a>

## <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planeamiento y preparación del desarrollo de un clúster independiente de Service Fabric
Realice los pasos siguientes antes de crear el clúster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Paso 1: Planear la infraestructura de clúster
Va a crear un clúster de Service Fabric en máquinas que posee, por lo que podrá decidir qué tipos de errores desea que resista el clúster. Por ejemplo, ¿necesita separar redes eléctricas o conexiones a Internet que proporcionan estas máquinas? Además, debe tener en cuenta la seguridad física de estas máquinas. ¿Donde se encuentran las máquinas y quién tiene acceso a ellas? Una vez que haya tomado estas decisiones, asigne las máquinas de manera lógica a los distintos dominios de error (vea el paso 4). El planeamiento de la infraestructura para clústeres de producción es más complicado que el de los clústeres de prueba.

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Paso 2: Preparar las máquinas para cumplir los requisitos previos
Requisitos previos para cada máquina que desee agregar al clúster:

* Se recomiendan como mínimo 16 GB de RAM.
* Se recomiendan 40 GB de espacio disponible en disco como mínimo.
* Se recomienda una CPU de cuatro núcleos o más.
* Conectividad a una o varias redes seguras para todas las máquinas.
* Windows Server 2012 R2 o Windows Server 2016. 
* [.NET Framework 4.5.1 o posterior](https://www.microsoft.com/download/details.aspx?id=40773)(instalación completa).
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* El [servicio RemoteRegistry](https://technet.microsoft.com/library/cc754820) se debe estar ejecutando en todas las máquinas.

El administrador de clúster que implemente y configure el clúster debe tener [privilegios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) en cada una de las máquinas. No se puede instalar Service Fabric en un controlador de dominio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Paso 3: Determinar el tamaño inicial del clúster
Cada nodo de un clúster de Service Fabric independiente tiene el sistema en tiempo de ejecución de Service Fabric implementado y es miembro del clúster. En una implementación típica de producción, hay un nodo por cada instancia de sistema operativo (física o virtual). El tamaño del clúster depende de sus necesidades empresariales. Sin embargo, debe tener un tamaño mínimo de clúster de tres nodos (máquinas o máquinas virtuales).
Con fines de desarrollo, puede tener más de un nodo en una máquina determinada. En un entorno de producción, Service Fabric solo admite un nodo por máquina virtual o física.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Paso 4: Determinar el número de dominios de error y de actualización
Un *dominio de error* (FD) es una unidad física de error, y está directamente relacionado con la infraestructura física de los centros de datos. Un dominio de error consta de componentes de hardware (equipos, conmutadores, redes, etc.) que comparten un único punto de error. Aunque no hay ninguna asignación 1:1 entre dominios de error y racks, en términos generales, cada bastidor puede considerarse un dominio de error. A la hora de valorar los nodos del clúster, se recomienda que los nodos se distribuyan entre tres dominios de error como mínimo.

Si especifica dominios de error en ClusterConfig.json, puede elegir el nombre de cada uno. Service Fabric admite FD jerárquicos, así que puede reflejar la topología de infraestructura en ellos.  Por ejemplo, los siguientes FD son válidos:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Un *dominio de actualización* (UD) es una unidad lógica de nodos. Durante una actualización de Service Fabric organizada (una actualización de la aplicación o del clúster), todos los nodos de un UD se eliminan para llevar a cabo este proceso, mientras que los nodos de otros UD siguen estando disponibles para atender las solicitudes. Las actualizaciones de firmware que realice en las máquinas no respetan los UD, así que debe realizar este proceso de forma individual.

La manera más sencilla de pensar en estos conceptos es considerar los FD como la unidad de error no planeada, y los UD, como la unidad de mantenimiento planeado.

Si especifica dominios de actualización en ClusterConfig.json, puede elegir el nombre de cada uno. Por ejemplo, los siguientes nombres son válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Para más información sobre los dominios de actualización y los dominios de error, vea [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Paso 5: Descarga del paquete independiente de Windows Server para Service Fabric
[Descargue el paquete independiente de Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) y descomprímalo en una máquina de implementación que no forme parte del clúster, o bien en una de las máquinas que formarán parte del clúster.

### <a name="step-6-modify-cluster-configuration"></a>Paso 6: Modificación de la configuración del clúster
Para crear un clúster independiente, debe crear un archivo ClusterConfig.json de configuración del clúster independiente, que describa la especificación del clúster. Puede basar el archivo de configuración en las plantillas que se encuentran en el siguiente vínculo. <br>
[Configuraciones de clúster independiente](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para ver detalles sobre las secciones de este archivo, vea [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md).

Abra uno de los archivos ClusterConfig.json del paquete descargado y modifique las siguientes opciones:
| **Opciones de configuración.** | **Descripción** |
| --- | --- |
| **NodeTypes** |Los tipos de nodo permiten separar los nodos de clúster en grupos distintos. Un clúster debe tener al menos un tipo NodeType. Todos los nodos de un grupo tienen las siguientes características comunes:  <br> **nombre** : es el nombre del tipo de nodo. <br>**Endpoint Ports** : diversos puntos de conexión (puertos) con nombre asociados a este tipo de nodo. Puede usar cualquier número de puerto que desee, siempre que no entren en conflicto con nada de este manifiesto y que no estén siendo usados por otra aplicación que se ejecute en el equipo o en la máquina virtual. <br> **Propiedades de ubicación**: Describen las propiedades de este tipo de nodo que se usan como restricciones de ubicación para los servicios del sistema o para sus servicios. Estas propiedades son pares de clave y valor definidos por el usuario que proporcionan metadatos adicionales para un nodo determinado. Algunos ejemplos de propiedades de nodo son si el nodo tiene una unidad de disco duro o una tarjeta gráfica, el número de ejes de la unidad de disco duro, los núcleos y otras propiedades físicas. <br> **Capacities** : las capacidades del nodo definen el nombre y la cantidad de un recurso concreto que un nodo tiene disponible para consumir. Por ejemplo, un nodo puede definir que tenga capacidad para una métrica llamada "MemoryInMb" y 2048 MB de memoria disponible de forma predeterminada. Estas funcionalidades se usan en tiempo de ejecución para garantizar que los servicios que requieren una cantidad determinada de recursos se coloquen en nodos donde esos recursos estén disponibles en la cantidad necesaria.<br>**IsPrimary** : si hay varias opciones NodeType definidas, asegúrese de establecer solo una como principal con el valor *true*; es donde se ejecutan los servicios del sistema. Todos los demás tipos de nodo se deben establecer en *false* |
| **Nodos** |Se trata de los detalles de cada uno de los nodos que forman parte del clúster (tipo de nodo, nombre de nodo, dirección IP, dominio de error y dominio de actualización del nodo). Las máquinas en las que desee que se cree el clúster en función de las necesidades se mostrarán aquí con sus direcciones IP. <br> Si usa la misma dirección IP para todos los nodos, se crea un clúster one-box, que puede usar con fines de prueba. No use clústeres one-box para implementar cargas de trabajo de producción. |

Después de que se han realizado todas las configuraciones del clúster de acuerdo con el entorno, se puede probar en el entorno del clúster (paso 7).

<a id="environmentsetup"></a>

### <a name="step-7-environment-setup"></a>Paso 7. Configuración del entorno

Cuando un administrador de clústeres configura un clúster independiente de Service Fabric, es necesario configurar el entorno con los siguientes criterios: <br>
1. El usuario que crea el clúster debe tener privilegios de seguridad de nivel de administrador en todas las máquinas que se enumeran como nodos en el archivo de configuración del clúster.
2. La máquina desde la que se crea el clúster, así como la máquina de cada nodo del clúster debe cumplir estos requisitos:
* Tener desinstalado el SDK de Service Fabric
* tener desinstalado el entorno de tiempo de ejecución de Service Fabric 
* Tener habilitado el servicio Firewall de Windows (mpssvc)
* Tener habilitado el servicio Registro remoto (remoteregistry)
* Tener habilitado el uso compartido de archivos (SMB)
* Tener abiertos los puertos necesarios, según los puertos de configuración del clúster
* Tener abiertos los puertos necesarios para Windows SMB y el servicio Registro remoto: 135, 137, 138, 139 y 445
* Tener conectividad de red con otra máquina
3. Ninguna de las máquinas de nodo del clúster debe ser un controlador de dominio.
4. Si el clúster que se va a implementar es un clúster seguro, valide que los requisitos previos de seguridad necesarios estén implementados y que se ajusten a la configuración.
5. Si las máquinas del clúster no son accesibles desde Internet, establezca lo siguiente en la configuración del clúster:
* Deshabilite la telemetría: en *Propiedades*, establezca   *"enableTelemetry" en false*.
* Deshabilite la descarga de versiones automáticas de Fabric y las notificaciones de que la versión actual del clúster está próxima a finalizar el soporte técnico: en *Propiedades*, establezca   *"fabricClusterAutoupgradeEnabled" en true*.
6. Establezca las exclusiones adecuadas del antivirus de Service Fabric:

| **Directorios excluidos del antivirus** |
| --- |
| Archivos de programa\Microsoft Service Fabric |
| FabricDataRoot (de la configuración del clúster) |
| FabricLogRoot (de la configuración del clúster) |

| **Procesos excluidos del antivirus** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

### <a name="step-8-validate-environment-using-testconfiguration-script"></a>Paso 8. Validación del entorno mediante el script TestConfiguration
El script TestConfiguration.ps1 se puede encontrar en el paquete independiente. Se usa como Analizador de procedimientos recomendados para validar algunos de los criterios anteriores y debe usarse como una comprobación de integridad para validar si un clúster puede implementarse en un entorno determinado. Si se produce cualquier error, consulte la lista que aparece en [Configuración del entorno](service-fabric-cluster-standalone-deployment-preparation.md) para solucionar el problema. 

Este script puede ejecutarse en cualquier máquina que tenga acceso de administrador a todas las máquinas que se muestran como nodos en el archivo de configuración del clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Actualmente, este módulo de prueba de configuración no valida la configuración de seguridad, así que se debe realizar de forma independiente.  

> [!NOTE]
> Continuamente realizamos mejoras para conseguir que este módulo sea más robusto; por tanto, si cree que hay algún caso incorrecto o que falta que no está contemplado en TestConfiguration, comuníquenoslo a través de nuestros [canales de soporte técnico](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un clúster independiente con Windows Server](service-fabric-cluster-creation-for-windows-server.md)

