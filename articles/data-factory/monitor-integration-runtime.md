---
title: "Supervisión de Integration Runtime en Azure Data Factory | Microsoft Docs"
description: Aprenda a supervisar los distintos tipos de instancia de Integration Runtime en Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: spelluru
ms.openlocfilehash: e1bfb7199ddf9f02297db9de529729ba3833cf8c
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Supervisión de Integration Runtime en Azure Data Factory  
**Integration Runtime** es la infraestructura de proceso que usa Azure Data Factory para proporcionar varias funcionalidades de integración de datos en distintos entornos de red. Data Factory ofrece tres tipos de instancia de Integration Runtime:

- Tiempo de ejecución de integración de Azure
- Integration Runtime autohospedado
- Integration Runtime de SSIS de Azure

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

Para obtener el estado de una instancia de Integration Runtime (IR), ejecute el siguiente comando de PowerShell: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

El cmdlet devuelve información diferente para distintos tipos de instancia de Integration Runtime. En este artículo se explican las propiedades y los estados de cada tipo de instancia de Integration Runtime.  

## <a name="azure-integration-runtime"></a>Tiempo de ejecución de integración de Azure
El recurso de proceso para una instancia de Azure Integration Runtime se puede administrar completamente de manera elástica en Azure. En la siguiente tabla se proporcionan las descripciones de las propiedades que devuelve el comando **Get-AzureRmDataFactoryV2IntegrationRuntime**:

### <a name="properties"></a>Propiedades
En la siguiente tabla se proporcionan las descripciones de las propiedades que devuelve el cmdlet para una instancia de Azure Integration Runtime:

| Propiedad | Descripción |
-------- | ------------- | 
| Nombre | Nombre de la instancia de Azure Integration Runtime. |  
| Estado | Estado de la instancia de Azure Integration Runtime. | 
| Ubicación | Ubicación de la instancia de Azure Integration Runtime. Para más información sobre la ubicación de una instancia de Azure Integration Runtime, consulte la [introducción a Integration Runtime](concepts-integration-runtime.md). |
| DataFactoryName | Nombre de la instancia de Data Factory a la que pertenece la instancia de Azure Integration Runtime. | 
| ResourceGroupName | Nombre del grupo de recursos al que pertenece la instancia de Data Factory.  |
| Descripción | Descripción de la instancia de Integration Runtime.  |

### <a name="status"></a>Estado
En la tabla siguiente se proporcionan los estados posibles de una instancia de Azure Integration Runtime:

| Estado | Comentarios/Escenarios | 
| ------ | ------------------ |
| En línea | La instancia de Azure Integration Runtime está en línea y lista para su uso. | 
| Sin conexión | La instancia de Azure Integration Runtime está desconectada por un error interno. |

## <a name="self-hosted-integration-runtime"></a>Integration Runtime autohospedado
En la siguiente sección se proporcionan las descripciones de las propiedades que devuelve el cmdlet Get AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Las propiedades y estado devueltos contienen información sobre la instancia de Integration Runtime autohospedada y de los nodos en tiempo de ejecución.  

### <a name="properties"></a>Propiedades

En la tabla siguiente se proporcionan las descripciones de las propiedades de supervisión para **cada nodo**:

