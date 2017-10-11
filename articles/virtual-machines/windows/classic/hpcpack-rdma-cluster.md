---
title: "Configuración de un clúster de Windows RDMA para ejecutar aplicaciones de MPI | Microsoft Docs"
description: "Aprenda a crear un clúster de Windows HPC Pack con máquinas virtuales de tamaño H16r, H16mr, A8 o A9 para usar la red de RDMA de Azure para ejecutar aplicaciones de MPI."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 19be1d693fe13af0f6c1ab0cb6f7bc829b9fad5a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI
Configure un clúster de Windows RDMA en Azure con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) y [Tamaños de máquina virtual de procesos de alto rendimiento](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) paralelas. Cuando configura nodos basados en Windows Server compatibles con RDMA en un clúster de HPC Pack, las aplicaciones de MPI se comunican eficazmente a través de una red de latencia baja y rendimiento alto en Azure que está basada en tecnología de acceso directo a memoria remota (RDMA).

Si desea ejecutar cargas de trabajo MPI en máquinas virtuales de Linux que tienen acceso a la red RDMA de Azure, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones de MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Opciones de implementación de clústeres de HPC Pack
Microsoft HPC Pack es una herramienta gratuita para crear clústeres de HPC locales o en Azure para ejecutar aplicaciones HPC de Windows o Linux. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de paso de mensajes para Windows (MS-MPI). Cuando se usa con instancias compatibles con RDMA que se ejecutan un sistema operativo Windows Server compatible, HPC Pack proporciona una opción eficaz para ejecutar aplicaciones de MPI de Windows que accedan a la red de RDMA de Azure. 

En este artículo se presentan dos escenarios y vínculos a instrucciones detalladas para configurar un clúster de Windows RDMA con Microsoft HPC Pack. 

* Escenario 1. Implementación de instancias de rol de trabajo de proceso intensivo (PaaS)
* Escenario 2. Implementación de nodos de proceso en máquinas virtuales de proceso intensivo (IaaS)

