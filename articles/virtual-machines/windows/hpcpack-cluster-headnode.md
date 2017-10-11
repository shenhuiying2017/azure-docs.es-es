---
title: "Creación de un nodo principal de HPC Pack en una VM de Azure | Microsoft Azure"
description: "Aprenda a usar Azure Portal y el modelo de implementación de Resource Manager para crear un nodo principal Microsoft HPC Pack 2012 R2 en una máquina virtual de Azure."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: b2bb9caf82a580dc5f67ea0b0b1c2e9a46363e9c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace
Use una [imagen de máquina virtual de Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de Azure Marketplace y Azure Portal para crear el nodo principal de un clúster HPC. Esta imagen de máquina virtual de HPC Pack se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstalado. Utilice este nodo principal para una implementación de prueba de concepto de HPC Pack en Azure. A continuación, puede agregar nodos de proceso al clúster para ejecutar cargas de trabajo HPC.

> [!TIP]
> Para implementar un clúster de HPC Pack 2012 R2 completo en Azure que incluya el nodo principal y los nodos de proceso, se recomienda utilizar un método automatizado. Entre las opciones se incluyen el [script de implementación de HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) y las plantillas de Resource Manager, como el [clúster de HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Las plantillas de Resource Manager también están disponibles para [clústeres de Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Consideraciones de planeación
Como se muestra en la ilustración siguiente, implementa el nodo principal de HPC Pack en un dominio de Active Directory de una red virtual de Azure.

![Nodo principal de HPC Pack][headnode]

* **Dominio de Active Directory**: el nodo principal de HPC Pack 2012 R2 debe estar unido a un dominio de Active Directory en Azure antes de iniciar los servicios HPC en la máquina virtual. Como se muestra en este artículo, para realizar una prueba de implementación del concepto, puede promover la máquina virtual creada para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC. Otra opción es implementar un controlador de dominio independiente y el bosque en Azure al que se une la máquina virtual del nodo principal.

* **Modelo de implementación**: para la mayoría de nuevas implementaciones, Microsoft recomienda que el uso del modelo de Resource Manager. En este artículo se da por supuesto que utiliza este modelo de implementación.

* **Red virtual de Azure**: al usar el modelo de implementación de Resource Manager para implementar el nodo principal, especifique o cree una red virtual de Azure. Use la red virtual si se debe unir el nodo principal a un dominio de Active Directory existente. También la necesita más adelante para agregar máquinas virtuales de nodo de ejecución al clúster.

## <a name="steps-to-create-the-head-node"></a>Pasos para crear el nodo principal
A continuación, se muestran los pasos generales para, con el Portal de Azure, crear una máquina virtual de Azure para el nodo principal de HPC Pack mediante el modelo de implementación de Resource Manager. 

1. Si desea crear un nuevo bosque de Active Directory en Azure con máquinas virtuales de controlador de dominio independientes, una opción es usar una [plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Para realizar simplemente una implementación de prueba de concepto, se puede omitir este paso y configurar la propia máquina virtual del nodo principal como un controlador de dominio. Esta opción se describe más adelante en este artículo.
2. En la página [HPC Pack 2012 R2 on Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) (página HPC Pack 2012 R2 en Windows Server 2012 R2) en Azure Marketplace, haga clic en **Crear Máquina Virtual**. 
3. En el portal, en la página **HPC Pack 2012 R2 on Windows Server 2012 R2** (HPC Pack 2012 R2 en Windows Server 2012 R2), elija el modelo de implementación **Administrador de recursos** y luego haga clic en **Crear**.
   
    ![Imagen de HPC Pack][marketplace]
4. Use el portal para definir la configuración y crear la máquina virtual. Si no está familiarizado con Azure, siga el tutorial [Creación de la primera máquina virtual de Windows en Azure Portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para una implementación de prueba de concepto, normalmente se puede aceptar la configuración predeterminada o recomendada.
   
   > [!NOTE]
   > Si desea unir el nodo principal a un dominio de Active Directory existente en Azure, asegúrese de especificar la red virtual de ese dominio al crear la máquina virtual.
   > 
   > 
5. Una vez que la máquina virtual esté creada y en ejecución, [conéctese a ella](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) mediante Escritorio remoto. 
6. Una la máquina virtual a un bosque de dominio de Active Directory mediante la selección de una de las siguientes opciones:
   
   * Si ha creado la máquina virtual en una red virtual de Azure con un bosque de dominio existente, una la máquina virtual al bosque mediante las herramientas estándar del Administrador de servidores o de Windows PowerShell. Después, reinicie.
   * Si creó la máquina virtual en una nueva red virtual (sin un bosque de dominio existente), configure la máquina virtual como controlador de dominio. Utilice los pasos estándar para instalar y configurar el rol de Servicios de dominio de Active Directory en el nodo principal. Para ver los pasos detallados, consulte [Instalar un nuevo bosque de Active Directory de Windows Server 2012 (nivel 200)](https://technet.microsoft.com/library/jj574166.aspx).
7. Cuando la máquina virtual se esté ejecutando y esté unida a un bosque de Active Directory, inicie los servicios de HPC Pack del modo siguiente:
   
    a. Conéctese a la máquina virtual del nodo principal con una cuenta de dominio que sea miembro del grupo Administradores local. Por ejemplo, use la cuenta de administrador que se configura al crear la máquina virtual del nodo principal.
   
    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Los servicios de HPC Pack pueden tardar algunos minutos en iniciarse.
   
    Para las opciones adicionales de configuración del nodo principal, escriba `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Pasos siguientes
* Ahora puede trabajar con el nodo principal del clúster HPC Pack. Por ejemplo, inicie el Administrador de clústeres de HPC y complete la [lista de tareas pendientes de implementación](https://technet.microsoft.com/library/jj884141.aspx).
* Si desea aumentar la capacidad de proceso del clúster a petición, agregue [nodos de ráfaga de Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) en un servicio en la nube. 
* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144)de HPC Pack.

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
