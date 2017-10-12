---
title: "Supervisión y diagnóstico de contenedores de Azure Service Fabric | Microsoft Docs"
description: Aprenda a supervisar y diagnosticar contenedores organizados en Microsoft Azure Service Fabric con soluciones Containers de OMS.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: dekapur
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Supervisión de contenedores de Windows Server con OMS

## <a name="oms-containers-solution"></a>Solución Containers de OMS

El equipo de Operations Management Suite (OMS) ha publicado una solución Containers para el diagnóstico y la supervisión de contenedores. Junto con su solución de Service Fabric, esta solución es una herramienta excelente para supervisar las implementaciones de contenedores que se organizan en Service Fabric. Este es un ejemplo sencillo de aspecto del panel de la solución:

![Panel de OMS básico](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

También recopila diferentes tipos de registros que se pueden consultar en la herramienta Log Analytics de OMS y se puede usarse para visualizar las métricas o eventos que se generan. Los tipos de registro que se recopilan son:

1. ContainerInventory: muestra información acerca de la ubicación, nombre e imágenes del contenedor
2. ContainerImageInventory: información acerca de las imágenes implementadas, lo que incluye sus identificadores o tamaños
3. ContainerLog: registros de error concretos, registros de docker (stdout, etc.) y otras entradas
4. ContainerServiceLog: comandos de demonio de docker que se han ejecutado
5. Perf: contadores de rendimiento, entre los que se incluyen la cpu del contenedor, la memoria, el tráfico de red, la e/s de disco y métricas personalizadas de los equipos host

En este artículo se tratan los pasos necesarios para configurar la supervisión del contenedor para el clúster. Para más información acerca de la solución Containers de OMS, consulte su [documentación](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Configuración de un clúster de Service Fabric

Cree un clúster de Service Fabric mediante la plantilla de Azure Resource Manager que encontrará [aquí](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample). Asegúrese de agregar un nombre de área de trabajo de OMS único. Esta plantilla también tiene como valor predeterminado la implementación de un clúster en la versión preliminar de Service Fabric (v255.255), lo que significa que no se puede usar en producción y no se puede actualizar a otra versión de Service Fabric. Si decide usar esta plantilla para el uso de producción o a largo plazo, cambie la versión a un número de versión estable.

Una vez que se ha configurado el clúster, confirme que ha instalado el certificado adecuado y asegúrese de que puede conectarse al clúster.

Confirme que el grupo de recursos se ha configurado correctamente, para lo que debe dirigirse a [Azure Portal](https://portal.azure.com/) y buscar la implementación. El grupo de recursos debe contener todos los recursos de Service Fabric y también tener una solución de Log Analytics, así como una solución de Service Fabric.

Para modificar un clúster de Service Fabric existente:
* Confirme que Diagnostics esté habilitado (si no lo está, habilítelo mediante la [actualización del conjunto de escalado de máquinas virtuales](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Agregue un área de trabajo de OMS mediante la creación de una solución "Service Fabric Analytics" a través de Azure Marketplace
* Edite los orígenes de datos de la solución Service Fabric para recoger datos de las tablas de Azure Storage adecuadas (configuradas por WAD) en el grupo de recursos en el que se encuentra el clúster
* Agregue el agente como [extensión al conjunto de escalado de máquinas virtuales](/powershell/module/azurerm.compute/add-azurermvmssextension) a través de PowerShell o mediante la actualización del conjunto de escalado de máquinas virtuales (el mismo vínculo que antes, para modificar la plantilla de Resource Manager)

## <a name="2-deploy-a-container"></a>2. Implementación de un contenedor

Una vez que el clúster esté listo y que ha confirmado que puede acceder a él, implemente un contenedor en él. Si ha elegido usar la versión preliminar que establece la plantilla, también puede explorar la nueva funcionalidad de Docker Compose de Service Fabric. Tenga en cuenta que la primera vez que una imagen del contenedor se implementa en un clúster, se tardan varios minutos en descargar la imagen según su tamaño.

## <a name="3-add-the-containers-solution"></a>3. Incorporación de la solución Containers

En Azure Portal, cree un recurso de Containers (en la categoría Supervisión y administración) a través de Azure Marketplace. 

![Adición de la solución Containers](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

En el paso de creación, solicita un área de trabajo de OMS. Seleccione el que se creó con la implementación anterior. Este paso agrega una solución Containers al área de trabajo de OMS y la detecta automáticamente el agente de OMS implementado por la plantilla. El agente empezará a recopilar datos sobre los procesos de contenedores del clúster y, en aproximadamente 10-15 minutos, debería ver que aparecen los datos, como se muestra en la imagen del panel anterior.

## <a name="4-next-steps"></a>4. Pasos siguientes

OMS ofrece en el área de trabajo varias herramientas que hacen que sea más útil para el usuario. Explore las siguientes opciones para personalizar la solución para ajustarla a sus necesidades:
- Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
- Configure el agente de OMS para que recoja contadores de rendimiento concretos (vaya a la página principal del área de trabajo > Configuración > Datos >Contadores de rendimiento de Windows)
- Configure OMS para establecer reglas de [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y diagnóstico