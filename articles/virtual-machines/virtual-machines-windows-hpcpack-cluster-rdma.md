<properties
 pageTitle="Configuración de un clúster de Windows RDMA para ejecutar aplicaciones de MPI | Microsoft Azure"
 description="Aprenda a crear un clúster de Windows HPC Pack con máquinas virtuales de tamaño A8 o A9 para usar la red RDMA de Azure para ejecutar aplicaciones de MPI."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Configuración de un clúster de Windows RDMA con HPC Pack e instancias de A8 y A9 para ejecutar aplicaciones de MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo configurar un clúster de Windows RDMA en [Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [instancias de proceso intensivo de tamaño A8 y A9](virtual-machines-a8-a9-a10-a11-specs.md) para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) paralelas. Al configurar instancias de tamaño A8 y A9 basadas en Windows Server para ejecutar una implementación de MPI compatible, las aplicaciones de MPI se comunican eficazmente a través de una red de latencia baja y alto rendimiento en Azure basada en tecnología de acceso directo a memoria remota (RDMA).

>[AZURE.NOTE]Windows RDMA de Azure es compatible actualmente con las aplicaciones de MPI que usan la interfaz Microsoft Network Direct para comunicarse entre instancias A8 y A9.
>
> Azure también ofrece instancias de proceso intensivo A10 y A11, con capacidades de procesamiento idénticas a las de las instancias A8 y A9, pero sin una conexión a una red back-end RDMA. Para ejecutar cargas de trabajo MPI en Azure, por lo general, obtendrá mejor rendimiento con las instancias A8 y A9.

Si desea ejecutar cargas de trabajo MPI en máquinas virtuales de Linux que tienen acceso a la red RDMA de Azure, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-cluster-rdma.md).

## Opciones de implementación de clústeres HPC de Windows
Microsoft HPC Pack es una herramienta recomendada para crear clústeres HPC de Windows Server en Azure. Cuando se usan con instancias A8 y A9, HPC Pack es la forma más eficaz de ejecutar aplicaciones de MPI basadas en Windows que tienen acceso a la red RDMA de Azure. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de transferencia de mensajes para Windows.

Este artículo presenta dos escenarios para implementar instancias A8 y A9 en clúster con Microsoft HPC Pack.

* Escenario 1. Implementación de instancias de rol de trabajo de proceso intensivo (PaaS)

* Escenario 2. Implementación de nodos de proceso en máquinas virtuales de proceso intensivo (IaaS)

## Requisitos previos

* **Consulte la [ información general y las consideraciones](virtual-machines-a8-a9-a10-a11-specs.md)** sobre las instancias de proceso intensivo.


* **Suscripción a Azure:** si no tiene ninguna cuenta, puede crear una cuenta gratuita de evaluación en un par de minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).