Para ver los requisitos previos para usar instancias de proceso intensivo con Windows, consulte [Tamaños de máquina virtual de procesos de alto rendimiento](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Escenario 1: Implementación de instancias de rol de trabajo de proceso intensivo (PaaS)
En un clúster de HPC Pack existente, agregue recursos de proceso adicionales en instancias de rol de trabajo de Azure (nodos de Azure) que se ejecutan en un servicio en la nube (PaaS). Esta característica de HPC Pack, también denominada "ráfaga a Azure", admite diferentes para las instancias de rol de trabajo. Al agregar los nodos de Azure, especifique uno de los tamaños compatibles con RDMA.

A continuación, se presentan consideraciones y pasos para enviar ráfagas a instancias de Azure compatibles con RDMA desde un clúster existente (normalmente, local). Use procedimientos similares para agregar instancias de rol de trabajo a un nodo principal de HPC Pack que se implementa en una máquina virtual de Azure.

> [!NOTE]
> Para ver un tutorial sobre cómo enviar ráfagas a Azure con HPC Pack, consulte [Configurar un clúster de proceso híbrido con Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenga en cuenta que las consideraciones que aparecen en los siguientes pasos se aplican específicamente a los nodos de Azure compatibles con RDMA.
> 
> 

![Ráfaga a Azure][burst]

### <a name="steps"></a>Pasos
1. **Implementación y configuración de un nodo principal de HPC Pack 2012 R2**
   
    Descargue el paquete de instalación de HPC Pack más reciente del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para ver los requisitos y las instrucciones de preparación de una implementación de ráfaga de Azure, consulte el artículo sobre [ráfagas a instancias de trabajo de Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Configurar un certificado de administración en la suscripción a Azure**
   
    Configure un certificado para proteger la conexión entre el nodo principal y Azure. Para ver las opciones y los procedimientos, consulte [Escenarios para configurar el certificado de administración de Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Para las implementaciones de prueba, HPC Pack instale un certificado de administración predeterminado de Microsoft HPC Azure que puede cargar rápidamente a su suscripción de Azure.
3. **Crear un nuevo servicio en la nube y una cuenta de almacenamiento**
   
    Use Azure Portal para crear un servicio en la nube y una cuenta de almacenamiento para la implementación en una región donde haya disponibles instancias compatibles con RDMA.
4. **Crear una plantilla de nodo de Azure**
   
    Use el Asistente para crear plantillas de nodo en el Administrador de clústeres de HPC. Para conocer los pasos, consulte [Crear una plantilla de nodo de Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) en "Pasos para implementar nodos de Azure con Microsoft HPC Pack".
   
    Para las pruebas iniciales, es recomendable configurar una directiva de disponibilidad manual en la plantilla.
5. **Agregar nodos al clúster**
   
    Use el Asistente para agregar nodos en el Administrador de clústeres de HPC. Para obtener más información, consulte [Agregar nodos de Azure al clúster de Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Cuando especifique el tamaño de los nodos, seleccione uno de los tamaños de instancia compatibles con RDMA.
   
   > [!NOTE]
   > En cada implementación de ráfaga a Azure con las instancias de proceso intensivo, HPC Pack implementa automáticamente un mínimo de 2 instancias compatibles con RDMA (como A8) como nodos proxy, además de las instancias de rol de trabajo de Azure que especifique. Los nodos proxy usan núcleos que se asignan a la suscripción y conllevan cargos junto con las instancias de rol de trabajo de Azure.
   > 
   > 
6. **Iniciar (aprovisionar) los nodos y conectarlos para ejecutar trabajos**
   
    Seleccione los nodos y use la acción **Iniciar** en el Administrador de clústeres de HPC. Cuando se complete el aprovisionamiento, seleccione los nodos y use la acción **Poner en línea** en el Administrador de clústeres de HPC. Los nodos están listos para ejecutar trabajos.
7. **Enviar trabajos al clúster**
   
   Use las herramientas de envío de trabajos de HPC Pack para ejecutar trabajos de clúster. Consulte [Microsoft HPC Pack: Administración de trabajos](http://technet.microsoft.com/library/jj899585.aspx).
8. **Detener (desaprovisionar) los nodos**
   
   Cuando termine de ejecutar los trabajos, desconecte los nodos y use la acción **Detener** del Administrador de clústeres de HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Escenario 2: Implementación de nodos de ejecución en máquinas virtuales de proceso intensivo (IaaS)
En este escenario, se implementa el nodo principal de HPC Pack y nodos de ejecución de clúster en máquinas virtuales en una red virtual de Azure. HPC Pack ofrece varias [opciones de implementación de máquinas virtuales de Azure](../../linux/hpcpack-cluster-options.md), incluidos los scripts de implementación automatizados y las plantillas de inicio rápido de Azure. Como ejemplo, las siguientes consideraciones y pasos lo guían en el uso del [script de implementación de HPC Pack IaaS](hpcpack-cluster-powershell-script.md) para automatizar la implementación de un clúster de HPC Pack 2012 R2 en Azure.

![Clúster en máquinas virtuales de Azure][iaas]

### <a name="steps"></a>Pasos
1. **Crear un nodo principal de clúster y las máquinas virtuales de los nodos de proceso con el script de implementación IaaS de HPC Pack en un equipo cliente**
   
    Descargue el paquete del script de implementación IaaS de HPC Pack del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).
   
    Para preparar el equipo cliente, crear el archivo de configuración del script y ejecutar el script, consulte [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS](hpcpack-cluster-powershell-script.md). 
   
    Para implementar nodos de proceso compatibles con RDMA, tenga en cuenta las siguientes consideraciones adicionales:
   
   * **Red virtual**: especifique una red virtual nueva en una región donde el tamaño de instancia compatible con RDMA que desee usar esté disponible.
   * **Sistema operativo Windows Server**: para admitir la conectividad RDMA, especifique un sistema operativo Windows Server 2012 R2 o Windows Server 2012 para las máquinas virtuales de nodo de ejecución.
   * **Servicios en la nube**: se recomienda implementar el nodo principal en un servicio en la nube y los nodos de ejecución en otro diferente.
   * **Tamaño de nodo principal**: en este caso, considere un tamaño de al menos A4 (extragrande) para el nodo principal.
   * **Extensión HpcVmDrivers**: el script de implementación instala el agente de VM de Azure y la extensión HpcVmDrivers automáticamente al implementar nodos de ejecución de tamaño A8 o A9 con un sistema operativo Windows Server. HpcVmDrivers instala a controladores en las máquinas virtuales de nodos de proceso para que se puedan conectar a la red RDMA. En las máquinas virtuales de la serie H compatibles con RDMA, debe instalar manualmente la extensión HpcVmDrivers. Consulte [Tamaños de máquina virtual de procesos de alto rendimiento](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Configuración de red de clúster**: el script de implementación configura automáticamente el clúster de HPC Pack en la topología 5 (todos los nodos de la red empresarial). Esta topología es obligatoria para todas las implementaciones de clúster de HPC Pack en máquinas virtuales. No cambie la topología de red de clúster más adelante.
2. **Poner en línea los nodos de proceso para ejecutar trabajos**
   
    Seleccione los nodos y use la acción **Poner en línea** en el Administrador de clústeres de HPC. Los nodos están listos para ejecutar trabajos.
3. **Enviar trabajos al clúster**
   
    Conecte con el nodo principal para enviar trabajos o configure un equipo local para ello. Para obtener información, consulte [Envío de trabajos a un clúster HPC en Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Desconecte los nodos y deténgalos (desasígnelos)**
   
    Cuando termine de ejecutar los trabajos, desconecte los nodos en el Administrador de clústeres de HPC. A continuación, use las herramientas de administración de Azure para apagarlos.

## <a name="run-mpi-applications-on-the-cluster"></a>Ejecución de aplicaciones de MPI en el clúster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Ejemplo: Ejecutar mpipingpong en un clúster de HPC Pack
Para comprobar una implementación de HPC Pack en las instancias compatibles con RDMA, ejecute el comando **mpipingpong** de HPC Pack en el clúster. **mpipingpong** envía repetidamente paquetes de datos entre nodos emparejados para calcular la latencia, la capacidad de proceso y las estadísticas de la red de la aplicación habilitada para RDMA. Este ejemplo muestra un patrón típico para ejecutar un trabajo MPI (en este caso, **mpipingpong**) mediante el uso del comando **mpiexec** del clúster.

En este ejemplo se supone que ha agregado nodos de Azure en una configuración "ráfaga a Azure" ([scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Si implementó HPC Pack en un clúster de máquinas virtuales de Azure, deberá modificar la sintaxis del comando para especificar un grupo de nodos diferente y establecer variables de entorno adicionales para dirigir el tráfico de red a la red RDMA.

Para ejecutar mpipingpong en el clúster:

1. En el nodo principal o en un equipo cliente correctamente configurado, abra un símbolo del sistema.
2. Para calcular la latencia entre pares de nodos en una implementación de ráfaga a Azure de cuatro nodos, escriba el siguiente comando para enviar un trabajo para ejecutar mpipingpong con un tamaño de paquete pequeño y muchas iteraciones:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    El comando devuelve el identificador del trabajo que se envió.
   
    Si implementó el clúster de HPC Pack en máquinas virtuales de Azure, especifique un grupo de nodos que contenga máquinas virtuales de nodos de proceso implementadas en un solo servicio en la nube, y modifique el comando **mpiexec** de la siguiente manera:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Una vez finalizado el trabajo, escriba lo siguiente para ver la salida (en este caso, la salida de la tarea 1 del trabajo):
   
    ```Command
    task view <JobID>.1
    ```
   
    donde &lt;*JobID*&gt; es el id. del trabajo que se ha enviado.
   
    La salida incluye resultados de latencia similares a los siguientes.
   
    ![Latencia de ping pong][pingpong1]
4. Para calcular el rendimiento entre pares de nodos de ráfaga de Azure, escriba el siguiente comando para enviar un trabajo para ejecutar **mpipingpong** con un tamaño de paquete grande y pocas iteraciones:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    El comando devuelve el identificador del trabajo que se envió.
   
    En un clúster de HPC Pack implementado en máquinas virtuales de Azure, modifique el comando como se indica en el paso 2.
5. Una vez finalizado el trabajo, escriba lo siguiente para ver la salida (en este caso, la salida de la tarea 1 del trabajo):
   
    ```Command
    task view <JobID>.1
    ```
   
   La salida incluye resultados de rendimiento similares a los siguientes.
   
   ![Capacidad de procesamiento de ping pong][pingpong2]

### <a name="mpi-application-considerations"></a>Consideraciones de las aplicaciones de MPI
Las siguientes son consideraciones que hay que tener en cuenta para ejecutar aplicaciones de MPI con HPC Pack en Azure. Algunas se aplican solo a las implementaciones de nodos de Azure (instancias de rol de trabajo agregadas a una configuración "ráfaga a Azure").

* Azure reaprovisiona periódicamente y sin previo aviso las instancias de rol de trabajo en un servicio en la nube (por ejemplo, para el mantenimiento del sistema o en caso de errores en una instancia). Si se reaprovisiona una instancia mientras se ejecuta un trabajo de MPI, la instancia pierde sus datos y vuelve al estado que tenía cuando se implementó por primera vez, lo que puede provocar un error en el trabajo de MPI. Cuantos más nodos use para un solo trabajo de MPI y más tiempo se ejecute el trabajo, más probable es que una de las instancias se reaprovisione mientras se está ejecutando un trabajo. También considere si designa un solo nodo en la implementación como servidor de archivos.
* Para ejecutar trabajos de MPI en Azure, no tiene que usar las instancias compatibles con RDMA. Puede usar cualquier tamaño de instancia admitido por HPC Pack. Sin embargo, se recomiendan las instancias compatibles con RDMA para ejecutar trabajos de MPI relativamente grandes que sean sensibles a la latencia y al ancho de banda de la red que conecta los nodos. Si usa otros tamaños para ejecutar trabajos de MPI sensibles a la latencia y al ancho de banda, se recomienda ejecutar trabajos pequeños, en los que una sola tarea se ejecuta solo en unos pocos nodos.
* Las aplicaciones implementadas en instancias de Azure están sujetas a los términos de licencias asociados a la aplicación. Póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso.
* Las instancias de Azure necesitan una configuración adicional para acceder a los nodos locales, los recursos compartidos y los servidores de licencias. Por ejemplo, para que los nodos de Azure puedan acceder a un servidor de licencias local, puede configurar una red virtual de Azure de sitio a sitio.
* Para ejecutar aplicaciones de MPI en instancias de Azure, registre cada aplicación de MPI con el Firewall de Windows en las instancias mediante el comando **hpcfwutil** . Esto permite que las comunicaciones de MPI se realicen en un puerto asignado dinámicamente por el firewall.
  
  > [!NOTE]
  > Para las implementaciones de ráfaga a Azure, también puede configurar un comando de excepción de firewall para que se ejecute automáticamente en todos los nodos de Azure nuevos que se agreguen al clúster. Después de ejecutar el comando **hpcfwutil** y comprobar que la aplicación funciona, agregue el comando a un script de inicio para los nodos de Azure. Para más información, consulte [Uso de un script de inicio para los nodos de Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack usa la variable de entorno de clúster CCP_MPI_NETMASK para especificar un intervalo de direcciones aceptables para la comunicación de MPI. A partir de HPC Pack 2012 R2, la variable de entorno de clúster CCP_MPI_NETMASK solo afecta a la comunicación de MPI entre nodos de proceso de clúster unidos a un dominio (de forma local o en máquinas virtuales de Azure). La variable es omitida por los nodos agregados en una configuración de ráfaga a Azure.
* Los trabajos de MPI no se pueden ejecutar en instancias de Azure que están implementadas en servicios en la nube diferentes (por ejemplo, en implementaciones de ráfaga a Azure con distintas plantillas de nodo o nodos de proceso de máquinas virtuales de Azure implementados en varios servicios en la nube). Si tiene varias implementaciones de nodos de Azure que se inician con distintas plantillas de nodo, debe ejecutar el trabajo de MPI solo en un conjunto de nodos de Azure.
* Cuando se agregan nodos de Azure al clúster y se ponen en línea, el servicio Programador de trabajos de HPC intenta iniciar inmediatamente los trabajos en los nodos. Si solo se puede ejecutar en Azure una parte de la carga de trabajo, asegúrese de actualizar o crear plantillas de trabajo para definir qué tipos de trabajo se pueden ejecutar en Azure. Por ejemplo, para asegurarse de que los trabajos enviados con una plantilla de trabajo solo se ejecutarán en nodos de Azure, agregue la propiedad Grupos de nodos a la plantilla de trabajo y seleccione AzureNodes como valor requerido. Para crear grupos personalizados para los nodos de Azure, use el cmdlet Add-HpcGroup de HPC PowerShell.

## <a name="next-steps"></a>Pasos siguientes
* Como alternativa al uso de HPC Pack, desarrolle con el servicio de Lote de Azure para ejecutar aplicaciones MPI en los grupos administrados de nodos de proceso de Azure. Consulte [Uso de tareas de instancias múltiples para ejecutar aplicaciones de la Interfaz de paso de mensajes (MPI) en Azure Batch](../../../batch/batch-mpi.md).
* Si desea ejecutar aplicaciones de Linux MPI que tienen acceso a la red RDMA de Azure, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones de MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
