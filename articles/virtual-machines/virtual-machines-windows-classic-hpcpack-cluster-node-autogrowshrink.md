---
title: Escalar nodos de clúster de HPC Pack automáticamente | Microsoft Docs
description: Aumento y reducción automáticos del número de nodos de proceso del clúster de HPC Pack en Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-service-management,hpc-pack

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep

---
# Aumento y reducción automáticos de los recursos de clúster de HPC Pack en Azure según la carga de trabajo de clúster
Si implementa nodos de "ráfaga" de Azure en su clúster de HPC Pack o crea un clúster de HPC Pack en máquinas virtuales de Azure, puede que quiera una manera de aumentar o reducir automáticamente el número de recursos de proceso de Azure, tales como los nodos o los núcleos, según la carga de trabajo actual en el clúster. Esto le permite usar su recursos de Azure de forma más eficiente y controlar sus costos. Para ello, establezca la propiedad **AutoGrowShrink** del clúster de HPC Pack. También puede ejecutar el script de HPC PowerShell **AzureAutoGrowShrink.ps1**, que se instala con HPC Pack.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Además, actualmente solo se pueden aumentar y reducir automáticamente los nodos de proceso de HPC Pack que ejecutan un sistema operativo Windows Server.

## Establecimiento de la propiedad de clúster AutoGrowShrink
### Requisitos previos
* **Clúster de HPC Pack 2012 R2 Update 2 o versiones posteriores**: el nodo principal del clúster se puede implementar de forma local o en una máquina virtual de Azure. Vea [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para comenzar con un nodo principal local y nodos de "ráfaga" de Azure. Consulte el [Script de implementación de IaaS de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar rápidamente un clúster de HPC Pack en máquinas virtuales de Azure.
* **Para un clúster con un nodo principal en Azure**: si usa el script de implementación de HPC Pack IaaS para crear el clúster, habilite la propiedad **AutoGrowShrink** del clúster mediante la opción AutoGrowShrink en el archivo de configuración del clúster. Para más información, consulte la documentación que acompaña a la [descarga del script](https://www.microsoft.com/download/details.aspx?id=44949).
  
    También puede habilitar la propiedad **AutoGrowShrink** del clúster después de implementar este mediante los comandos de HPC PowerShell que se describen en la sección siguiente. Para prepararse para esto, complete primero los pasos siguientes:
  
  1. Configure un certificado de administración de Azure en el nodo principal y en la suscripción de Azure. En una implementación de prueba, puede usar el certificado autofirmado Default Microsoft HPC Azure que HPC Pack instala en el nodo principal; simplemente cargue dicho certificado en su suscripción de Azure. Para las opciones y los pasos, consulte la [Guía de la biblioteca de TechNet](https://technet.microsoft.com/library/gg481759.aspx).
  2. Ejecute **regedit** en el nodo principal, vaya a HKLM\\SOFTWARE\\Micorsoft\\HPC\\IaasInfo y agregue un nuevo valor de cadena. Establezca Value name en "ThumbPrint" y Value data en la huella digital del certificado del paso 1.

### Comandos de HPC PowerShell para establecer la propiedad AutoGrowShrink
Los siguientes son ejemplos de comandos de HPC PowerShell para establecer **AutoGrowShrink** y ajustar su comportamiento con parámetros adicionales. Consulte [Parámetros de AutoGrowShrink](#AutoGrowShrink-parameters) más adelante en este artículo para obtener una lista completa de las opciones de configuración.

Para ejecutar estos comandos, inicie HPC PowerShell en el nodo principal del clúster como administrador.

**Para habilitar la propiedad AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Para deshabilitar la propiedad AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Para cambiar el intervalo de aumento en minutos**

    Set-HpcClusterProperty –GrowInterval <interval>

**Para cambiar el intervalo de reducción en minutos**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Para ver la configuración actual de AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### Parámetros de AutoGrowShrink
Los siguientes son parámetros de AutoGrowShrink que se pueden modificar con el comando **Set-HpcClusterProperty**.

* **EnableGrowShrink**: modificador para habilitar o deshabilitar la propiedad **AutoGrowShrink**.
* **ParamSweepTasksPerCore**: número de tareas de barrido paramétrico para aumentar un núcleo. El valor predeterminado es aumentar un núcleo por tarea.
  
  > [!NOTE]
  > HPC Pack versión de QFE KB3134307 cambia **ParamSweepTasksPerCore** por **TasksPerResourceUnit**. Se basa en el tipo de recurso de trabajo y puede ser un nodo, un socket o un núcleo.
  > 
  > 
* **GrowThreshold**: umbral de tareas en cola para desencadenar el crecimiento automático. El valor predeterminado es 1, lo que significa que si hay 1 o más tareas en estado en cola, los nodos aumentarán automáticamente.
* **GrowInterval**: intervalo en minutos para desencadenar el aumento automático. El intervalo predeterminado es de 5 minutos.
* **ShrinkInterval**: intervalo en minutos para desencadenar la reducción automática. El intervalo predeterminado es de 5 minutos.|
* **ShrinkIdleTimes**: número de comprobaciones continuas que indican que los nodos están inactivos para reducirlos. El valor predeterminado es 3 veces. Por ejemplo, si el valor de **ShrinkInterval** es 5 minutos, HPC Pack comprueba si el nodo está inactivo cada 5 minutos. Si los nodos están en estado de inactividad después de 3 comprobaciones continuas (15 minutos), HPC Pack reduce ese nodo.
* **ExtraNodesGrowRatio**: porcentaje adicional de nodos que se aumentará para los trabajos de la interfaz MPI (Message Passing Interface). El valor predeterminado es 1, lo que significa que HPC Pack aumenta los nodos un 1 % para los trabajos de MPI.
* **GrowByMin**: modificador que indica si la directiva de crecimiento automático se basa en los recursos mínimos necesarios para el trabajo. El valor predeterminado es false, lo que significa que HPC Pack aumenta los nodos para los trabajos según los recursos máximos necesarios para los trabajos.
* **SoaJobGrowThreshold**: umbral de solicitudes de SOA entrantes para desencadenar el proceso de crecimiento automático. El valor predeterminado es 50 000.
  
  > [!NOTE]
  > Este parámetro se admite a partir de HPC Pack 2012 R2 Update 3.
  > 
  > 
* **SoaRequestsPerCore**: número de solicitudes de SOA entrantes para aumentar un núcleo. El valor predeterminado es 20 000.
  
  > [!NOTE]
  > Este parámetro se admite a partir de HPC Pack 2012 R2 Update 3.
  > 
  > 

### Ejemplo de MPI
De forma predeterminada, HPC Pack aumenta un 1 % los nodos adicionales para los trabajos de MPI (**ExtraNodesGrowRatio** se establece en 1). El motivo es que MPI puede necesitar varios nodos y el trabajo solo se puede ejecutar cuando todos los nodos están listos. Cuando Azure inicia los nodos, en ocasiones un nodo puede necesitar más tiempo para iniciarse que otros, lo que provoca que los demás nodos estén inactivos mientras esperan a que ese nodo esté preparado. Al aumentar los nodos adicionales, HPC Pack reduce este tiempo de espera de recursos y podría ahorrar costos. Para aumentar el porcentaje de nodos adicionales para los trabajos MPI (por ejemplo, al 10 %), ejecute un comando similar a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### Ejemplo de SOA
De forma predeterminada, **SoaJobGrowThreshold** se establece n 50 000 y **SoaRequestsPerCore** se establece en 200 000. Si envía un trabajo de SOA con 70 000 solicitudes, habrá una tarea en cola y las solicitudes entrantes son 70 000. En este caso, HPC Pack 1 aumenta un núcleo para la tarea en cola y, para las solicitudes entrantes, aumentará (70 000 - 50 000)/20 000 = 1 núcleo, por lo que en total aumentará 2 núcleos para este trabajo de SOA.

## Ejecución del script AzureAutoGrowShrink.ps1
### Requisitos previos
* **Clúster de HPC Pack 2012 R2 Update 1 o versiones posteriores**: el script **AzureAutoGrowShrink.ps1** se instala en la carpeta %CCP\_HOME%bin. El nodo principal del clúster se puede implementar de forma local o en una máquina virtual de Azure. Vea [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para comenzar con un nodo principal local y nodos de "ráfaga" de Azure. Consulte el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar rápidamente un clúster de HPC Pack en máquinas virtuales de Azure, o use una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 0.8.12**: actualmente, el script depende de esta versión específica de Azure PowerShell. Si ejecuta una versión posterior en el nodo principal, es posible que tenga que cambiar Azure PowerShell a la [versión 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para ejecutar el script.
* **Para un clúster con nodos de ráfaga de Azure**: ejecute el script en un equipo cliente donde esté instalado HPC Pack o en el nodo principal. Si se ejecuta en un equipo cliente, asegúrese de establecer la variable $env:CCP\_SCHEDULER correctamente para que apunte al nodo principal. Los nodos de "ráfaga" de Azure ya deben haberse agregado al clúster, pero es posible que se encuentren en el estado Not-Deployed.
* **Para un clúster implementado en máquinas virtuales de Azure**: ejecute el script en la máquina virtual del nodo principal, porque depende de los scripts **Start-HpcIaaSNode.ps1** y **Stop-HpcIaaSNode.ps1** que están instalados allí. Esas scripts requieren además un certificado de administración de Azure o un archivo de configuración de publicación (consulte [Administrar nodos de ejecución en un clúster de HPC Pack en Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Asegúrese de que todas las máquinas virtuales de nodo de ejecución que necesita ya se agregaron al clúster. Pueden tener el estado Detenido.

### Sintaxis
```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### Parámetros
* **NodeTemplates**: nombres de las plantillas de nodos para definir el ámbito de crecimiento y reducción de los nodos aumente. Si no se especifican (el valor predeterminado es @()), todos los nodos del grupo de nodos **AzureNodes** se encuentran dentro del ámbito cuando **NodeType** tiene un valor de AzureNodes y todos los nodos del grupo de nodos **ComputeNodes** se encuentran dentro del ámbito cuando **NodeType** tiene un valor de ComputeNodes.
* **JobTemplates**: nombres de las plantillas de trabajos para definir el ámbito de crecimiento de los nodos.
* **NodeType**: el tipo que se va a aumentar y a reducir. Los valores admitidos son:
  
  * **AzureNodes**: para los nodos (ráfaga) de Azure PaaS en un clúster de Azure IaaS o local.
  * **ComputeNodes**: para máquinas virtuales de nodos de ejecución solo en un clúster de Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow**: número de trabajos en cola que se requieren para aumentar un nodo.
* **NumOfQueuedJobsToGrowThreshold**: el número máximo de trabajos en cola para iniciar el proceso de crecimiento.
* **NumOfActiveQueuedTasksPerNodeToGrow** : el número de tareas en cola activas necesarias para aumentar un nodo. Si se especifica **NumOfQueuedJobsPerNodeToGrow** con un valor superior a 0, se omite este parámetro.
* **NumOfActiveQueuedTasksToGrowThreshold**: el número máximo de tareas en cola activas para iniciar el proceso de crecimiento.
* **NumOfInitialNodesToGrow**: el número mínimo inicial de nodos que se van a aumentar si todos los nodos del ámbito son **Not-Deployed** o **Stopped (Deallocated)**.
* **GrowCheckIntervalMins**: el intervalo en minutos entre comprobaciones que se van a aumentar.
* **ShrinkCheckIntervalMins**: el intervalo en minutos entre comprobaciones que se van a reducir.
* **ShrinkCheckIdleTimes**: el número de comprobaciones de reducción continua (separadas por **ShrinkCheckIntervalMins**) para indicar que los nodos están inactivos.
* **UseLastConfigurations**: las configuraciones anteriores guardadas en el archivo de argumentos.
* **ArgFile**: el nombre del archivo de argumento usado para guardar y actualizar las configuraciones para ejecutar el script.
* **LogFilePrefix**: el nombre del prefijo del archivo de registro. Puede especificar una ruta de acceso. De forma predeterminada, el registro escrito en el directorio de trabajo actual.

### Ejemplo 1
En el ejemplo siguiente se configuran los nodos de ráfaga de Azure implementados con la plantilla AzureNode predeterminada para aumentar y reducir automáticamente. Si todos los nodos se encuentran inicialmente en el nodo **Not-Deployed**, se inician al menos 3 nodos. Si el número de trabajos en cola es superior a 8, el script inicia nodos hasta que su número supera la proporción de trabajos en cola en **NumOfQueuedJobsPerNodeToGrow**. Si se descubre que un nodo está inactivo en 3 tiempos de inactividad consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Ejemplo 2
En el ejemplo siguiente se configuran las máquinas virtuales del nodo de ejecución de Azure con la plantilla de ComputeNode predeterminada para aumentar y disminuir automáticamente. Los trabajos configurados por la plantilla de trabajo predeterminada definen el ámbito de la carga de trabajo en el clúster. Si todos los nodos se detienen inicialmente, se inician al menos 5 nodos. Si el número de tareas en cola activas es superior a 15, el script inicia nodos hasta que su número supera la relación de tareas en cola activas en **NumOfActiveQueuedTasksPerNodeToGrow**. Si se descubre que un nodo está inactivo en 10 tiempos de inactividad consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=AcomDC_0727_2016-->