* **Cuota de núcleos**: es posible que tenga que aumentar la cuota de núcleos para implementar un clúster de máquinas virtuales A8 o A9. Por ejemplo, necesitará al menos 128 núcleos si desea implementar instancias A8 o A9 con HPC Pack. Para aumentar una cuota, abra una [solicitud de soporte técnico al cliente en línea](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

## Escenario 1. Implementación de instancias de rol de trabajo de proceso intensivo (PaaS)


En un clúster de HPC Pack existente, agregue recursos de proceso adicionales en instancias de rol de trabajo de Azure (nodos de Azure) que se ejecutan en un servicio en la nube (PaaS). Esta característica de HPC Pack, también denominada "ráfaga a Azure", admite diferentes para las instancias de rol de trabajo. Para usar instancias de proceso intensivo, simplemente especifique un tamaño A8 o A9 al agregar los nodos de Azure.

Los siguientes son los pasos para enviar ráfagas a instancias de A8 o A9 de Azure desde un clúster existente (normalmente local). Use procedimientos similares para agregar instancias de rol de trabajo a un nodo principal de HPC Pack que se implementa en una máquina virtual de Azure.

>[AZURE.NOTE]Para ver un tutorial sobre cómo enviar ráfagas a Azure con HPC Pack, consulte [Configurar un clúster de proceso híbrido con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenga en cuenta las consideraciones en los pasos siguientes que se aplican específicamente a los nodos de tamaño A8 y A9 de Azure.

![Ráfaga a Azure][burst]



4. **Implementación y configuración de un nodo principal de HPC Pack 2012 R2**

    Descargue el paquete de instalación de HPC Pack más reciente del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=47755). Para ver los requisitos e instrucciones para preparar una implementación de ráfaga a Azure, consulte [Guía de introducción a HPC Pack](https://technet.microsoft.com/library/jj884144.aspx) y [Ráfagas a Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

5. **Configurar un certificado de administración en la suscripción a Azure**

    Configure un certificado para proteger la conexión entre el nodo principal y Azure. Para ver las opciones y los procedimientos, consulte [Escenarios para configurar el certificado de administración de Azure para HPC Pack](http://technet.microsoft.com/library/gg481759.aspx).

6. **Crear un nuevo servicio en la nube y una cuenta de almacenamiento**

    Use el Portal de Azure para crear un servicio en la nube y una cuenta de almacenamiento para implementar en una región donde haya disponibles instancias de proceso intensivo. (No asocie el servicio en la nube y la cuenta de almacenamiento con un grupo de afinidad existente que se use para otras implementaciones).

7. **Crear una plantilla de nodo de Azure**

    Use el Asistente para crear plantillas de nodo en el Administrador de clústeres de HPC. Para conocer los pasos, consulte [Crear una plantilla de nodo de Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) en "Pasos para implementar nodos de Azure con Microsoft HPC Pack".

    Para las pruebas iniciales, es recomendable configurar una directiva de disponibilidad manual en la plantilla.

8. **Agregar nodos al clúster**

    Use el Asistente para agregar nodos en el Administrador de clústeres de HPC. Para obtener más información, consulte [Agregar nodos de Azure al clúster de Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Al especificar el tamaño de los nodos, seleccione A8 o A9.

9. **Iniciar (aprovisionar) los nodos y conectarlos para ejecutar trabajos**

    Seleccione los nodos y use la acción **Iniciar** en el Administrador de clústeres de HPC. Cuando se complete el aprovisionamiento, seleccione los nodos y use la acción **Poner en línea** en el Administrador de clústeres de HPC. Los nodos están listos para ejecutar trabajos.

10. **Enviar trabajos al clúster**

    Use las herramientas de envío de trabajos de HPC Pack para ejecutar trabajos de clúster. Consulte [Microsoft HPC Pack: Administración de trabajos](http://technet.microsoft.com/library/jj899585.aspx).

11. **Detener (desaprovisionar) los nodos**

    Cuando termine de ejecutar los trabajos, desconecte los nodos y use la acción **Detener** del Administrador de clústeres de HPC.


### Consideraciones adicionales

* **Nodos proxy**; en cada implementación de ráfaga a Azure con las instancias de proceso intensivo, HPC Pack implementa automáticamente un mínimo de 2 instancias adicionales de tamaño A8 como nodos proxy, además de las instancias de rol de trabajo de Azure que especifique. Para obtener más información, consulte [Establecimiento del número de nodos proxy de Azure](https://technet.microsoft.com/library/jj899633.aspx). Los nodos proxy usan núcleos que se asignan a la suscripción y conllevan cargos junto con las instancias de rol de trabajo de Azure.

* **Red virtual**: HPC Pack no admite actualmente la configuración de VPN de punto a sitio para las implementaciones PaaS.


## Escenario 2. Implementación de nodos de proceso en máquinas virtuales de proceso intensivo (IaaS)

En este escenario, se implementa el nodo principal de HPC Pack y nodos de proceso intensivo de clúster en máquinas virtuales unidas a un dominio de Active Directory en una red virtual de Azure. El [script de implementación IaaS de HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) automatiza la mayor parte de este proceso y proporciona opciones de implementación flexibles, incluida la capacidad de especificar el tamaño de máquina virtual A8 o A9 para los nodos del clúster. Los siguientes pasos le indican cómo usar este método de implementación automatizada. También puede implementar el clúster con el modelo de implementación del Administrador de recursos mediante una plantilla de inicio rápido de Azure. Para las implementaciones de prueba, puede implementar manualmente el dominio de Active Directory, la máquina virtual del nodo principal, las máquinas virtuales de los nodos de proceso y otras partes de la infraestructura de clúster de HPC Pack en Azure. Consulte [Opciones de clúster de HPC con Microsoft HPC Pack en Azure](virtual-machines-hpcpack-cluster-options.md).

![Clúster en máquinas virtuales de Azure][iaas]


1. **Crear un nodo principal de clúster y las máquinas virtuales de los nodos de proceso con el script de implementación IaaS de HPC Pack en un equipo cliente**

 Descargue el paquete del script de implementación IaaS de HPC Pack del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=47755).

 Para preparar el equipo cliente, crear el archivo de configuración del script y ejecutar el script, consulte [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md). Para implementar nodos de proceso de tamaño A8 y A9, consulte las consideraciones adicionales más adelante en este artículo.

2. **Poner en línea los nodos de proceso para ejecutar trabajos**

    Seleccione los nodos y use la acción **Poner en línea** en el Administrador de clústeres de HPC. Los nodos están listos para ejecutar trabajos.

3. **Enviar trabajos al clúster**

    Conecte con el nodo principal para enviar trabajos o configure un equipo local para ello. Para obtener información, consulte [Envío de trabajos a un clúster HPC en Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

4. **Desconecte los nodos y deténgalos (desasígnelos)**

    Cuando termine de ejecutar los trabajos, desconecte los nodos en el Administrador de clústeres de HPC. A continuación, use las herramientas de administración de Azure para apagarlos.

### Consideraciones adicionales para ejecutar el script de implementación de clúster
* **Red virtual**: especifique una nueva red virtual en una región en la que estén disponibles las instancias A8 y A9.


* **Sistema operativo Windows Server**: para admitir la conectividad RDMA, especifique un sistema operativo Windows Server 2012 R2 o Windows Server 2012 para las máquinas virtuales de nodo de proceso de tamaño A8 o A9.


* **Servicios en la nube**: se recomienda implementar el nodo principal en un servicio en la nube y los nodos de proceso A8 y A9 en un servicio en la nube diferente.


* **Tamaño del nodo principal**: al agregar máquinas virtuales de nodos de proceso con el tamaño A8 o A9, considere usar un tamaño A4 (extra grande) como mínimo para el nodo principal.


* **Extensión HpcVmDrivers**: el script de implementación instala el agente de VM de Azure y la extensión HpcVmDrivers automáticamente al implementar nodos de proceso de tamaño A8 o A9 con un sistema operativo Windows Server. HpcVmDrivers instala a controladores en las máquinas virtuales de nodos de proceso para que se puedan conectar a la red RDMA. Consulte [Características y extensiones de las máquinas virtuales de Azure](virtual-machines-extensions-features.md).


* **Configuración de red de clúster**: el script de implementación configura automáticamente el clúster de HPC Pack en la topología 5 (todos los nodos de la red empresarial). Esta topología es necesaria para todas las implementaciones de clústeres de HPC Pack en máquinas virtuales, incluidas aquellas con nodos de proceso de tamaño A8 o A9. No cambie la topología de red de clúster más adelante.

## Ejecución de aplicaciones de MPI en instancias A8 y A9

### Ejemplo: Ejecutar mpipingpong en un clúster de HPC Pack

Para comprobar una implementación de HPC Pack en las instancias de proceso intensivo, ejecute el comando **mpipingpong** de HPC Pack en el clúster. **mpipingpong** envía repetidamente paquetes de datos entre nodos emparejados para calcular la latencia, la capacidad de proceso y las estadísticas de la red de la aplicación habilitada para RDMA. Este ejemplo muestra un patrón típico para ejecutar un trabajo MPI (en este caso, **mpipingpong**) mediante el uso del comando **mpiexec** del clúster.

En el ejemplo se da por hecho que agregó nodos de Azure en una configuración de "ráfaga a Azure" ([Escenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS)) en estos artículos). Si implementó HPC Pack en un clúster de máquinas virtuales de Azure, deberá modificar la sintaxis del comando para especificar un grupo de nodos diferente y establecer variables de entorno adicionales para dirigir el tráfico de red a la red RDMA.


Para ejecutar mpipingpong en el clúster:


1. En el nodo principal o en un equipo cliente correctamente configurado, abra una ventana de comandos.

2. Para calcular la latencia entre pares de nodos en una implementación de ráfaga a Azure de 4 nodos, escriba el siguiente comando para enviar un trabajo para ejecutar mpipingpong con un tamaño de paquete pequeño y un gran número de iteraciones:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    El comando devuelve el identificador del trabajo que se envió.

    Si implementó el clúster de HPC Pack en máquinas virtuales de Azure, especifique un grupo de nodos que contenga máquinas virtuales de nodos de proceso implementadas en un solo servicio en la nube, y modifique el comando **mpiexec** de la siguiente manera:

  ```
  job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
  ```

3. Una vez finalizado el trabajo, escriba lo siguiente para ver la salida (en este caso, la salida de la tarea 1 del trabajo):

    ```
    task view <JobID>.1
    ```

    donde &lt;*JobID*&gt; es el identificador del trabajo que se envió.

    La salida incluirá resultados de latencia similares a los siguientes.

    ![Latencia de ping pong][pingpong1]

4. Para calcular la capacidad de procesamiento entre pares de nodos de ráfaga de Azure, escriba el siguiente comando para enviar un trabajo para ejecutar **mpipingpong** con un tamaño de paquete grande y un número pequeño de iteraciones:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    El comando devuelve el identificador del trabajo que se envió.

    En un clúster de HPC Pack implementado en máquinas virtuales de Azure, modifique el comando como se indica en el paso 2.

5. Una vez finalizado el trabajo, escriba lo siguiente para ver la salida (en este caso, la salida de la tarea 1 del trabajo): task view &lt;JobID&gt;.1

  La salida incluirá resultados de capacidad de procesamiento similares a los siguientes.

  ![Capacidad de procesamiento de ping pong][pingpong2]


### Consideraciones de las aplicaciones de MPI


Las siguientes son consideraciones que hay que tener en cuenta para ejecutar aplicaciones de MPI en instancias de Azure. Algunas se aplican solo a las implementaciones de nodos de Azure (instancias de rol de trabajo agregadas a una configuración "ráfaga a Azure").

* Azure reaprovisiona periódicamente y sin previo aviso las instancias de rol de trabajo en un servicio en la nube (por ejemplo, para el mantenimiento del sistema o en caso de errores en una instancia). Si se reaprovisiona una instancia mientras se ejecuta un trabajo de MPI, la instancia pierde todos sus datos y vuelve al estado que tenía cuando se implementó por primera vez, lo que puede provocar un error en el trabajo de MPI. Cuantos más nodos use para un solo trabajo de MPI y más tiempo se ejecute el trabajo, más probable es que una de las instancias se reaprovisione mientras se está ejecutando un trabajo. También debe considerar si designa un solo nodo en la implementación como servidor de archivos.


* No tiene que usar las instancias A8 y A9 para ejecutar trabajos de MPI en Azure. Puede usar cualquier tamaño de instancia admitido por HPC Pack. Sin embargo, se recomiendan las instancias A8 y A9 para ejecutar trabajos de MPI relativamente grandes que sean sensibles a la latencia y al ancho de banda de la red que conecta los nodos. Si usa instancias que no sean A8 y A9 para ejecutar trabajos de MPI sensibles a la latencia y al ancho de banda, se recomienda ejecutar trabajos pequeños en los que una sola tarea se ejecuta solo en unos pocos nodos.

* Las aplicaciones implementadas en instancias de Azure están sujetas a los términos de licencias asociados a la aplicación. Póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso.


* Las instancias de Azure necesitan una configuración adicional para acceder a los nodos locales, los recursos compartidos y los servidores de licencias. Por ejemplo, para que los nodos de Azure puedan acceder a un servidor de licencias local, puede configurar una red virtual de Azure de sitio a sitio.


* Para ejecutar aplicaciones de MPI en instancias de Azure, registre cada aplicación de MPI con Firewall de Windows en las instancias ejecutando el comando **hpcfwutil**. Esto permite que las comunicaciones de MPI se realicen en un puerto asignado dinámicamente por el firewall.

    >[AZURE.NOTE]Para las implementaciones de ráfaga a Azure, también puede configurar un comando de excepción de firewall para que se ejecute automáticamente en todos los nodos de Azure nuevos que se agreguen al clúster. Después de ejecutar el comando **hpcfwutil** y comprobar que la aplicación funciona, agregue el comando a un script de inicio para los nodos de Azure. Para obtener más información, consulte [Uso de un script de inicio para los nodos de Azure] (https://technet.microsoft.com/library/jj899632(v=ws.10).aspx).



* HPC Pack usa la variable de entorno de clúster CCP\_MPI\_NETMASK para especificar un intervalo de direcciones aceptables para la comunicación de MPI. A partir de HPC Pack 2012 R2, la variable de entorno de clúster CCP\_MPI\_NETMASK solo afecta a la comunicación de MPI entre nodos de proceso de clúster unidos a un dominio (de forma local o en máquinas virtuales de Azure). La variable es omitida por los nodos agregados en una configuración de ráfaga a Azure.


* Los trabajos de MPI no se pueden ejecutar en instancias de Azure que están implementadas en servicios en la nube diferentes (por ejemplo, en implementaciones de ráfaga a Azure con distintas plantillas de nodo o nodos de proceso de máquinas virtuales de Azure implementados en varios servicios en la nube). Si tiene varias implementaciones de nodos de Azure que se inician con distintas plantillas de nodo, debe ejecutar el trabajo de MPI solo en un conjunto de nodos de Azure.


* Cuando se agregan nodos de Azure al clúster y se ponen en línea, el servicio Programador de trabajos de HPC intenta iniciar inmediatamente los trabajos en los nodos. Si solo se puede ejecutar en Azure una parte de la carga de trabajo, asegúrese de actualizar o crear plantillas de trabajo para definir qué tipos de trabajo se pueden ejecutar en Azure. Por ejemplo, para asegurarse de que los trabajos enviados con una plantilla de trabajo solo se ejecutarán en nodos de Azure, agregue la propiedad Grupos de nodos a la plantilla de trabajo y seleccione AzureNodes como valor requerido. Para crear grupos personalizados para los nodos de Azure, puede usar el cmdlet Add-HpcGroup de HPC PowerShell.


## Pasos siguientes

* Si desea ejecutar aplicaciones de Linux MPI que tienen acceso a la red RDMA de Azure, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-cluster-rdma.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=Nov15_HO3-->