| Propiedad | Descripción | 
| -------- | ----------- | 
| Nombre | Nombre de la instancia de Integration Runtime autohospedada y sus nodos asociados. Un nodo es una máquina Windows local con la instancia de Integration Runtime autohospedada instalada. |  
| Estado | Estado de la instancia general de Integration Runtime autohospedada y de los nodos. Ejemplo: En línea, Sin conexión, Limitado, etc. Para información acerca de estos estados, consulte la siguiente sección. | 
| Versión | Versión de la instancia de Integration Runtime autohospedada y de los nodos. La versión de la instancia de Integration Runtime autohospedada se determina en función de la versión de la mayoría de los nodos del grupo. Si hay nodos con versiones diferentes en la configuración de la instancia de Integration Runtime autohospedada, solo los nodos con el mismo número de versión que la instancia lógica de Integration Runtime autohospedada funcionan correctamente. Los otros están en el modo limitado y deben actualizarse manualmente (solo en caso de que se produzca un error en la actualización automática). | 
| Memoria disponible | Memoria disponible en un nodo de una instancia de Integration Runtime autohospedada. Este valor es una instantánea casi en tiempo real. | 
| Uso de CPU | Uso de CPU de un nodo de una instancia de Integration Runtime autohospedada. Este valor es una instantánea casi en tiempo real. |
| Redes (Entrada/Salida) | Uso de las redes de un nodo de una instancia de Integration Runtime autohospedada. Este valor es una instantánea casi en tiempo real. | 
| Trabajos simultáneos (En ejecución / Límite) | Número de trabajos o tareas que se ejecutan en cada nodo. Este valor es una instantánea casi en tiempo real. Límite significa el máximo número de trabajos simultáneos para cada nodo. Este valor se define basándose en el tamaño de la máquina. Puede aumentar el límite para escalar verticalmente la ejecución de trabajos simultáneos en escenarios avanzados, donde la CPU, la memoria o la red están infrautilizadas pero se agota el tiempo de espera de las actividades. Esta funcionalidad también está disponible con una instancia de Integration Runtime autohospedada de nodo único. |
| Rol | En una instancia multinodo de Integration Runtime autohospedada hay dos tipos de roles: distribuidor y trabajo. Todos los nodos son trabajos, lo que significa que pueden usarse para ejecutar los trabajos. Hay solo un nodo distribuidor, que se usa para extraer los trabajos y las tareas de Cloud Services y enviarlos a los diferentes nodos de trabajo. El nodo distribuidor también es un nodo de trabajo. |

Algunos valores de configuración de las propiedades tienen más sentido cuando hay dos o más nodos (escenario de escalado horizontal) en la instancia de Integration Runtime autohospedada. 
  
### <a name="status-per-node"></a>Estado (por nodo)
En la tabla siguiente se proporcionan los estados posibles de los nodos de una instancia de Integration Runtime autohospedada:

| Estado | Descripción |
| ------ | ------------------ | 
| En línea | Nodo conectado al servicio Data Factory. |
| Sin conexión | El nodo está sin conexión. |
| Actualizando | El nodo se está actualizando automáticamente. |
| Limitado | Debido a un problema de conectividad. Puede ser debido a un problema en el puerto HTTP 8050, a un problema de conectividad de Service Bus o a un problema de sincronización de credenciales. |
| Inactivo | El nodo tiene una configuración diferente de la configuración de la mayoría de los otros nodos. |

Un nodo puede estar inactivo cuando no se puede conectar a otros nodos.

### <a name="status-overall-self-hosted-integration-runtime"></a>Estado (instancia general de Integration Runtime autohospedada)
En la tabla siguiente se proporcionan los estados posibles de una instancia de Integration Runtime autohospedada. Este estado depende de los de todos los nodos que pertenecen a la instancia de Runtime. 

| Estado | Descripción |
| ------ | ----------- | 
| Need registration (Se necesita registro) | No hay nodos registrados para esta instancia de Integration Runtime autohospedada aún. |
| En línea | Todos los nodos están en línea. |
| Sin conexión | Ningún nodo está en línea. |
| Limitado | No todos los nodos de esta instancia de Integration Runtime autohospedada tienen un estado correcto. Este estado es una advertencia de que alguno de los nodos podría estar inactivo. Puede deberse a un problema de sincronización de credenciales en el nodo distribuidor o de trabajo. |

Use el cmdlet **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** para capturar la carga de JSON que contiene las propiedades detalladas de la instancia de Integration Runtime autohospedada y los valores de su instantánea durante el tiempo de ejecución del cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Salida de ejemplo (se supone que hay dos nodos asociados a esta instancia de Integration Runtime autohospedada):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Integration Runtime de SSIS de Azure
Integration Runtime de SSIS de Azure es un clúster totalmente administrado de máquinas virtuales (o nodos) de Azure que se dedica a ejecutar los paquetes SSIS. No ejecuta otras actividades de Azure Data Factory. Una vez aprovisionado, puede consultar sus propiedades y supervisar su estado general o el específico del nodo.

### <a name="properties"></a>Propiedades

