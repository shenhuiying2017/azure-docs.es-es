---
title: "Introducción a los conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Más información sobre los conjuntos de escalado de máquinas virtuales de Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2048a39f28a74ca8a31c2e6d7466c69ba4d58f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>¿Qué son los conjuntos de escalado de máquinas virtuales en Azure?
Los conjuntos de escalado de máquinas virtuales son un recurso de Azure Compute que se puede usar para implementar y administrar un conjunto de máquinas virtuales idénticas. Con todas las máquinas virtuales configuradas de la misma manera, los conjuntos de escalado están diseñados para admitir el escalado automático verdadero y no es necesario aprovisionar las máquinas virtuales antes. Esto facilita la compilación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedor, de macroproceso y macrodatos.

Para las aplicaciones que necesiten escalar y reducir horizontalmente los recursos de proceso, las operaciones de escala se equilibran implícitamente en dominios de actualización y de error. Para una introducción a los conjuntos de escalado, consulte el [anuncio en el blog de Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Para más información acerca de los conjuntos de escalado, vea estos vídeos:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich habla sobre los conjuntos de escalado de Azure)  
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Creación y administración de conjuntos de escalado
Para crear un conjunto de escalado en [Azure Portal](https://portal.azure.com), seleccione **nuevo** y escriba **escalado** en la barra de búsqueda. El **conjunto de escalado de máquinas virtuales** se enumera en los resultados. Aquí podrá rellenar los campos obligatorios para personalizar e implementar el conjunto de escalado. También hay opciones para configurar reglas básicas de escalado automático en función del uso de la CPU. Para administrar el conjunto de escalado, puede usar Azure Portal, [cmdlets de Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) o la CLI de Azure 2.0.

Los conjuntos de escalado se pueden implementar en una [zona de disponibilidad](../availability-zones/az-overview.md).

> [!NOTE]
> En la actualidad, los conjuntos de escalado de máquinas virtuales solo admiten la implementación en una zona de disponibilidad. Sin embargo, en el futuro se admitirá la implementación en varias zonas.

Los conjuntos de escalado de máquinas virtuales se pueden definir e implementar mediante plantillas JSON y [API de REST](https://msdn.microsoft.com/library/mt589023.aspx), igual que las máquinas virtuales individuales de Azure Resource Manager. Por tanto, puede utilizar cualquier método de implementación estándar de Azure Resource Manager. Para más información sobre las plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Puede encontrar un conjunto de plantillas de ejemplo para los conjuntos de escalado de máquinas virtuales en el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) (busque plantillas que tengan **vmss** en el título).

Para los ejemplos de plantilla de inicio rápido, hay un botón "Implementar en Azure" en el archivo Léame de cada plantilla vinculado a la característica de implementación del portal. Para implementar el conjunto de escalado, haga clic en el botón y rellene los parámetros obligatorios en el portal. 


## <a name="autoscale"></a>Autoscale
Para mantener el rendimiento de la aplicación coherente, puede aumentar o reducir automáticamente el número de instancias de máquina virtual del conjunto de escalado. Esta capacidad de escalado automático reduce la sobrecarga de administración para supervisar y ajustar el escalado automático como cambios a demanda del cliente a lo largo del tiempo. El usuario define las reglas en función de las métricas de rendimiento, la respuesta de la aplicación o una programación fija y el conjunto de escalado se escala automáticamente según sea necesario.

Como reglas básicas de escalado automático, puede usar las métricas de rendimiento basadas en el host, como el uso de CPU o las E/S del disco. Estas métricas basadas en el host están disponibles de forma automática, sin agentes o extensiones adicionales que instalar y configurar. Las reglas de escalado automático que usan métricas basadas en el host se crean con una de las herramientas siguientes:

- [Portal de Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [CLI de Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Para usar las métricas de rendimiento más granulares, puede instalar y configurar la extensión de Azure Diagnostics en instancias de máquina virtual del conjunto de escalado. La extensión de Azure Diagnostics permite recopilar métricas de rendimiento adicionales, como el consumo de memoria, del interior de cada instancia de máquina virtual. Estas métricas de rendimiento se transmiten a una cuenta de Azure Storage y el usuario crea reglas de escalado automático para consumir los datos. Para más información, consulte los artículos sobre cómo habilitar la extensión de Azure Diagnostics en una [máquina virtual Linux](../virtual-machines/linux/diagnostic-extension.md) o una [máquina virtual Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).

Para supervisar el rendimiento de la aplicación, puede instalar y configurar un pequeño paquete de instrumentación en a la aplicación para Application Insights. Entonces se podrán transmitir desde esta las métricas de rendimiento detalladas sobre el tiempo de respuesta o el número de sesiones de la aplicación. A continuación, puede crear reglas de escalado automático con los umbrales definidos para el propio rendimiento de nivel de aplicación. Para más información sobre Application Insights, consulte [¿Qué es Application Insights?](../application-insights/app-insights-overview.md)


## <a name="manually-scaling-a-scale-set-out-and-in"></a>Escalado y reducción verticales automáticos de un conjunto de escalado
Puede cambiar manualmente la capacidad de un conjunto de escalado en Azure Portal; para ello, haga clic en la sección **Escalado** de **Configuración**. 

Para cambiar la capacidad del conjunto de escalado en la línea de comandos, use el comando **scale** de la [CLI de Azure](https://github.com/Azure/azure-cli). Por ejemplo, este comando se puede usar para establecer que un conjunto de escalado tenga una capacidad de 10 máquinas virtuales:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Para establecer el número de máquinas virtuales de un conjunto de escalado mediante PowerShell, utilice el comando **Update-AzureRmVmss**:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Para aumentar o disminuir el número de máquinas virtuales de un conjunto de escalado mediante una plantilla de Azure Resource Manager, cambie la propiedad **capacity** y vuelva a implementar la plantilla. Esta sencillez facilita la integración de conjuntos de escalado en Azure Autoscale o la incorporación de su propia capa de escalado personalizada si necesita definir eventos personalizados de escalado que no sean compatibles con Azure Autoscale. 

Si va a volver a implementar una plantilla de Azure Resource Manager para modificar la capacidad, puede definir una plantilla mucho menor que solo incluya el paquete de la propiedad **SKU** con la capacidad actualizada. [Este es un ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).


## <a name="monitoring-your-scale-set"></a>Supervisión del conjunto de escalado
En [Azure Portal](https://portal.azure.com) se enumeran los conjuntos de escalado y se muestran sus propiedades. El portal también admite operaciones de administración. Se pueden realizar operaciones de administración tanto en conjuntos de escalado como en máquinas virtuales individuales de un conjunto de escalado. El portal también proporciona un gráfico de uso de los recursos personalizado. 

Si tiene que ver o editar la definición de JSON subyacente de un recurso de Azure, también puede usar el [Explorador de recursos de Azure](https://resources.azure.com). Los conjuntos de escalado son un recurso del proveedor de recursos Microsoft.Compute Azure. Los puede ver desde este sitio. Para ello, solo tiene que expandir los vínculos siguientes:

**Suscripciones** > **su suscripción** > **resourceGroups** > **proveedores** > **Microsoft.Compute** > **virtualMachineScaleSets** > **su conjunto de escalado** &gt; etc.

## <a name="scale-set-scenarios"></a>Escenarios de conjuntos de escalado
En esta sección se enumeran algunos escenarios típicos de conjuntos de escalado. Algunos servicios de alto nivel de Azure (como Batch, Service Fabric y Container Service) usan estos escenarios.

* **Uso de RDP o SSH para conectarse a instancias de conjuntos de escalado**: se crea un conjunto de escalado en una red virtual y no se asignan de forma predeterminada direcciones IP públicas a las máquinas virtuales individuales del conjunto de escalado. Esta directiva evita los costos y los gastos generales de administración de la asignación de direcciones IP públicas independientes para todos los nodos en la cuadrícula de proceso. Si necesita conexiones externas directas a máquinas virtuales del conjunto de escalado, puede configurar este para que asigne automáticamente direcciones IP públicas a nuevas máquinas virtuales. Como alternativa, puede conectarse a máquinas virtuales desde otros recursos de la red virtual a los que se puedan asignar direcciones IP públicas (por ejemplo, equilibradores de carga y máquinas virtuales independientes). 
* **Conexión a máquinas virtuales mediante reglas NAT**: puede crear una dirección IP pública, asignarla a un equilibrador de carga y definir un grupo NAT entrante. Estas acciones asignan puertos de la dirección IP a un puerto de una máquina virtual del conjunto de escalado. Por ejemplo:
  
  | Origen | Puerto de origen | Destino | Puerto de destino |
  | --- | --- | --- | --- |
  |  Dirección IP pública |Puerto 50000 |vmss\_0 |Puerto 22 |
  |  Dirección IP pública |Puerto 50001 |vmss\_1 |Puerto 22 |
  |  Dirección IP pública |Puerto 50002 |vmss\_2 |Puerto 22 |
  
   En [este ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat), se definen reglas NAT para habilitar una conexión SSH a cada una de las máquinas virtual de un conjunto de escalado mediante una única dirección IP pública.
  
   [En este ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) se hace lo mismo con RDP y Windows.
* **Conexión a máquinas virtuales mediante un "servidor de salto"**: si crea una máquina de un conjunto de escalado y una máquina virtual independiente en la misma red virtual, ambas pueden conectarse entre sí mediante sus direcciones IP internas, como se define en la red virtual o la subred. Si crea una dirección IP pública y la asigna a la máquina virtual independiente, puede usar RDP o SSH para conectarse a esta última. Después, puede conectarse desde dicha máquina a sus instancias del conjunto de escalado. En este punto, ya se habrá percatado de que un simple conjunto de escalado es, por naturaleza, más seguro que una simple máquina virtual independiente con una dirección IP pública en su configuración predeterminada.
  
   Por ejemplo, en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) se implementa un conjunto de escalado simple con una máquina virtual independiente. 
* **Equilibrio de carga en instancias de conjuntos de escalado**: si desea entregar trabajos a un clúster de proceso de máquinas virtuales con un enfoque "round robin", puede configurar una instancia de Azure Load Balancer con reglas de equilibrio de carga de nivel 4, según corresponda. Puede definir sondeos que comprueben que la aplicación se ejecuta haciendo ping en los puertos con un protocolo, un intervalo y una ruta de acceso de solicitud específicos. [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) también admite conjuntos de escalado, junto con escenarios de equilibrio de carga de nivel 7, y más sofisticados.
  
   En [este ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) se crea un conjunto de escalado que ejecuta servidores web Apache y se utiliza un equilibrador de carga para equilibrar la carga que recibe de cada máquina virtual (examine el tipo de recurso Microsoft.Network/loadBalancers, networkProfile y extensionProfile en virtualMachineScaleSet).

   En [este ejemplo de Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) y en [este ejemplo de Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) se usa Application Gateway.  

* **Implementación de un conjunto de escalado como clúster de proceso en un administrador de clústeres PaaS**: los conjuntos de escalado a veces se describen como un rol de trabajo de última generación. Aunque es una descripción válida, existe el riesgo de confundir las características del conjunto de escalado con las de Azure Cloud Services. Hasta cierto punto, los conjuntos de escalado proporcionan unos verdaderos rol de trabajo o recurso de trabajo. Son un recurso de proceso generalizado que no depende de la plataforma ni del runtime, se puede personalizar y se integra en el IaaS de Azure Resource Manager.
  
   Los roles de trabajo de Cloud Services están limitados en lo que respecta a la compatibilidad con plataformas o runtimes (solo imágenes de plataformas de Windows). Pero también incluye servicios como el intercambio de VIP, la configuración de las opciones de actualización y la configuración específica de la implementación de runtimes o aplicaciones. Estos servicios *aún* no están disponibles en los conjuntos de escalado, o los prestan otros servicios de PaaS de alto nivel como Azure Service Fabric. Puede ver los conjuntos de escalado como una infraestructura que admite PaaS. Las soluciones de PaaS, como [Service Fabric](https://azure.microsoft.com/services/service-fabric/), se basan en esta infraestructura.
  
   En [este ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) de este enfoque, [Azure Container Service](https://azure.microsoft.com/services/container-service/) implementa un clúster basado en conjuntos de escalado con un orquestador del contenedor.

## <a name="scale-set-performance-and-scale-guidance"></a>Guía de escalado y rendimiento del conjunto de escalado
* Un conjunto de escalado admite un máximo de 1.000 máquinas virtuales. Si crea y carga sus propias imágenes de máquina virtual personalizadas, el límite es 300. Para ver todo lo que se debe saber al usar conjuntos de escalado grandes, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md).
* Para usar los conjuntos de escalado no necesita crear antes cuentas de almacenamiento de Azure. Los conjuntos de escalado admiten Azure Managed Disks, lo que evita problemas de rendimiento relacionados con el número de discos por cuenta de almacenamiento. Para más información, consulte [Conjuntos de escalado de máquinas virtuales y discos administrados](virtual-machine-scale-sets-managed-disks.md).
* Considere la posibilidad de usar Azure Premium Storage, en lugar de Azure Storage, para lograr unos tiempos de aprovisionamiento de máquinas virtuales menores y más predecibles, y mejorar el rendimiento de E/S.
* La cuota de vCPU en la región en la que realiza la implementación limita el número de máquinas virtuales que puede crear. Aunque disponga de un límite alto de vCPU que se pueden usar con Azure Cloud Services, es posible que necesite ponerse en contacto con el servicio de soporte técnico al cliente para aumentarlo. Para consultar la cuota, ejecute este comando de la CLI de Azure: `az vm list-usage`. O bien, ejecute este comando de PowerShell: `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>Preguntas más frecuentes acerca de los conjuntos de escalado
**P.** ¿Cuántas máquinas virtuales puede contener un conjunto de escalado?

**R.** Un conjunto de escalado puede tener entre 0 y 1000 máquinas virtuales basadas en imágenes de plataforma, o entre 0 y 300 basadas en imágenes personalizadas. 

**P.** ¿Se admiten discos de datos en los conjuntos de escalado?

**R.** Sí. Un conjunto de escalado puede definir una configuración de discos de datos conectados que se aplica a todas las máquinas virtuales del conjunto. Para más información, consulte [Conjuntos de escalado de máquinas virtuales de Azure y discos de datos conectados](virtual-machine-scale-sets-attached-disks.md). Otras opciones para almacenar datos son las siguientes:

* Archivos de Azure (unidades compartidas SMB)
* Unidad de sistema operativo
* Unidad temporal (local, sin el respaldo de Azure Storage)
* Servicio de datos de Azure (por ejemplo, tablas y blobs de Azure)
* Servicio de datos externos (por ejemplo, una base de datos remota)

**P.** ¿Qué regiones de Azure admiten conjuntos de escalado?

**R.** Todas las regiones admiten conjuntos de escalado.

**P.** ¿Cómo se crea un conjunto de escalado con una imagen personalizada?

**R.** Cree un disco administrado basado en el VHD de su imagen personalizada y cree una referencia a él en la plantilla del conjunto de escalado. [Este es un ejemplo](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**P.** Si reduzco la capacidad de mi conjunto de escalado de 20 a 15, ¿qué máquinas virtuales se quitan?

**R.** Las máquinas virtuales se quitan uniformemente del conjunto de escalado tanto en los dominios de actualización como en los dominios de error para maximizar su disponibilidad. Primero se quitan las máquinas virtuales que tengan los identificadores más altos.

**P.** ¿Y si después se aumenta la capacidad de 15 a 18?

**R.** Si aumenta la capacidad a 18, se crearán 3 máquinas virtuales. Cada vez, el identificador de la instancia de máquina virtual se aumenta a partir del mayor valor anterior (por ejemplo, 20, 21 y 22). Las máquinas virtuales están equilibradas entre los dominios de error y los dominios de actualización.

**P.** Si se usan varias extensiones en un conjunto de escalado, ¿se puede exigir una secuencia de ejecución?

**R.** No directamente, pero en el caso de la extensión customScript, el script puede esperar hasta que finalice otra extensión. Encontrará más instrucciones acerca de la secuenciación de extensiones en la siguiente entrada de blog: [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Secuenciación de extensiones en conjuntos de escalado de máquinas virtuales de Azure).

**P.** ¿Funcionan los conjuntos de escalado con los conjuntos de disponibilidad de Azure?

**R.** Sí. Un conjunto de escalado es un conjunto de disponibilidad implícita con cinco dominios de error y cinco dominios de actualización. Los conjuntos de escalado de más de 100 máquinas virtuales abarcan varios *grupos de ubicación*, que son equivalentes a varios conjuntos de disponibilidad. Para más información, consulte [Uso de grandes conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-placement-groups.md). Puede existir un conjunto de disponibilidad de máquinas virtuales en la misma red virtual que un conjunto de escalado de máquinas virtuales. Una configuración común consiste en colocar máquinas virtuales de nodos de control (que a menudo necesitan una configuración única) en un conjunto de disponibilidad y nodos de datos en el conjunto de escalado.

Encontrará más respuestas a las preguntas acerca de los conjuntos de escalado en [Preguntas frecuentes sobre los conjuntos de escalado de máquinas virtuales de Azure](virtual-machine-scale-sets-faq.md).
