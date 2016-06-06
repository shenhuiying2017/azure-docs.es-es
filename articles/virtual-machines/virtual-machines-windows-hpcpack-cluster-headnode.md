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
 ms.date="05/19/2016"
 ms.author="danlep"/>

# Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace


Use una [imagen de máquina virtual de Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de Azure Marketplace para crear el nodo principal de un clúster HPC mediante el Portal de Azure. Esta imagen de máquina virtual de HPC Pack se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 preinstalado. Utilice este nodo principal para una implementación de prueba de concepto de HPC Pack en Azure. A continuación, puede agregar nodos de proceso al clúster para ejecutar cargas de trabajo HPC.



>[AZURE.TIP]Para una implementación de un clúster HPC Pack completo en Azure que incluya el nodo principal y los nodos de proceso, se recomienda utilizar un método automatizado, como el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) o una plantilla de Resource Manager, como la plantilla del [clúster de HPC Pack para cargas de trabajo de Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Consulte [Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) de Windows en Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md) para ver más plantillas.


## Consideraciones de planeación

Como se muestra en la ilustración siguiente, implementará el nodo principal en un dominio de Active Directory en una red virtual de Azure.

![Nodo principal de HPC Pack][headnode]

* **Dominio de Active Directory**: el nodo principal de HPC Pack debe estar unido a un dominio de Active Directory en Azure antes de iniciar los servicios HPC en la máquina virtual. Como se muestra en este artículo, para realizar una prueba de implementación del concepto, puede promover la máquina virtual creada para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC. Otra opción es implementar un controlador de dominio independiente y el bosque en Azure al que se une la máquina virtual del nodo principal.

* **Red virtual de Azure**: como se muestra en este artículo, al implementar el nodo principal de HPC Pack utilizando el modelo de implementación de Resource Manager en el Portal de Azure, hay que especificar o crear una red virtual de Azure. Necesitará usar la red virtual más adelante para agregar máquinas virtuales de nodos de proceso de clúster al clúster de HPC, así como para unir el nodo principal a un dominio de Active Directory existente.

    
## Pasos para crear el nodo principal

Estos son los pasos generales para crear una máquina virtual de Azure para el nodo principal de HPC Pack mediante el modelo de implementación del Administrador de recursos en el portal de Azure.


1. Si desea crear un nuevo bosque de Active Directory en Azure con máquinas virtuales de controlador de dominio independientes, una opción es usar una [plantilla de Resource Manager](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Para una simple implementación de prueba de concepto de HPC Pack, no pasa nada si se omite esto. En su lugar, podrá configurar la propia máquina virtual del nodo principal como controlador de dominio, tal y como se describe más adelante en este artículo.
    
2. Para crear el nodo principal de HPC Pack en el [Portal de Azure](https://portal.azure.com), seleccione la imagen HPC Pack 2012 R2 de Azure Marketplace. Para ello, haga clic en **Nuevo** y busque **HPC Pack** en Marketplace. Elija la imagen **HPC Pack 2012 R2 en Windows Server 2012 R2**.

3. En la página **HPC Pack 2012 R2 en Windows Server 2012 R2**, elija el modelo de implementación de **Resource Manager** y luego haga clic en **Crear**.

    ![Imagen de HPC Pack][marketplace]

4. Use el portal para definir la configuración y crear la máquina virtual. Si no está familiarizado con Azure, siga el tutorial [Creación de la primera máquina virtual de Windows en el Portal de Azure](virtual-machines-windows-hero-tutorial.md). Para una implementación de prueba de concepto, normalmente se pueden aceptar muchos de los valores predeterminados o recomendados.

    **Consideraciones de red virtual**

   * Si desea unir el nodo principal a un dominio de Active Directory existente en Azure, asegúrese de especificar la red virtual de ese dominio al crear la máquina virtual del nodo principal.
   
   * Si desea crear una nueva red virtual, en **Configuración**, especifique un intervalo de direcciones de red privada como 10.0.0.0/16 y un intervalo de direcciones de subred como 10.0.0.0/24.
    
4. Una vez que la máquina virtual esté creada y en ejecución, podrá [conectarla](virtual-machines-windows-connect-logon.md) mediante Escritorio remoto. 

5. Una la máquina virtual a un bosque de dominio existente o cree un nuevo bosque de dominio en la propia máquina virtual.

    * Si ha creado la máquina virtual en una red virtual de Azure con un bosque de dominio existente, utilice las herramientas estándar del Administrador de servidores o de Windows PowerShell para unirla al bosque de dominio. Después, reinicie.

    * Si la máquina virtual se creó en una nueva red virtual sin un bosque de dominio existente, configúrela como un controlador de dominio. Para ello, use las herramientas estándar de Administrador de servidores o Windows PowerShell para instalar y configurar el rol de Servicios de dominio de Active Directory. Para ver los pasos detallados, consulte [Instalar un nuevo bosque de Active Directory de Windows Server 2012 (nivel 200)](https://technet.microsoft.com/library/jj574166.aspx).

5. Cuando la máquina virtual se esté ejecutando y esté unida a un bosque de Active Directory, inicie los servicios de HPC Pack en el nodo principal. Para ello, siga estos pasos:

    a. Conéctese a la máquina virtual con una cuenta de dominio que sea miembro del grupo Administradores local. Por ejemplo, use la cuenta de administrador que se configura al crear la máquina virtual del nodo principal.

    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Los servicios de HPC Pack pueden tardar algunos minutos en iniciarse.

    Para las opciones adicionales de configuración del nodo principal, escriba `get-help HPCHNPrepare.ps1`.


## Pasos siguientes

* Ahora puede trabajar con el nodo principal del clúster HPC Pack. Por ejemplo, inicie el Administrador de clústeres de HPC y complete la [lista de tareas pendientes de implementación](https://technet.microsoft.com/library/jj884141.aspx).
* Agregue [nodos de ráfaga de Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) en un servicio en la nube para aumentar la capacidad de proceso del clúster a petición. 

* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144) de HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0525_2016-->