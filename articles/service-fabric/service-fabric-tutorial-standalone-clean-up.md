---
title: 'Tutorial de limpieza del clúster de Service Fabric independiente: Azure Service Fabric | Microsoft Docs'
description: En este tutorial aprenderá a limpiar el clúster independiente
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
ms.openlocfilehash: d92712200920f135e362e15e1d9536dc2342ebaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211096"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: limpieza del clúster independiente

Los clústeres de Service Fabric independientes ofrecen la opción de elegir un entorno propio y crear un clúster como parte del enfoque "Cualquier sistema operativo, cualquier nube" que toma Service Fabric. En esta serie de tutoriales se crea un clúster independiente hospedado en AWS y se instala en él una aplicación.

Este tutorial es la cuarta parte de una serie. Esta parte del tutorial muestra cómo limpiar los recursos de AWS que creó para hospedar el clúster de Service Fabric.

En la parte número cuatro de la serie, se aprende a:

> [!div class="checklist"]
> * Limpiar un clúster de Service Fabric
> * Limpiar los recursos de AWS

## <a name="clean-up-service-fabric-cluster"></a>Limpiar un clúster de Service Fabric

* Utilice RDP en la instancia de EC2 que usó para instalar Service Fabric
* Abra PowerShell
* Cambie el directorio a la carpeta extraída del segundo tutorial.
* Ejecute el comando siguiente para eliminar el clúster de Service Fabric:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` cuando se le solicite, si se realizó correctamente, la salida tendrá un aspecto similar al siguiente, con sus propias direcciones IP:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Limpieza de los recursos de AWS

* Inicie sesión en la cuenta de AWS.
* Vaya a la consola de EC2.
* Seleccione los tres nodos que creó en la primera parte del tutorial.
* Haga clic en **Actions** > **Instance State** > **Terminate** (Acciones > Estado de instancia > Terminar)

## <a name="next-steps"></a>Pasos siguientes

En la cuarta parte de la serie aprendió a limpiar los recursos que se crearon en los pasos anteriores.

> [!div class="checklist"]
> * Limpiar los recursos

> [!div class="nextstepaction"]
> [Volver al principio](service-fabric-tutorial-standalone-create-infrastructure.md)