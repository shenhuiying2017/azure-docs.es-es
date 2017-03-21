---
title: "Introducción a los conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Más información sobre los conjuntos de escalado de máquinas virtuales de Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 2071debe24ef2705b2ee05ce1210a813234cf277
ms.lasthandoff: 03/10/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>¿Qué son los conjuntos de escalado de máquinas virtuales en Azure?
Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar un conjunto de máquinas virtuales idénticas. Con la misma configuración en las máquinas virtuales, los conjuntos de escalado de máquinas virtuales están diseñados para admitir un auténtico escalado automático (sin necesidad de aprovisionarlas antes), por lo tanto, facilitan la creación de servicios a gran escala cuyo objetivo sean las cargas de trabajo en contenedor, los macroprocesos y los macrodatos.

Para las aplicaciones que necesiten escalar y reducir horizontalmente los recursos de proceso, las operaciones de escala se equilibran implícitamente en dominios de actualización y de error. Para una introducción a los conjuntos de escalado de máquinas virtuales, consulte el [anuncio en el blog de Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Visualice estos vídeos para más información sobre los conjuntos de escalado:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich habla sobre los conjuntos de escalado de Azure)  
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Creación y administración de conjuntos de escalado
Puede crear un conjunto de escalado en [Azure Portal](https://portal.azure.com) si selecciona *Nuevo* y escribe "escalado" en la barra de búsqueda. "Conjunto de escalado de máquinas virtuales" aparecerá en los resultados. Aquí podrá rellenar los campos obligatorios para personalizar e implementar el conjunto de escalado. Tenga en cuenta que en el portal también hay opciones para configurar reglas básicas de escalado automático en función del uso de la CPU.

Los conjuntos de escalado también se pueden definir e implementar mediante plantillas JSON y [API de REST](https://msdn.microsoft.com/library/mt589023.aspx), al igual que las máquinas virtuales individuales de Azure Resource Manager. Por lo tanto, pueden utilizarse cualquier método de implementación estándar de Administrador de recursos de Azure. Para más información sobre las plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Puede encontrar un conjunto de plantillas de ejemplo para los conjuntos de escalado de máquinas virtuales en el repositorio de GitHub de plantillas de inicio rápido de Azure [aquí](https://github.com/Azure/azure-quickstart-templates) (busque las plantillas que tengan *vmss* en el título).

En las páginas de detalle de estas plantillas verá un botón que lleva a la característica de implementación del portal. Para implementar el conjunto de escalado, haga clic en el botón y rellene los parámetros obligatorios en el portal. Si no está seguro de si un recurso admite mayúsculas o la mezcla de mayúsculas y minúsculas, es más seguro usar siempre minúsculas y números en los valores de los parámetros. Encontrará un práctico análisis minucioso en vídeo de una plantilla de conjunto de escalado aquí:

[VM Scale Set Template Dissection](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player) (Análisis minucioso de una plantilla de conjunto de escalado de máquinas virtuales)

## <a name="scaling-a-scale-set-out-and-in"></a>Escalado y reducción verticales de un conjunto de escalado
Para cambiar la capacidad de conjunto de escalado en Azure Portal, haga clic en la sección _Escalado_ de _Configuración_. 

Para cambiar la capacidad del conjunto de escalado de la línea de comandos, la [CLI de Azure](https://github.com/Azure/azure-cli) proporciona un comando de _escala_. Por ejemplo, para establecer un conjunto de escalado con una capacidad de 10 máquinas virtuales:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Para establecer el número de máquinas virtuales de un conjunto de escalado con PowerShell, utilice el comando _Update-AzureRmVmss_:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Para aumentar o disminuir el número de máquinas virtuales de un conjunto de escalado con una plantilla de Azure Resource Manager, cambie la propiedad *capacity* y vuelva a implementar la plantilla. Esta sencillez facilita la integración de conjuntos de escalado en el escalado automático de Azure o la incorporación de su propia capa de escalado personalizada si quiere definir eventos personalizados de escalado que no sean compatibles con el escalado automático de Azure. 

Si va a volver a implementar una plantilla de Azure Resource Manager para modificar la capacidad, puede definir una plantilla mucho más pequeña, que solo incluya el paquete de la propiedad "SKU" con la capacidad actualizada. [Aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) se muestra un ejemplo.

## <a name="autoscale"></a>Autoscale

Opcionalmente, se puede configurar un conjunto de escalado con escalado automático cuando se crea en Azure Portal, lo cual permite aumentar o reducir el número de máquinas virtuales en función del uso medio de la CPU. Muchas de las plantillas de conjunto de escalado de las [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) definen la configuración de escalado automático. También puede agregar la configuración de escalado automático a un conjunto de escalado existente. Por ejemplo, este es un script de Azure PowerShell para agregar escalado automático en función del uso de la CPU a un conjunto de escalado:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

 Encontrará una lista de métricas válidas de escalado aquí: [Métricas compatibles con Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) bajo el encabezado _Microsoft.Compute/virtualMachineScaleSets_. Las opciones más avanzadas de escalado automático también están disponibles, que incluyen el escalado automático basado en programación y el uso de webhooks para la integración con sistemas de alertas.

## <a name="monitoring-your-scale-set"></a>Supervisión del conjunto de escalado
En [Azure Portal](https://portal.azure.com) se enumeran los conjuntos de escalado con sus propiedades. El portal también admite operaciones de administración, que pueden realizarse tanto en los conjuntos de escalado como en las máquinas virtuales individuales dentro de uno específico. El portal también proporciona un gráfico de uso de los recursos personalizado. Si tiene que ver o editar la definición de JSON subyacente de un recurso de Azure, también puede usar el [Explorador de recursos de Azure](https://resources.azure.com). Los conjuntos de escalado son un recurso del proveedor de recursos de Azure Microsoft.Compute, por lo que en este sitio puede verlos al expandir los vínculos siguientes:

**Suscripciones -> su suscripción -> resourceGroups -> proveedores -> Microsoft.Compute -> virtualMachineScaleSets -> su conjunto de escalado -> etc.**

## <a name="scale-set-scenarios"></a>Escenarios de conjuntos de escalado
En esta sección se enumeran algunos escenarios típicos de conjuntos de escalado. Algunos servicios de Azure de niveles superiores (por ejemplo, Batch, Service Fabric, Azure Container Service) usan estos escenarios.

* **RDP/SSH a instancias de conjuntos de escalado**: se crea un conjunto de escalado en una red virtual y a las máquinas virtuales individuales de este no se les asignan direcciones IP públicas. Esta directiva evita los costos y los gastos generales de administración de la asignación de direcciones IP públicas independientes para todos los nodos en la cuadrícula de proceso. Puede conectarse a estas máquinas virtuales desde otros recursos de la red virtual, por ejemplo, los equilibradores de carga y las máquinas virtuales independientes, a los cuales se les pueden asignar direcciones IP públicas.
* **Conexión a máquinas virtuales con reglas NAT**: puede crear una dirección IP pública, asignarla a un equilibrador de carga y definir un grupo NAT entrante que asigne puertos de la dirección IP a un puerto de una máquina virtual en el conjunto de escalado. Por ejemplo:
  
  | Origen | Puerto de origen | Destino | Puerto de destino |
  | --- | --- | --- | --- |
  |  Dirección IP pública |Puerto 50000 |vmss\_0 |Puerto 22 |
  |  Dirección IP pública |Puerto 50001 |vmss\_1 |Puerto 22 |
  |  Dirección IP pública |Puerto 50002 |vmss\_2 |Puerto 22 |
  
   En este ejemplo, se definen reglas NAT para permitir la conexión SSH a todas las máquinas virtuales de un conjunto de escalado mediante una sola dirección IP pública: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Este es un ejemplo que hace lo mismo con RDP y Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Conexión a máquinas virtuales con un "jumpbox"**: si crea un conjunto de escalado y una máquina virtual independiente en la misma red virtual, la máquina virtual independiente y las del conjunto de escalado pueden conectarse entre sí mediante sus direcciones IP internas como se define en la red virtual y la subred. Si crea una dirección IP pública y la asigna a la máquina virtual independiente, puede conectarse mediante RDP o SSH a la máquina virtual independiente y después conectarse desde esa máquina a las instancias del conjunto de escalado. En este punto, es posible que observe que un simple conjunto de escalado es, por naturaleza, más seguro que una simple máquina virtual independiente con una dirección IP pública en su configuración predeterminada.
  
   Por ejemplo, esta plantilla implementa un conjunto de escalado simple con una máquina virtual independiente: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Equilibrio de carga en instancias de conjuntos de escalado**: si quiere entregar trabajos a un clúster de proceso de máquinas virtuales con un enfoque "round robin", puede configurar una instancia de Azure Load Balancer con reglas de equilibrio de carga de nivel&4;, según corresponda. También puede definir sondeos para comprobar que la aplicación se esté ejecutando haciendo ping en los puertos con un protocolo, un intervalo y una ruta de acceso de solicitud específicos. Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) también admite conjuntos de escalado, junto con nivel&7; y escenarios de equilibrio de carga más sofisticados.
  
   En este ejemplo se un conjunto de escalado que ejecuta servidores web Apache y utiliza un equilibrador de carga la carga que recibe cada máquina virtual: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (consulte el tipo de recurso de Microsoft.Network/loadBalancers, networkProfile y extensionProfile en virtualMachineScaleSet).

   Este ejemplo utiliza una instancia de Application Gateway. Linux:  [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) 

* **Implementación de un conjunto de escalado como clúster de proceso en un administrador de clústeres PaaS**: los conjuntos de escalado se describen como un rol de trabajo de próxima generación. Aunque es una descripción válida, existe el riesgo de confundir las características del conjunto de escalado con las de Azure Cloud Services. En cierto sentido, los conjuntos de escalado proporcionan un verdadero "rol de trabajo" o recurso de trabajo, pues suministran un recurso de proceso generalizado que no depende de la plataforma ni del tiempo de ejecución, es personalizada y se integra en IaaS de Azure Resource Manager.
  
   Un rol de trabajo de Cloud Services, aunque está limitado en cuanto a la compatibilidad con la plataforma y el tiempo de ejecución (solo imágenes de la plataforma Windows), incluye servicios como el intercambio de VIP, parámetros de actualización configurables o una configuración específica para la implementación de aplicaciones y para tiempo de ejecución, que no están *todavía* disponibles en los conjuntos de escalado o que se ofrecen con otros servicios de PaaS de nivel superior como Service Fabric. Puede ver los conjuntos de escalado como una infraestructura que admite PaaS. Las soluciones de PaaS, como [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/), compilan en esta infraestructura.
  
   Para un ejemplo de este enfoque, [Azure Container Service](https://azure.microsoft.com/services/container-service/) implementa un clúster basado en conjuntos de escalado con un orquestador de contenedores: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="scale-set-performance-and-scale-guidance"></a>Guía de escalado y rendimiento del conjunto de escalado
* Un conjunto de escalado admite hasta 1000 máquinas virtuales. Si crea y carga sus propias imágenes de máquina virtual personalizadas, el límite es 100. Para consideraciones al usar grandes conjuntos de escalado, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md).
* Para usar los conjuntos de escalado no necesita crear antes cuentas de almacenamiento de Azure. Los conjuntos de escalado admiten Azure Managed Disks, lo que evita problemas de rendimiento respecto al número de discos por cuenta de almacenamiento. Para más información, consulte [Conjuntos de escalado de máquinas virtuales y discos administrados](virtual-machine-scale-sets-managed-disks.md).
* Considere la posibilidad de usar Azure Premium Storage en lugar de Standard Storage para unos tiempos de aprovisionamiento de máquinas virtuales más rápidos y predecibles, y mejorar el rendimiento de E/S.
* El número de máquinas virtuales que puede crear está limitado por la cuota de núcleos en la región en la que realiza la implementación. Puede que necesite ponerse en contacto con el servicio al cliente para aumentar el límite de cuota de Compute, aunque ya disponga de un límite alto de núcleos para su uso con Azure Cloud Services. Para consultar la cuota, ejecute este comando en la CLI de Azure: `azure vm list-usage` o el siguiente comando de PowerShell: `Get-AzureRmVMUsage` (si utiliza una versión de PowerShell anterior a 1.0, use `Get-AzureVMUsage`).

## <a name="scale-set-frequently-asked-questions"></a>Preguntas más frecuentes sobre los conjuntos de escalado
**P.** ¿Cuántas máquinas virtuales puede tener en un conjunto de escalado?

**R.** Un conjunto de escalado puede tener entre 0 y 1000 máquinas virtuales basadas en imágenes de la plataforma o de 0 a 100, basadas en imágenes personalizadas. 

**P.** ¿Se admiten discos de datos en los conjuntos de escalado?

**R.** Sí. Un conjunto de escalado puede definir una configuración de unidades de datos conectadas que se aplique a todas las máquinas virtuales del conjunto. Para más información, consulte el artículo sobre conjuntos de escalado de Azure y discos de datos adjuntos[virtual-machine-scale-sets-attached-disks.md]. Otras opciones para almacenar datos son las siguientes:

* Archivos de Azure (unidades compartidas SMB)
* Unidad de sistema operativo
* Unidad temporal (local, no respaldada por almacenamiento de Azure)
* Servicio de datos de Azure (por ejemplo, tablas y blobs de Azure)
* Servicio de datos externos (por ejemplo, base de datos remota)

**P.** ¿Qué regiones de Azure admiten conjuntos de escalado?

**R.** Todas las regiones admiten conjuntos de escalado.

**P.** ¿Cómo se crea un conjunto de escalado con una imagen personalizada?

**R.** Cree un disco administrado basado en la imagen personalizada VHD y cree una referencia a él en la plantilla de conjunto de escalado. Aquí encontrará un ejemplo: [https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**P.** Si reduzco la capacidad de mi conjunto de escalado de 20 a 15, ¿qué máquinas virtuales se quitan?

**R.** Las máquinas virtuales se quitan uniformemente del conjunto de escala por los dominios de actualización y dominios de error para potenciar al máximo la disponibilidad. Primero se quitan las VM con el identificador más alto.

**P.** ¿Y si después aumento la capacidad de 15 a 18?

**R.** Si aumenta la capacidad a 18, se crearán 3 máquinas virtuales. Cada vez, el identificador de instancia de máquina virtual se aumenta a partir del mayor valor anterior (por ejemplo, 20, 21, 22). Las VM se equilibran entre dominios de error y de actualización.

**P.** Si uso varias extensiones en un conjunto de escalado, ¿se puede aplicar una secuencia de ejecución?

**R.** No directamente, pero para la extensión customScript, el script podría esperar a que se completara otra extensión ([por ejemplo, supervisando el registro de la extensión](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Encontrará más instrucciones sobre la secuenciación de extensión en esta entrada de blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Secuenciación de extensión en conjuntos de escalado de máquinas virtuales de Azure).

**P.** ¿Funcionan los conjuntos de escalado con los conjuntos de disponibilidad de Azure?

**R.** Sí. Un conjunto de escalado es de forma implícita un conjunto de disponibilidad con 5 dominios de error y 5 dominios de actualización. Los conjuntos de escalado de más de 100 máquinas virtuales abarcan varios "grupos de ubicación", que son equivalentes a varios conjuntos de disponibilidad. Para más información, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md). En la misma red virtual puede existir un conjunto de disponibilidad de máquinas virtuales como un conjunto de escalado de máquinas virtuales. Una configuración común consiste en colocar máquinas virtuales de nodos de control, que a menudo necesitan una configuración única en un conjunto de disponibilidad y nodos de datos en el conjunto de escalado.

Encontrará más preguntas frecuentes sobre los conjuntos de escalado en [Azure Virtual Machine Scale Sets FAQ](virtual-machine-scale-sets-faq.md) (Preguntas más frecuentes sobre los conjuntos de escalado de máquinas virtuales de Azure).

