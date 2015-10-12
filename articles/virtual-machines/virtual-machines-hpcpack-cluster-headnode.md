<properties
 pageTitle="Creación de un nodo principal de HPC Pack en una máquina virtual de Azure | Microsoft Azure"
 description="Aprenda a usar el Portal de Azure y el modelo de implementación clásico para crear un nodo principal de Microsoft HPC Pack en una máquina virtual de Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Creación del nodo principal de un clúster de HPC Pack en una máquina virtual de Azure con una imagen de Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artículo se aplica a la creación de un recurso con el modelo de implementación clásica.

En este artículo se muestra cómo usar la [imagen de máquina virtual de Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) en Azure Marketplace para crear el nodo principal de un clúster de Windows HPC en Azure en el modelo de implementación clásico (administración de servicios). El nodo principal debe estar unido a un dominio de Active Directory en una red virtual de Azure. Puede usar este nodo principal para realizar una prueba de implementación del concepto de HPC Pack en Azure y agregar recursos de proceso al clúster para ejecutar cargas de trabajo de HPC.


![Nodo principal de HPC Pack][headnode]

>[AZURE.NOTE]Actualmente, la imagen de máquina virtual de HPC Pack se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 2 preinstalado. Microsoft SQL Server 2014 Express también está preinstalado.


Para una implementación de producción de un clúster de HPC Pack en Azure, se recomienda un método de implementación automatizado, como el [script de implementación de HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md) o una [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/) de Administrador de recursos de Azure.

## Consideraciones de planeación

* **Dominio de Active Directory**: el nodo principal de HPC Pack debe estar unido a un dominio de Active Directory en Azure antes de iniciar los servicios HPC. Una opción es implementar un controlador de dominio independiente y el bosque implementado en Azure al que se puede unir la máquina virtual. Para realizar una prueba de implementación del concepto, puede promover la máquina virtual creada para el nodo principal como un controlador de dominio antes de iniciar los servicios HPC.

* **Red virtual de Azure**: si piensa agregar máquinas virtuales de nodos de proceso de clúster al clúster HPC o crea un controlador de dominio independiente para el clúster, deberá implementar el nodo principal en una red virtual (VNet) de Azure. Sin una red virtual todavía puede agregar nodos de "ráfagas" en el clúster.

## Pasos para crear el nodo principal

Estos son los pasos de nivel superior para crear una máquina virtual de Azure para el nodo principal de HPC Pack. Puede usar una variedad de herramientas de Azure para realizar estos pasos en el modelo de implementación clásico (administración de servicios) de Azure.


1. Si planea crear una máquina virtual para la máquina virtual del nodo principal consulte [Creación de una red virtual usando el portal de vista previa de Azure](../virtual-networks/virtual-networks-create-vnet-classic-pportal.md).

    **Consideraciones**

    * Puede aceptar la configuración predeterminada del espacio de direcciones y las subredes de la red virtual.

    * Si piensa usar un tamaño de instancia de proceso intensivo (A8: A11) para el nodo principal de HPC Pack o después de agregar recursos de proceso al clúster, elija una región en la que estén disponibles las instancias. Al usar instancias A8 o A9 para cargas de trabajo MPI, asegúrese también de que el espacio de direcciones de la red virtual no se superpone con el espacio de direcciones reservado por la red RDMA de Azure (172.16.0.0/12). Para obtener más información, consulte [Sobre las instancias de proceso intensivo A8, A9, A10 y A11](virtual-machines-a8-a9-a10-a11-specs.md).

2. Si necesita crear un nuevo bosque de Active Directory en una máquina virtual independiente, consulte [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](../active-directory/active-directory-new-forest-virtual-machine.md).

    **Consideraciones**

    * En muchas de las implementaciones de prueba puede crear un controlador de dominio en Azure. Para garantizar la alta disponibilidad del dominio de Active Directory, puede implementar un controlador de dominio adicional y de copia de seguridad.

    * Para realizar una simple prueba de implementación del concepto, puede omitir este paso y después promocionar el nodo principal de máquina virtual como un controlador de dominio.

3. En el Portal de administración de Azure o el Portal de vista previa de Azure, cree una máquina virtual clásica mediante la selección de la imagen de HPC Pack 2012 R2 en Azure Marketplace. (Consulte los pasos para el Portal de administración [aquí](virtual-machines-windows-tutorial-classic-portal.md)).

    **Consideraciones**

    * Seleccione un tamaño de memoria virtual de al menos A4.

    * Si desea implementar el nodo principal en una máquina virtual, asegúrese de especificar la red virtual en la configuración de la máquina virtual.

    * Le recomendamos que cree un nuevo servicio en la nube para la máquina virtual.

4. Después de crear la máquina virtual y de que se esté ejecutando, una la máquina virtual a un bosque de dominio existente o cree un nuevo bosque de dominio en la máquina virtual.

    **Consideraciones**

    * Si creó la máquina virtual en una red virtual de Azure con un bosque de dominio existente, conéctese a la máquina virtual. A continuación, use las herramientas estándar de Administrador del servidor o Windows PowerShell para unirse al bosque de dominio. Después, reinicie.

    * Si no se creó la máquina virtual en una red virtual de Azure, o se creó en una máquina virtual sin un bosque de dominio existente, promociona como un controlador de dominio. Para ello, conéctese a la máquina virtual y después use las herramientas estándar del Administrador del servidor o Windows PowerShell. Para ver los pasos detallados, consulte [Instalar un nuevo bosque de Active Directory de Windows Server 2012 (nivel 200)](https://technet.microsoft.com/library/jj574166.aspx).

5. Cuando la máquina virtual se esté ejecutando y esté unida a un bosque de Active Directory, inicie los servicios de HPC Pack en el nodo principal. Para ello, siga estos pasos:

    a. Conéctese a la máquina virtual con una cuenta de dominio que sea miembro del grupo Administradores local. Por ejemplo, puede usar la cuenta de administrador que se configura al crear la máquina virtual del nodo principal.

    b. Para una configuración de nodo principal predeterminada, inicie Windows PowerShell como administrador y escriba lo siguiente:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Los servicios de HPC Pack pueden tardar algunos minutos en iniciarse.

    Para las opciones adicionales de configuración del nodo principal, escriba `get-help HPCHNPrepare.ps1`.


## Pasos siguientes

* Ahora puede trabajar con el nodo principal del clúster HPC de Windows. Por ejemplo, puede iniciar el Administrador de clústeres de HPC o empezar a trabajar con los cmdlets de PowerShell de HPC.

* [Agregue máquinas virtuales de nodo de proceso](virtual-machines-hpcpack-cluster-node-manage.md) al clúster o agregue [nodos ráfagas de Azure](virtual-machines-hpcpack-cluster-node-burst.md) en un servicio en la nube.

* Pruebe a ejecutar una carga de trabajo de prueba en el clúster. Para obtener un ejemplo, consulte la [guía de introducción](https://technet.microsoft.com/library/jj884144) de HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=Oct15_HO1-->