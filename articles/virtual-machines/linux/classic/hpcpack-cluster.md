---
title: "Uso de máquinas virtuales de proceso de Linux en un clúster de HPC Pack | Microsoft Docs"
description: "Aprenda a crear y usar un clúster de HPC Pack en Azure para cargas de trabajo de informática de alto rendimiento (HPC) de Linux."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 809d3944311badf265117d353b65642e044d900c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure
Configure un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) en Azure que contenga un nodo principal en el que se ejecute Windows Server y varios nodos de proceso en los que se ejecute una distribución de Linux compatible. Explore las opciones para mover datos entre los nodos de Linux y el nodo principal de Windows del clúster. Aprenda a enviar trabajos de HPC de Linux al clúster.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

En un nivel alto, el diagrama siguiente muestra el clúster de HPC Pack que se crea para trabajar.

![Clúster de HPC con nodos de Linux][scenario]

Para ver otras opciones para ejecutar cargas de trabajo HPC de Linux en Azure, consulte [Recursos técnicos para informática de alto rendimiento y computación por lotes](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Implementación de un clúster de HPC Pack con nodos de proceso de Linux
En este artículo puede ver dos opciones para implementar un clúster de HPC Pack en Azure con nodos de proceso Linux. Ambos métodos usan una imagen de Marketplace de Windows Server con HPC Pack para crear el nodo principal. 

* **Plantilla de Azure Resource Manager** : use una plantilla de Azure Marketplace, o una plantilla de inicio rápido de la comunidad, para automatizar la creación del clúster en el modelo de implementación de Resource Manager. Por ejemplo, la plantilla [HPC Pack cluster for Linux workloads](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) de Azure Marketplace crea una completa infraestructura de clúster de HPC Pack para cargas de trabajo de Linux HPC.
* **Script de PowerShell**: use el [script de implementación de IaaS de Microsoft HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) para automatizar una implementación de clúster completa en el modelo de implementación clásica. Este script de Azure PowerShell usa una imagen de VM de HPC Pack en Azure Marketplace para una implementación rápida y proporciona un conjunto completo de parámetros de configuración para implementar nodos de proceso de Linux.

Para más información sobre las opciones de implementación de clústeres de HPC Pack en Azure, consulte las [opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) en Azure con Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Requisitos previos
* **Suscripción de Azure** : puede usar una suscripción en el servicio Azure Global o Azure China. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.
* **Cuota de núcleos** : tal vez tenga que aumentar la cuota de núcleos, especialmente si decide implementar varios nodos de clúster con tamaños de máquina virtual de múltiples núcleos. Para aumentar una cuota, abra una solicitud de soporte técnico al cliente en línea sin cargo alguno.
* **Distribuciones de Linux** : HPC Pack admite actualmente las siguientes distribuciones de Linux para nodos de proceso. Puede usar versiones de Marketplace de estas distribuciones cuando estén disponibles o proporcionar las suyas.
  
  * **Basado en CentOS**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC y 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8 y 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 para HPC y SLES 12 para HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS y 16.04 LTS
    
    > [!TIP]
    > Para utilizar la red RDMA de Azure con tamaños de máquina virtual compatibles con RDMA, especifique una de las imágenes HPC de SUSE Linux Enterprise Server 12 o basada en CentOS desde Azure Marketplace. Para más información, consulte [Tamaños de máquina virtual de procesos de alto rendimiento](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Requisitos previos adicionales si implementa el clúster mediante el script de implementación de IaaS de HPC Pack:

* **Equipo cliente** : necesita un equipo cliente basado en Windows para ejecutar el script de implementación del clúster.
* **Azure PowerShell** - [instale y configure Azure PowerShell](/powershell/azure/overview) (versión 0.8.10 o posterior) en el equipo cliente.
* **Script de implementación de IaaS de HPC Pac** : descargue y desempaquete la versión más reciente del script desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Puede comprobar la versión del script ejecutando `.\New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.4.1 (o posterior) del script.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Opción de implementación 1. Usar una plantilla de Resource Manager
1. Vaya a la plantilla [Clúster de HPC Pack para cargas de trabajo de Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) en Azure Marketplace y haga clic en **Implementar**.
2. En el Portal de Azure, revise la información y luego haga clic en **Crear**.
   
    ![Creación del portal][portal]
3. En la hoja **Datos básicos** , escriba un nombre para el clúster, que también dará nombre a la máquina virtual del nodo principal. Puede elegir un grupo de recursos existente o crear un grupo para la implementación en una ubicación que esté a su disposición. La ubicación afecta a la disponibilidad de algunos tamaños de VM y otros servicios de Azure (ver [Productos disponibles por región](https://azure.microsoft.com/regions/services/)).
4. En la hoja **Configuración del nodo principal** , para una primera implementación, puede generalmente aceptar la configuración predeterminada. 
   
   > [!NOTE]
   > **Dirección URL de script de configuración posterior** es una configuración opcional para especificar un script de Windows PowerShell disponible públicamente que desea ejecutar en la máquina virtual del nodo principal después de que se ejecute. 
   > 
   > 
5. En la hoja **Configuración del nodo de proceso** , seleccione un nombre de modelo para los nodos, el número y tamaño de los nodos y la distribución de Linux que implementar.
6. En la hoja **Infrastructure settings** (Configuración de infraestructura), escriba los nombres de la red virtual y el dominio de Active Directory, el dominio y las credenciales de administrador de la máquina virtual y un patrón de nomenclatura para las cuentas de almacenamiento.
   
   > [!NOTE]
   > HPC Pack utiliza el dominio de Active Directory para autenticar los usuarios del clúster. 
   > 
   > 
7. Una vez ejecutadas las pruebas de validación y revisados los términos de uso, haga clic en **Comprar**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Opción de implementación 2. Uso del script de implementación de IaaS
A continuación se muestran requisitos previos adicionales para implementar el clúster mediante el script de implementación de IaaS de HPC Pack:

* **Equipo cliente** : necesita un equipo cliente basado en Windows para ejecutar el script de implementación del clúster.
* **Azure PowerShell** - [instale y configure Azure PowerShell](/powershell/azure/overview) (versión 0.8.10 o posterior) en el equipo cliente.
* **Script de implementación de IaaS de HPC Pac** : descargue y desempaquete la versión más reciente del script desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Puede comprobar la versión del script ejecutando `.\New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.4.1 (o posterior) del script.

**Archivo de configuración XML**

El script de implementación de IaaS de HPC Pack usa un archivo de configuración XML como entrada que describe el clúster HPC. En el siguiente archivo de configuración de ejemplo se especifica un pequeño clúster que consta de un nodo principal de HPC Pack y dos nodos de proceso de CentOS 7.0 Linux de tamaño A7. 

Modifique el archivo según sea necesario para su entorno y la configuración de clúster deseada y guárdelo con un nombre como HPCDemoConfig.xml. Por ejemplo, debe proporcionar el nombre de la suscripción y un nombre de cuenta de almacenamiento único, así como el nombre del servicio en la nube. Además, para los nodos de proceso, puede elegir una imagen de Linux compatible diferente. Para obtener más información sobre los elementos en el archivo de configuración, vea el archivo Manual.rtf en la carpeta de script y [Creación de un clúster de HPC de Windows con el script de implementación de IaaS de HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Para ejecutar el script de implementación de HPC Pack IaaS**

1. Abra Windows PowerShell en el equipo cliente como administrador.
2. Cambie el directorio a la carpeta donde está instalado el script (E:\IaaSClusterScript en este ejemplo).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Ejecute el comando siguiente para implementar el clúster de HPC Pack. En este ejemplo se supone que el archivo de configuración se encuentra en E:\HPCDemoConfig.xml.
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Como **AdminPassword** no se ha especificado en el comando anterior, se le pedirá que escriba la contraseña del usuario *MyAdminName*.
   
    b. A continuación, el script empieza a validar el archivo de configuración. Puede tardar varios minutos dependiendo de la conexión de red.
   
    ![Validación][validate]
   
    c. Una vez superadas las validaciones, el script enumera los recursos de clúster que se van a crear. Escriba *Y* para continuar.
   
    ![Recursos][resources]
   
    d. El script empieza a implementar el clúster de HPC Pack y completa la configuración sin más pasos manuales. El script puede tardar varios minutos en ejecutarse.
   
    ![Implementación][deploy]
   
   > [!NOTE]
   > En este ejemplo, el script genera un archivo de registro automáticamente porque el parámetro **-LogFile** no está especificado. Los registros no se escriben en tiempo real, pero se recopilan al final de la validación y la implementación. Si se detiene el proceso de PowerShell mientras se ejecuta el script, se pierden algunos registros.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Conexión al nodo principal
Después de implementar el clúster de HPC Pack en Azure, [conéctese mediante el Escritorio remoto](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a la máquina virtual del nodo principal con las credenciales de dominio proporcionadas cuando implementó el clúster (por ejemplo, *hpc\\clusteradmin*). Usted administra el clúster desde el nodo principal.

En el nodo principal, inicie el Administrador de clústeres de HPC para comprobar el estado del clúster de HPC Pack. Puede administrar y supervisar los nodos de proceso de Linux del mismo modo que trabaja con nodos de proceso de Windows. Por ejemplo, verá los nodos de Linux en **Administración de recursos** (estos nodos se implementan con la plantilla **LinuxNode**).

![Administración de nodos][management]

También verá los nodos de Linux en la vista **Mapa térmico** .

![Mapa térmico][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Cómo mover los datos en un clúster con nodos de Linux
Hay varias opciones para mover datos entre los nodos de Linux y el nodo principal de Windows del clúster. A continuación se muestran tres métodos comunes, descritos con más detalle en las secciones siguientes:

* **Archivo de Azure** : expone un recurso compartido de archivos SMB administrado para almacenar archivos de datos en Almacenamiento de Azure. Los nodos de Windows y de Linux pueden montar un recurso compartido de archivo de Azure como una unidad o carpeta al mismo tiempo, incluso si se implementan en diferentes redes virtuales.
* **Recurso compartido de SMB del nodo principal** : monta una carpeta compartida de Windows estándar del nodo principal en los nodos de Linux.
* **Servidor NFS del nodo principal**: proporciona una solución de uso compartido de archivos para un entorno mixto de Windows y Linux.

### <a name="azure-file-storage"></a>Almacenamiento de archivos de Azure
El servicio [Archivo de Azure](https://azure.microsoft.com/services/storage/files/) expone recursos compartidos de archivos mediante el protocolo SMB 2.1 estándar. Las VM y los servicios en la nube de Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos. 

Para obtener pasos detallados para crear un recurso compartido de archivos de Azure y montarlo en el nodo principal, vea [Introducción a Almacenamiento de archivos de Azure en Windows](../../../storage/files/storage-how-to-use-files-windows.md). Para montar el recurso compartido de archivos de Azure en los nodos de Linux, vea [Uso de almacenamiento de Azure File Storage con Linux](../../../storage/files/storage-how-to-use-files-linux.md). Para establecer conexiones persistentes, consulte [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

En el siguiente ejemplo, cree un recurso compartido de Archivos de Azure en una cuenta de almacenamiento. Para montar el recurso compartido en el nodo principal, abra un símbolo del sistema y escriba los siguientes comandos:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

En este ejemplo, allvhdsje es el nombre de la cuenta de almacenamiento, storageaccountkey es la clave de la cuenta de almacenamiento y rdma es el nombre del recurso compartido de Archivo de Azure. El recurso compartido de archivos de Azure se montará como Z: en el nodo principal.

Para montar el recurso compartido de archivos de Azure en los nodos de Linux, ejecute un comando **clusrun** en el nodo principal. **[ClusRun](https://technet.microsoft.com/library/cc947685.aspx)** es una herramienta de HPC Pack útil para llevar a cabo tareas administrativas en varios nodos. (consulte también [ClusRun para nodos de Linux](#Clusrun-for-Linux-nodes) en este artículo).

Abra una ventana de Windows PowerShell y escriba los comandos siguientes:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

El primer comando crea una carpeta denominada /rdma en todos los nodos en el grupo LinuxNodes. El segundo comando monta el recurso compartido de Archivo de Azure allvhdsjw.file.core.windows.net/rdma en la carpeta /rdma con dir y bits de modo de archivo establecidos en 777. En el segundo comando, allvhdsje es el nombre de la cuenta de almacenamiento y storageaccountkey es la clave de la cuenta de almacenamiento.

> [!NOTE]
> El símbolo “\`” en el segundo comando es un símbolo de escape de PowerShell. “\`,” significa que “,” (carácter de coma) forma parte del comando.
> 
> 

### <a name="head-node-share"></a>Recurso compartido del nodo principal
Si lo prefiere, puede montar una carpeta compartida del nodo principal en los nodos de Linux. Un recurso compartido es la manera más sencilla de compartir archivos, pero el nodo principal y todos los nodos de Linux deben implementarse en la misma red virtual. A continuación se muestran los pasos que se deben seguir.

1. Cree una carpeta en el nodo principal y compártala con Todos con permisos de lectura y escritura. Por ejemplo, comparta D:\OpenFOAM en el nodo principal como \\CentOS7RDMA-HN\OpenFOAM. En el ejemplo, CentOS7RDMA-HN es el nombre de host del nodo principal.
   
    ![Permisos del recurso compartido de archivos][fileshareperms]
   
    ![Uso compartido de archivos][filesharing]
2. Abra una ventana de Windows PowerShell y ejecute los comandos siguientes:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

El primer comando crea una carpeta denominada /openfoam en todos los nodos del grupo LinuxNodes. El segundo comando monta la carpeta compartida //CentOS7RDMA-HN/OpenFOAM en la carpeta con dir y bits de modo de archivo establecidos en 777. El nombre de usuario y la contraseña del comando deben ser el nombre de usuario y la contraseña de un usuario de clúster en el nodo principal. Consulte [Adición o eliminación de usuarios de clúster](https://technet.microsoft.com/library/ff919330.aspx).

> [!NOTE]
> El símbolo “\`” en el segundo comando es un símbolo de escape de PowerShell. “\`,” significa que “,” (carácter de coma) forma parte del comando.
> 
> 

### <a name="nfs-server"></a>Servidor NFS
El servicio NFS le permite compartir y migrar archivos entre equipos que ejecutan el sistema operativo Windows Server 2012 con el protocolo SMB y equipos basados en Linux con el protocolo NFS. El servidor NFS y todos los demás nodos tienen que implementarse en la misma red virtual. Proporciona mayor compatibilidad con los nodos de Linux en comparación con un recurso compartido de SMB. Por ejemplo, admite vínculos de archivo.

1. Para instalar y configurar un servidor NFS, siga los pasos indicados en [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Por ejemplo, cree un recurso compartido de NFS denominado nfs con las siguientes propiedades:
   
    ![Autorización de NFS][nfsauth]
   
    ![Permisos del recurso compartido de NFS][nfsshare]
   
    ![Permisos de NTFS de NFS][nfsperm]
   
    ![Propiedades de administración de NFS][nfsmanage]
2. Abra una ventana de Windows PowerShell y ejecute los comandos siguientes:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   El primer comando crea una carpeta denominada /nfsshared en todos los nodos del grupo LinuxNodes. El segundo comando monta el recurso compartido de NFS CentOS7RDMA-HN:/nfs en la carpeta. Aquí  CentOS7RDMA-HN:/nfs es la ruta de acceso remoto al recurso compartido de NFS.

## <a name="how-to-submit-jobs"></a>Envío de trabajos
Hay varias formas de enviar trabajos al clúster de HPC Pack:

* Administrador de clústeres HPC o GUI del Administrador de trabajos de HPC
* Portal web de HPC
* API de REST

El envío de trabajos al clúster en Azure a través de herramientas de GUI de HPC Pack y el portal web de HPC es igual que para los nodos de proceso de Windows. Consulte [Administrador de trabajos de HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) y [Envío de trabajos desde un equipo cliente local](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para enviar trabajos mediante la API de REST, consulte [Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Para enviar trabajos desde un cliente Linux, consulte también el ejemplo de Python en el [SDK de HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>ClusRun para nodos de Linux
La herramienta [clusrun](https://technet.microsoft.com/library/cc947685.aspx) de HPC Pack se puede usar para ejecutar comandos en los nodos de Linux mediante un símbolo del sistema o el Administrador de clústeres HPC. A continuación se muestran algunos ejemplos básicos.

* Mostrar los nombres de usuario actuales de todos los nodos del clúster.
  
    ```command
    clusrun whoami
    ```
* Instale la herramienta de depuración **gdb** con **yum** en todos los nodos del grupo linuxnodes y reinícielos después de 10 minutos.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Cree un script de shell que muestre cada número de 1 a 10 durante un segundo en cada nodo del clúster, ejecútelo y muestre la salida de los nodos inmediatamente.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Tal vez tenga que usar ciertos caracteres de escape en los comandos de **clusrun** . Como se muestra en este ejemplo, utilice ^ en un símbolo del sistema para anular el símbolo ">".
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Pruebe a escalar verticalmente el clúster a un mayor número de nodos o ejecute una carga de trabajo de Linux en el clúster. Para ver un ejemplo, consulte [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](hpcpack-cluster-namd.md).
* Pruebe un clúster con [máquinas virtuales de proceso intensivo compatibles con RDMA](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ejecutar cargas de trabajo MPI. Para ver un ejemplo, consulte [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](hpcpack-cluster-openfoam.md).
* Si está interesado en trabajar con nodos de Linux en un clúster de HPC Pack local, consulte la [guía TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
