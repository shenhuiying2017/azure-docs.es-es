---
title: 'Tutorial de instalación del cliente de Service Fabric independiente: Azure Service Fabric | Microsoft Docs'
description: En este tutorial aprenderá a instalar el cliente independiente de Service Fabric en el clúster que creó en el tutorial del artículo anterior.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: b9b9e08676228ddbdea8be91253b41dac8ef8fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211742"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Tutorial: instalación y creación del clúster de Service Fabric

Los clústeres de Service Fabric independientes ofrecen la opción de elegir un entorno propio y crear un clúster como parte del enfoque "Cualquier sistema operativo, cualquier nube" que toma Service Fabric. En esta serie de tutoriales se crea un clúster independiente hospedado en AWS y se instala en él una aplicación.

Este tutorial es la segunda parte de una serie. Este tutorial le guía por los pasos para crear un clúster independiente de Service Fabric.

En la segunda parte de la serie, se aprende a:

> [!div class="checklist"]
> * Descargar e instalar del paquete independiente de Service Fabric
> * Crear el clúster de Service Fabric
> * Conectarse al clúster de Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Descarga del paquete de Service Fabric para Windows Server

Service Fabric proporciona un paquete de instalación para crear clústeres independientes de Service Fabric.  [Descargue el paquete de instalación](http://go.microsoft.com/fwlink/?LinkId=730690) en el equipo local.  Una vez descargado correctamente, cópielo a través de la conexión RDP en la instancia de EC2 y péguelo en el escritorio.

Seleccione el archivo ZIP, abra el menú contextual y seleccione **Extract All** > **Extract** (Extraer todos > Extraer).  Al extraer los archivos se generará una carpeta en el escritorio con el mismo nombre que el del archivo ZIP.

Más detalles sobre el [contenido del paquete de instalación](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Configuración del archivo de configuración

Está compilando un clúster Windows de tres nodos, por lo que necesita modificar el archivo `ClusterConfig.Unsecure.MultiMachine.json`.

A continuación, actualice las tres líneas de dirección IP del archivo en las líneas 8, 15 y 22 con las direcciones IP de las instancias.

Después de actualizar los nodos, aparecen como se indica a continuación:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

A continuación, debe actualizar un par de propiedades.  En la línea 34 debe modificar la cadena de conexión para el almacén de diagnóstico; después de la modificación debe ser similar al siguiente, con la dirección IP reemplazada en `"connectionstring": "\\\\172.31.27.1\\c$\\DiagnosticsStore"`

Después de actualizar la cadena de conexión, asegúrese de crear la carpeta.  El comando siguiente la crea, no olvide reemplazar la dirección IP a continuación por la que insertó en la cadena de conexión:

```powershell
mkdir \\172.31.27.1\c$\DiagnosticsStore
```

Por último, en la sección `nodeTypes` de la configuración, agregue una nueva sección para asignar los puertos efímeros que usará Windows.  El archivo de configuración debería tener un aspecto similar al siguiente:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Validación del entorno

El script *TestConfiguration.ps1* del paquete independiente se usa como analizador de procedimientos recomendados para validar si se puede implementar un clúster en un entorno determinado. En la [preparación para la implementación](service-fabric-cluster-standalone-deployment-preparation.md) se enumeran los requisitos previos y los requisitos del entorno. Ejecute el script para comprobar si puede crear el clúster de desarrollo:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

El resultado debería ser parecido al que se muestra a continuación. Si el campo inferior "Passed" se devuelve como `True`, se han superado las comprobaciones de integridad y, según la configuración de entrada el clúster se puede implementar.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Creación de clústeres

Una vez validada correctamente la configuración del clúster, ejecute el script *CreateServiceFabricCluster.ps1* para implementar el clúster de Service Fabric en las máquinas virtuales del archivo de configuración.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Si todo funciona, obtendrá una salida similar a esta:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Se escriben seguimientos de implementación en la máquina virtual/máquina física en la que ejecutó el script CreateServiceFabricCluster.ps1 de PowerShell. Estos seguimientos se pueden encontrar en la subcarpeta DeploymentTraces, que se encuentra en el directorio desde el que se ejecutó el script. Para ver si Service Fabric se ha implementado correctamente en una máquina, busque los archivos instalados en el directorio FabricDataRoot, como se detalla en la sección FabricSettings del archivo de configuración del clúster (de forma predeterminada c:\ProgramData\SF). También, los procesos FabricHost.exe y Fabric.exe se pueden ver ejecutando el Administrador de tareas.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Incorporación de Service Fabric Explorer

Ahora puede conectarse al clúster con Service Fabric Explorer directamente desde una de las máquinas con http://localhost:19080/Explorer/index.html o de forma remota con http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Agregar y quitar nodos

Puede agregar o quitar nodos del clúster de Service Fabric independiente a medida que cambien las necesidades empresariales. Lea [Incorporación o eliminación de nodos de un clúster de Service Fabric independiente](service-fabric-cluster-windows-server-add-remove-nodes.md) para obtener pasos detallados.

## <a name="next-steps"></a>Pasos siguientes

En la segunda parte de la serie aprendió no solo cómo cargar grandes cantidades de datos aleatorios en una cuenta de almacenamiento en paralelo, si no que también aprendió lo siguiente:

> [!div class="checklist"]
> * Configurar la cadena de conexión
> * Compilar la aplicación
> * Ejecución de la aplicación
> * Validar el número de conexiones

Vaya a la tercera parte de la serie para instalar una aplicación en el clúster que ha creado.

> [!div class="nextstepaction"]
> [Instalación de la aplicación en el clúster de Service Fabric](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png