| Propiedad/estado | Descripción |
| --------------- | ----------- |
| CreateTime | Hora UTC a la que se creó la instancia de Integration Runtime de SSIS de Azure. |
| Nodos | Nodos asignados/disponibles de la instancia de Integration Runtime de SSIS de Azure con estados específicos de nodo (Starting/Available/Recycling/Unavailable) y errores que requieren acción. |
| OtherErrors | Errores inespecíficos del nodo que no requieren acción en la instancia de Integration Runtime de SSIS de Azure. |
| LastOperation | Resultado de la última operación de inicio/detención en la instancia de Integration Runtime de SSIS de Azure con errores que requieren acción, en caso de error de esta. |
| Estado | Estado general (Initial/Starting/Started/Stopping/Stopped) de la instancia de Integration Runtime de SSIS de Azure. |
| Ubicación | Ubicación de la instancia de Integration Runtime de SSIS de Azure. |
| NodeSize | Tamaño de cada nodo de la instancia de Integration Runtime de SSIS de Azure. |
| NodeCount | Número de nodos de la instancia de Integration Runtime de SSIS de Azure. |
| MaxParallelExecutionsPerNode | Número de ejecuciones en paralelo por nodo en la instancia de Integration Runtime de SSIS de Azure. |
| CatalogServerEndpoint | Punto de conexión del servidor de Azure SQL Database/de la instancia administrada existente al host SSISDB. |
| CatalogAdminUserName | Nombre de usuario administrador del servidor de Azure SQL Database/de la instancia administrada. El servicio Data Factory utiliza esta información para preparar y administrar SSISDB en su nombre. |
| CatalogAdminPassword | Contraseña del administrador del servidor de Azure SQL Database/de la instancia administrada. |
| CatalogPricingTier | Plan de tarifa de SSISDB hospedado en el servidor de Azure SQL Database existente.  No es aplicable a la instancia administrada de Azure SQL que hospeda SSISDB. |
| VNetId | Identificador del recurso de red virtual para que se una la instancia de Integration Runtime de SSIS de Azure. |
| Subred | Nombre de la subred para que se una la instancia de Integration Runtime de SSIS de Azure. |
| ID | Identificador del recurso de la instancia de Integration Runtime de SSIS de Azure. |
| Tipo | Tipo (administrada/autohospedada) de instancia de Integration Runtime de SSIS de Azure. |
| ResourceGroupName | Nombre del grupo de recursos de Azure donde se crearon las instancias de Data Factory y de Integration Runtime de SSIS de Azure. |
| DataFactoryName | Nombre de la instancia de Azure Data Factory. |
| Nombre | Nombre de la instancia de Integration Runtime de SSIS de Azure. |
| Descripción | Descripción de la instancia de Integration Runtime de SSIS de Azure. |

  
### <a name="status-per-node"></a>Estado (por nodo)

| Estado | Descripción |
| ------ | ----------- | 
| Iniciando | Este nodo se está preparando. |
| Disponible | Este nodo está listo para la implementación y ejecución de paquetes SSIS. |
| Recycling | Este nodo se va a reparar/reiniciar. |
| No disponible | Este nodo no está listo para implementar ni ejecutar paquetes SSIS y tiene errores que requieren acción o problemas que se pueden resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Estado (instancia general de Integration Runtime de SSIS de Azure)

| Estado general | Descripción | 
| -------------- | ----------- | 
| Inicial | Los nodos de la instancia de Integration Runtime de SSIS de Azure no se han asignado/preparado. | 
| Iniciando | Los nodos de la instancia de Integration Runtime de SSIS de Azure se están asignando/preparando y la facturación ha comenzado. |
| Started | Los nodos de la instancia de Integration Runtime de SSIS de Azure se han asignado/preparado y están listos para la implementación/ejecución de paquetes SSIS. |
| Deteniéndose  | Se están liberando los nodos de la instancia de Integration Runtime de SSIS de Azure. |
| Stopped | Los nodos de la instancia de Integration Runtime de SSIS de Azure se han liberado y la facturación se ha detenido. |

Consulte los artículos siguientes para más información sobre Integration Runtime de SSIS de Azure:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Integration Runtime de SSIS de Azure). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Administración de una instancia de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También muestra cómo escalar horizontalmente la instancia de Integration Runtime de SSIS de Azure al agregar más nodos a Integration Runtime. 
- [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes artículos para la supervisión de las canalizaciones de maneras diferentes: 

- [Guía de inicio rápido:creación de una instancia de Data Factory](quickstart-create-data-factory-dot-net.md)
- [Uso de Azure Monitor de Azure para supervisar las canalizaciones Data Factory](monitor-using-azure-monitor.md)