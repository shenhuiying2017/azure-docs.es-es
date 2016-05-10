<properties
 pageTitle="Ejecución de StarCCM+ con HPC Pack en máquinas virtuales de Linux | Microsoft Azure"
 description="Implemente un clúster de Microsoft HPC Pack en Azure y ejecute un trabajo StarCCM+ en varios nodos de proceso Linux en una red RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Ejecución de StarCCM+ con Microsoft HPC Pack en un clúster de Linux RDMA en Azure
En este artículo se muestra cómo implementar un clúster de Microsoft HPC Pack en Azure y ejecutar un trabajo [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) en varios nodos de proceso Linux interconectados con Infiniband.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack proporciona características para ejecutar una variedad de aplicaciones HPC y paralelas a gran escala, incluidas las aplicaciones MPI, en clústeres de máquinas virtuales de Microsoft Azure. HPC Pack también admite la ejecución de aplicaciones Linux HPC en máquinas virtuales de nodos de proceso Linux implementadas en un clúster de HPC Pack. Consulte [Introducción a los nodos de ejecución de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para ver información básica relativa al uso de los nodos de ejecución de Linux con HPC Pack.

## Instalación del clúster de HPC Pack
Descargue los scripts de implementación IaaS de HPC Pack [aquí](https://www.microsoft.com/es-ES/download/details.aspx?id=44949) y extráigalos localmente.

Azure PowerShell es un requisito previo; lea este artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) si no está configurado en el equipo local.

En el momento de redactar este artículo, las imágenes de Linux de la Galería de Azure que contiene los controladores de Infiniband para Azure son para SLES 12, CentOS 6.5 y CentOS 7.1. Este artículo se basa en el uso de SLES 12. Para recuperar el nombre de todas las imágenes de Linux que admiten HPC en la Galería, puede ejecutar el siguiente comando de PowerShell:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

La salida muestra la ubicación en que están disponibles estas imágenes y el valor **ImageName** que se utilizará después en la plantilla de implementación. Antes de implementar el clúster, debe crear un archivo de plantilla de implementación de HPC Pack. Además, como el objetivo es un clúster pequeño, el nodo principal será el controlador de dominio y hospedará una base de datos SQL local. La plantilla siguiente implementará tal nodo principal (HeadNode), creará un archivo XML llamado **MyCluster.xml** y reemplazará los valores de **SubscriptionId**, **StorageAccount**, **Location**, **VMName** y **ServiceName** por los suyos.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Para comenzar a crear el nodo principal, ejecute el comando de PowerShell en una ventana de comandos con privilegios elevados:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Después de 20 a 30 minutos, el nodo principal debería estar listo. Para conectarse a él desde el Portal de Azure, haga clic en el icono Conectar de la máquina virtual.

Es posible que tenga que corregir el reenviador DNS. Para hacerlo, inicie el Administrador de DNS.

1.  Haga clic con el botón derecho en el nombre del servidor en el Administrador de DNS, seleccione Propiedades y seleccione la pestaña Reenviadores.

2.  Haga clic en el botón Editar para quitar los reenviadores que haya y después haga clic en Aceptar.

3.  Asegúrese de que esté activada la casilla "Usar sugerencias de raíz si no hay reenviadores disponibles" y haga clic en Aceptar.

## Configuración de nodos de proceso Linux
La implementación de los nodos de proceso Linux se lleva a cabo con la misma plantilla de implementación que se usó antes para crear el nodo principal. Copie el archivo **MyCluster.xml** del equipo local al nodo principal y actualice la etiqueta **NodeCount** con el número de nodos que desee implementar (<=20). Procure tener suficientes núcleos disponibles en la cuota de Azure, ya que cada instancia A9 consumirá 16 núcleos de su suscripción. Puede usar instancias A8 (8 núcleos) en lugar de A9 si desea utilizar más máquinas virtuales con el mismo presupuesto.

En el nodo principal, copie los scripts de implementación IaaS de HPC Pack.

Abra Azure PowerShell en una ventana de comandos con privilegios elevados:

1.  Ejecute **Add-AzureAccount** para conectarse a su suscripción de Azure.

2.  Si tiene varias suscripciones, ejecute **Get-AzureSubscription** para enumerarlas.

3.  Establezca una predeterminada con el comando **Select-AzureSubscription -SubscriptionName xxxx -Default**.

4.  Ejecute **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** para empezar a implementar nodos de proceso Linux. ![Implementación del nodo principal en acción][hndeploy]

Abra la herramienta del administrador de clústeres de HPC Pack y, tras unos minutos, normalmente los nodos de proceso Linux aparecerán en la lista de nodos de proceso del clúster. Con el modo clásico de implementación, las máquinas virtuales IaaS se crean de forma secuencial, por lo que si el número de nodos es considerable, se puede tardar bastante tiempo en implementarlas todas. ![Nodos Linux en el administrador de clústeres de HPC Pack][clustermanager]

Ahora que todos los nodos están en ejecución en el clúster, hay que establecer diversas configuraciones de infraestructura adicionales.

## Configuración de un recurso compartido de archivos de Azure para nodos Linux y Windows
Para facilitar el trabajo, usamos archivos de Azure, lo que proporciona funcionalidad CIFS ademas de blobs de Azure como almacén persistente, para almacenar nuestros scripts, paquetes de aplicación y archivos de datos. Tenga en cuenta que no se trata de la solución más escalable, pero sí es la más sencilla y no requiere máquinas virtuales dedicadas.

Cree un recurso compartido de archivos de Azure siguiendo las instrucciones disponibles en el artículo [Introducción a Almacenamiento de archivos de Azure en Windows](..\storage\storage-dotnet-how-to-use-files.md).

Mantenga **saname** como nombre de la cuenta de almacenamiento, **sharename** como nombre del recurso compartido de archivos y **sakey** como clave de la cuenta de almacenamiento.

### Montaje del recurso compartido de archivos de Azure en el nodo principal
Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para almacenar las credenciales en el almacén del equipo local.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Después, para montar el recurso compartido de archivos de Azure, ejecute:

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Montaje del recurso compartido de archivos de Azure en nodos de proceso Linux
Una herramienta útil que se incluye con HPC Pack es la utilidad clusrun. Esta línea de comandos le permite ejecutar el mismo comando simultáneamente en un conjunto de nodos de proceso. En nuestro caso, se usa para montar el recurso compartido de archivos de Azure y hacerlo persistente para que permanezca tras los reinicios. En una ventana de comandos con privilegios elevados en el nodo principal, ejecute los siguientes comandos.

Para crear el directorio de montaje:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Para montar el recurso compartido de archivos de Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Para hacer persistente el recurso compartido de montaje:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## Actualización de los controladores de Linux
En algún momento tendrá que actualizar los controladores de Infiniband en los nodos de proceso Linux. Lea este artículo para saber si debe hacerlo y cómo: [Actualización de los controladores de Linux RDMA para SLES 12](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12).

## Instalación de StarCCM+
Las instancias A8 y A9 de máquina virtual de Azure proporcionan compatibilidad con Infiniband y funcionalidad de RDMA. En el momento de redactar este artículo, hay controladores de kernel que habilitan esas funcionalidades disponibles para imágenes de Windows 2012 R2 y SUSE 12 en la Galería de Azure; CentOS 7.x estará disponible pronto. Microsoft MPI e Intel MPI (versión 5.x) son las dos bibliotecas MPI compatibles con estos controladores en Azure.

Se incluyen CD-Adapco StarCCM+ versión 11.x y posteriores con la versión 5.x de Intel MPI, por lo que se proporciona compatibilidad con Infiniband para Azure.

Obtenga el paquete de Linux64 StarCCM+ en el [portal de CD-Adapco](https://steve.cd-adapco.com); en nuestro caso, usamos la versión 11.02.010 con el método de precisiones mixtas.

En el nodo principal, en el recurso compartido de archivos **/hpcdata** de Azure, cree un script de shell denominado **setupstarccm.sh** con el siguiente contenido. Este script se iniciará en cada nodo de proceso para configurar StarCCM+ localmente.

#### Script setupstarcm.sh de ejemplo
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Ahora, para configurar StarCCM+ en todos los nodos de proceso Linux, abra una ventana de comandos con privilegios elevados y ejecute el siguiente comando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Mientras se ejecuta el comando, puede supervisar el uso de CPU con el mapa térmico del Administrador de clústeres. Tras unos minutos todos los nodos deberían estar correctamente configurados.

## Ejecución de trabajos StarCCM+
Se usa HPC Pack por su funcionalidad como programador de trabajos para ejecutar trabajos StarCCM+. Para hacerlo, necesitamos algunos scripts que sirven para iniciar el trabajo y ejecutar StarCCM+. Los datos de entrada se mantienen en el recurso compartido de archivos de Azure por simplicidad. El siguiente script de PowerShell sirve para poner en cola un trabajo StarCCM+. Acepta tres argumentos:

*  El nombre del modelo
*  El número de nodos que se usarán
*  El número de núcleos en cada nodo que se vaya a usar

Como StarCCM+ puede maximizar el ancho de banda de memoria, suele ser mejor usar menos núcleos por nodo de proceso y agregar nuevos nodos. La proporción exacta de núcleos por nodo dependerá de la familia de procesadores y la velocidad de la interconexión.

Los nodos se asignan exclusivamente para el trabajo y no se pueden compartir con otros trabajos. Como puede observar, el trabajo no se ha iniciado como trabajo MPI directamente; el iniciador de MPI se iniciará desde el script de shell **runstarccm.sh**.

El modelo de entrada y el script **runstarccm.sh** deben almacenarse en el recurso compartido **/hpcdata** montado antes.

Los archivos de registro reciben un nombre que incluye el identificador del trabajo y se almacenan en el **recurso compartido /hpcdata**, junto con los archivos de salida de StarCCM+.


#### Script SubmitStarccmJob.ps1 de ejemplo
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Reemplace **runner.java** por el iniciador de modelo Java de StarCCM+ y el código de registro que prefiera.

#### Script runstarccm.sh de ejemplo
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run the starccm with hostfile arg
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

En nuestra prueba usamos un token de licencia Power-On-Demand, para el que tendrá que establecer la variable de entorno **$CDLMD\_LICENSE\_FILE** en ****1999@flex.cd-adapco.com** y la clave en la opción **-podkey** de la línea de comandos.

Después de la inicialización, el script extrae de las variables de entorno **$CCP\_NODES\_CORES** establecidas por HPC Pack la lista de nodos para crear un archivo de host utilizado por el iniciador de MPI. Este archivo de host contendrá la lista de nombres de nodos de proceso usados para el trabajo, con un nombre por línea.

El formato de **$CCP\_NODES\_CORES** sigue este patrón:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

donde

* `<Number of nodes>`: es el número de nodos asignados a este trabajo.  
* `<Name of node_n_...>`: es el nombre de cada nodo asignado a este trabajo.
* `<Cores of node_n_...>`: es el número de núcleos en el nodo asignado a este trabajo.

El número de núcleos **$NBCORES** también se calcula en función del número de nodos **$NBNODES** y el número de núcleos por nodo proporcionado como parámetro **$NBCORESPERNODE**.

Para las opciones de MPI, las usadas con Intel MPI en Azure son:

*   -mpi intel => Para especificar IntelMPI.
*   -fabric UDAPL => Para usar los verbos de Infiniband de Azure.
*   -cpubind bandwidth,v => Esto es para optimizar el ancho de banda de MPI con StarCCM+.
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => Son configuraciones de Intel MPI para funcionar con Azure Infiniband y también establece el número necesario de núcleos por nodo.
*   -batch => Para iniciar StarCCM+ en el modo por lotes sin IU.


Por último, para iniciar un trabajo, asegúrese de que los nodos estén en funcionamiento y en línea en el Administrador de clústeres. Después, desde una ventana de comandos de PowerShell, ejecute esto:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## Detención de nodos
Más adelante, cuando haya terminado las pruebas, use los siguientes comandos de PowerShell de HPC Pack para detener e iniciar nodos:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## Pasos siguientes
Pruebe a ejecutar otras cargas de trabajo Linux. Por ejemplo, consulte:

* [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->