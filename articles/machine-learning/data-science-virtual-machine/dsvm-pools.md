---
title: 'Grupos de máquinas virtuales de Data Science Virtual Machine: Azure | Microsoft Docs'
description: Implementación de grupos de máquinas virtuales Data Science Virtual Machine como recurso compartido para el equipo
keywords: aprendizaje profundo, AI, herramientas de ciencia de datos, data science virtual machine, análisis geoespacial, proceso de ciencia de datos en equipo
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 625154378fecbd4d45904fa5669adb866fc9487c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Creación de un grupo compartido de máquinas virtuales de Data Science Virtual Machine

En este artículo se describe la creación de un grupo compartido de máquinas virtuales de Data Science Virtual Machine (DSVM) para que lo use el equipo. La ventaja de utilizar un grupo compartido es que mejora el uso de los recursos, facilita la colaboración y el uso compartido y permite al departamento de TI administrar los recursos de DSVM de manera más eficaz. 

Hay muchas maneras y diversas tecnologías que pueden usarse para crear un grupo de DSVM.  Escenarios principales:

* Grupo para el procesamiento por lotes
* Grupo para máquinas virtuales interactivas

## <a name="batch-processing-pool"></a>Grupo de procesamiento por lotes
Si desea configurar un grupo de DSVM principalmente para ejecutar trabajos en un lote sin conexión, puede usar el servicio [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) o [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch AI
Se admite la edición de Ubuntu de la DSVM como una de las imágenes de Azure Batch AI. En la CLI de Azure o el SDK de Python donde cree el clúster de Azure Batch AI, puede especificar el parámetro ```image``` y establecerlo en ```UbuntuDSVM```. Se puede elegir el tipo de nodos de procesamiento: instancias basadas en GPU o instancias solo de CPU, el número de CPU o la memoria de entre una [amplia gama de instancias de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponibles en Azure. Si utiliza la imagen de DSVM de Ubuntu en Batch AI con nodos basados en GPU, todos los controladores de GPU y los marcos de aprendizaje profundo se preinstalan, lo cual ahorra mucho tiempo de preparación de los nodos de Batch. De hecho, si desarrolla en una DSVM de Ubuntu de forma interactiva, observará que los nodos de Batch AI tienen exactamente la misma instalación y configuración del entorno. Normalmente cuando se crea un clúster de Batch AI también se crea un recurso compartido de archivos que montan todos los nodos y se utiliza para la entrada y salida de datos, así como para almacenar el código de trabajo y los scripts por lotes. 

Una vez creado el clúster de Batch AI, puede usar la misma CLI o SDK de Python para enviar trabajos para que se ejecuten. Solo se paga por el tiempo empleado en ejecutar los trabajos por lotes. 

#### <a name="more-information"></a>Más información
* Tutorial paso a paso sobre el uso de la [CLI de Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) para administrar Batch AI
* Tutorial paso a paso sobre el uso de [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) para administrar Batch AI
* Hay [trucos de Batch AI](https://github.com/Azure/BatchAI) disponibles que muestran el uso de varios marcos de aprendizaje profundo/AI con Batch AI.

## <a name="interactive-vm-pool"></a>Grupo de máquinas virtuales interactivas

Un grupo de máquinas virtuales DSVM interactivas que comparte todo el equipo de AI / ciencia de datos permite a los usuarios iniciar sesión en una instancia disponible de DSVM en lugar de tener una instancia específica para cada conjunto de usuarios. Esto mejora la disponibilidad y la eficacia de uso de los recursos. 

La tecnología utilizada para crear un grupo de máquinas virtuales interactivas es [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS), que permite crear y administrar un grupo de máquinas virtuales idénticas, con equilibrio de carga y escalado automático. El usuario inicia sesión en la dirección IP o DNS del grupo principal. El conjunto de escalado enruta automáticamente la sesión a una máquina virtual DSVM de este. Con el usuario desea un entorno similar con independencia de la máquina virtual donde inicie sesión, todas las instancias de Virtual Machine Scale Sets montan una unidad de red compartida de Azure Files o NFS, por ejemplo. El área de trabajo compartida del usuario se suele conservar en el almacén de archivos compartidos montado en todas las instancias. 

En [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) se puede encontrar una plantilla de Azure Resource Manager de ejemplo que crea una instancia de Virtual machine Scale Sets con máquinas virtuales DSVM de Ubuntu. En la misma ubicación también se encuentra un ejemplo de plantilla de Azure Resource Manager de [archivo de parámetros](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json). 

Puede crear la instancia de Virtual Machine Scale Sets a partir de la plantilla de Azure Resource Manager al especificar los valores adecuados para el archivo de parámetros mediante la CLI de Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Los comandos anteriores dan por hecho que tiene una copia del archivo de parámetros con los valores especificados para la instancia de Virtual Machine Scale Sets, el número de máquinas virtuales, los punteros a Azure Files junto con las credenciales de la cuenta de almacenamiento que se montará en cada máquina virtual. En el comando anterior se hace referencia al archivo de parámetros local. También puede pasar parámetros alineados o solicitarlos en el script.  

La plantilla anterior habilita el puerto SSH y JupyterHub desde el front-end de Virtual Machine Scale Sets en el grupo back-end de las máquinas virtuales DSVM de Ubuntu.  Como usuario, simplemente inicie sesión en el puerto SSH o JupyterHub de la máquina virtual como lo haría normalmente. Puesto que las instancias de máquina virtual se pueden escalar o reducir en vertical dinámicamente, es necesario guardar los estados en el recurso compartido montado de Azure Files. Puede utilizarse el mismo enfoque para crear un grupo de máquinas virtuales DSVM Windows. 

El [script que monta la instancia de Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) también está disponible en Azure DataScienceVM Github. Además de montar Azure Files en el momento de montaje especificado en el archivo de parámetros, también crea vínculos lógicos adicionales a la unidad montada en el directorio principal del usuario inicial y un directorio del cuaderno específico del usuario en el recurso compartido de Azure Files se vincula lógicamente al directorio ```$HOME/notebooks/remote``` para permitir al usuario el acceso, la ejecución y el guardado de los cuadernos de Jupyter notebooks.  La misma convención puede usarse al crear usuarios adicionales en la máquina virtual para que apunte el área de trabajo de Jupyter de cada usuario a la instancia compartida de Azure Files. 

Azure Virtual Machine Scale Sets es compatible con el escalado automático al establecer reglas durante la creación de instancias adicionales y en los casos de reducción vertical de instancias, incluso que lleguen a cero, para ahorrar costos en el uso de hardware en la nube cuando las máquinas virtuales no se usen. Las páginas de documentación de Virtual Machine Scale Sets proporcionan pasos detallados para realizar el [escalado automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de una identidad común](dsvm-common-identity.md)
* [Almacenamiento seguro de las credenciales para acceder a recursos en la nube](dsvm-secure-access-keys.md)















