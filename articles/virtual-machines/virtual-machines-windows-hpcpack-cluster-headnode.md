<properties
 pageTitle="Creación de un nodo principal de HPC Pack en una máquina virtual de Azure | Microsoft Azure"
 description="Aprenda a usar el Portal de Azure y el modelo de implementación del Administrador de recursos para crear un nodo principal de HPC Pack en una máquina virtual de Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace


Use una [imagen de máquina virtual de Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de Azure Marketplace y el Portal de Azure para crear el nodo principal de un clúster HPC. Esta imagen de máquina virtual de HPC Pack se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstalado. Utilice este nodo principal para una implementación de prueba de concepto de HPC Pack en Azure. A continuación, puede agregar nodos de proceso al clúster para ejecutar cargas de trabajo HPC.



>[AZURE.TIP]Para implementar un clúster de HPC Pack completo en Azure que incluya el nodo principal y los nodos de proceso, se recomienda utilizar un método automatizado. Entre las opciones se incluyen el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) y la plantilla de Resource Manager del [clúster de HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Consulte [Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md) para ver más plantillas.


## Consideraciones de planeación

Como se muestra en la ilustración siguiente, implementa el nodo principal de HPC Pack en un dominio de Active Directory de una red virtual de Azure.

![Nodo principal de HPC Pack][headnode]

* **Dominio de Active Directory**: el nodo principal de HPC Pack debe estar unido a un dominio de Active Directory en Azure antes de iniciar los servicios HPC en la máquina virtual. Como se muestra en este artículo, para realizar una prueba de implementación del concepto, puede promover la máquina virtual creada para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC. Otra opción es implementar un controlador de dominio independiente y el bosque en Azure al que se une la máquina virtual del nodo principal.

* **Red virtual de Azure**: al usar el modelo de implementación de Resource Manager para implementar el nodo principal, especifique o cree una red virtual. Use la red virtual si se debe unir el nodo principal a un dominio de Active Directory existente. También la necesita más adelante para agregar máquinas virtuales de nodo de ejecución al clúster.

    
## Pasos para crear el nodo principal

A continuación, se muestran los pasos generales para, con el Portal de Azure, crear una máquina virtual de Azure para el nodo principal de HPC Pack mediante el modelo de implementación de Resource Manager.


1. Si desea crear un nuevo bosque de Active Directory en Azure con máquinas virtuales de controlador de dominio independientes, una opción es usar una [plantilla de Resource Manager](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para realizar simplemente una implementación de prueba de concepto, se puede omitir este paso y configurar la propia máquina virtual del nodo principal como un controlador de dominio. Esta opción se describe más adelante en este artículo.
    
2. En la página [HPC Pack 2012 R2 on Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) (página HPC Pack 2012 R2 en Windows Server 2012 R2) en Azure Marketplace, haga clic en **Crear Máquina Virtual**.

3. En el portal, en la página **HPC Pack 2012 R2 on Windows Server 2012 R2** (página HPC Pack 2012 R2 en Windows Server 2012 R2), elija el modelo de implementación **Administrador de recursos** y luego haga clic en **Crear**.

    ![Imagen de HPC Pack][marketplace]

4. Use el portal para definir la configuración y crear la máquina virtual. Si no está familiarizado con Azure, siga el tutorial [Creación de la primera máquina virtual Windows en el Portal de Azure](virtual-machines-windows-hero-tutorial.md). Para una implementación de prueba de concepto, normalmente se puede aceptar la configuración predeterminada o recomendada.

    >[AZURE.NOTE]Si desea unir el nodo principal a un dominio de Active Directory existente en Azure, asegúrese de especificar la red virtual de ese dominio al crear la máquina virtual.
       
4. Una vez que la máquina virtual esté creada y en ejecución, [conéctese a ella](virtual-machines-windows-connect-logon.md) mediante Escritorio remoto.

5. Una la máquina virtual a un bosque de dominio existente o cree un bosque de dominio en la propia máquina virtual.

    * Si ha creado la máquina virtual en una red virtual de Azure con un bosque de dominio existente, una la máquina virtual al bosque mediante las herramientas estándar del Administrador de servidores o de Windows PowerShell. Después, reinicie.

    * Si creó la máquina virtual en una nueva red virtual (sin un bosque de dominio existente), configure la máquina virtual como controlador de dominio. Utilice los pasos estándar para instalar y configurar el rol de Servicios de dominio de Active Directory en el nodo principal. Para ver los pasos detallados, consulte [Instalar un nuevo bosque de Active Directory de Windows Server 2012 (nivel 200)](https://technet.microsoft.com/library/jj574166.aspx).

5. Cuando la máquina virtual se esté ejecutando y esté unida a un bosque de Active Directory, inicie los servicios de HPC Pack del modo siguiente:

    a. Conéctese a la máquina virtual del nodo principal con una cuenta de dominio que sea miembro del grupo Administradores local. Por ejemplo, use la cuenta de administrador que se configura al crear la máquina virtual del nodo principal.

    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Los servicios de HPC Pack pueden tardar algunos minutos en iniciarse.

    Para las opciones adicionales de configuración del nodo principal, escriba `get-help HPCHNPrepare.ps1`.


## Pasos siguientes

* Ahora puede trabajar con el nodo principal del clúster HPC Pack. Por ejemplo, inicie el Administrador de clústeres de HPC y complete la [lista de tareas pendientes de implementación](https://technet.microsoft.com/library/jj884141.aspx).
* Si desea aumentar la capacidad de proceso del clúster a petición, agregue [nodos de ráfaga de Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) en un servicio en la nube.

* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144) de HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0824_2016-->