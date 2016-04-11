<properties
 pageTitle="Uso de máquinas virtuales de proceso de Linux en un clúster de HPC Pack | Microsoft Azure"
 description="Aprenda a crear y usar un clúster de HPC Pack en Azure para cargas de trabajo de informática de alto rendimiento (HPC) de Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="03/21/2016"
 ms.author="danlep"/>

# Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure

Configure un clúster de Microsoft HPC Pack en Azure que contenga un nodo principal en el que se ejecuta Windows Server y varios nodos de proceso en los que se ejecuta una distribución de Linux compatible. Explore las opciones para mover datos entre los nodos de Linux y el nodo principal de Windows del clúster. Aprenda a enviar trabajos de HPC de Linux al clúster.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


En un nivel alto, el diagrama siguiente muestra el clúster de HPC Pack que creará y con el que trabajará.

![Clúster de HPC con nodos de Linux][scenario]

>[AZURE.TIP]Si está interesado en trabajar con nodos de Linux en un clúster de HPC Pack local, consulte la [guía TechNet](https://technet.microsoft.com/library/mt595803.aspx).

## Implementación de un clúster de HPC Pack con nodos de proceso de Linux

A continuación se muestran dos maneras recomendadas de crear un clúster de HPC Pack en Azure con nodos de proceso de Linux:

* **Plantilla de Azure Resource Manager**: use una plantilla de Azure Marketplace o una plantilla de la galería de la comunidad para automatizar la creación del clúster en el modelo de implementación de Resource Manager. Por ejemplo, la plantilla [Clúster de HPC Pack para cargas de trabajo de Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) en Azure Marketplace crea un clúster de HPC Pack completo, incluida la red virtual de Azure, un nodo principal con bases de datos SQL locales, un bosque de Dominio de Active Directory (con el nodo principal configurado como controlador de dominio) y un número de nodos de proceso que ejecuta una distribución de Linux compatible.

* **Script de PowerShell**: use el [script de implementación de IaaS de Microsoft HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) (**New-HpcIaaSCluster.ps1**) para automatizar la implementación del clúster en el modelo de implementación clásica. Este script de PowerShell de Azure usa una imagen de VM de HPC Pack en Azure Marketplace para una implementación rápida y proporciona un conjunto completo de parámetros de configuración para que la implementación sea sencilla y flexible. El script implementa la red virtual de Azure, las cuentas de almacenamiento, los servicios en la nube, el controlador de dominio, el servidor de base de datos de SQL Server independiente opcional, el nodo principal del clúster, los nodos de proceso, los nodos de agente, los nodos de PaaS de Azure ("ráfaga") y los nodos de proceso de Linux.

Para obtener información general sobre las opciones de implementación de clústeres de HPC Pack, consulte [Getting started guide for HPC Pack 2012 R2 and HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx) y [Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) en Azure con Microsoft HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

### Requisitos previos

* **Suscripción de Azure**: puede usar una suscripción en el servicio Azure Global o Azure China. En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos.

* **Cuota de núcleos**: tal vez tenga que aumentar la cuota de núcleos, especialmente si decide implementar varios nodos de clúster con tamaños de máquina virtual de múltiples núcleos. Para aumentar una cuota, [abra una solicitud de soporte técnico al cliente en línea](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo alguno.

* **Distribuciones de Linux**: actualmente, HPC Pack admite las siguientes distribuciones de Linux para nodos de proceso: Ubuntu Server 14.04, CentOS 6.6 o 7.0, Red Hat Enterprise Linux 6.7 o 7.2, y SUSE Linux Enterprise Server 12. Puede usar versiones de Marketplace de estas distribuciones cuando estén disponibles o proporcionar las suyas.

    >[AZURE.TIP]Para utilizar la red RDMA de Azure con máquinas virtuales de nodo de proceso de tamaño A8 y A9, especifique SUSE Linux Enterprise Server 12: optimizado para la imagen de proceso de alto rendimiento de Marketplace. Debe instalar y configurar una biblioteca MPI compatible en los nodos después de implementar el clúster según necesite la aplicación. Para ver un ejemplo, consulte [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

Requisitos previos adicionales si implementa el clúster mediante el script de implementación de IaaS de HPC Pack:

* **Equipo cliente**: necesitará un equipo cliente basado en Windows para ejecutar el script de implementación del clúster.

* **Azure PowerShell**: [instale y configure Azure PowerShell](../powershell-install-configure.md) (versión 0.8.10 o posterior) en el equipo cliente.

* **Script de implementación de IaaS de HPC Pac **: descargue y desempaquete la versión más reciente del script desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Puede comprobar la versión del script ejecutando `New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.4.0 o posterior del script.

### Escenario de implementación 1. Uso de una plantilla de Marketplace

1. Vaya a la plantilla [Clúster de HPC Pack para cargas de trabajo de Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) en Azure Marketplace y haga clic en **Implementar**.

2. En el Portal de Azure, revise la información y luego haga clic en **Crear**.

    ![Creación del portal][portal]

3. En la hoja **Aspectos básicos**, escriba un nombre para el clúster, que también dará nombre a la máquina virtual del nodo principal. Puede elegir un grupo de recursos existente o crear un nuevo grupo para la implementación.

4. En la hoja **Configuración del nodo principal**, para una primera implementación, puede generalmente aceptar la configuración predeterminada.

    >[AZURE.NOTE]**Dirección URL de script de configuración posterior** es una configuración opcional para especificar un script de Windows PowerShell disponible públicamente que desea ejecutar en la máquina virtual del nodo principal después de que se ejecute.
    
5. En la hoja **Configuración del nodo de proceso**, seleccione un nombre de modelo para los nodos, el número y tamaño de los nodos y la imagen de la distribución de Linux que implementar.

6. En la hoja **Configuración de infraestructura**, escriba los nombres de la red virtual y el dominio de Active Directory para el clúster, el dominio y las credenciales de administrador de la máquina virtual y un patrón de nomenclatura para las cuentas de almacenamiento necesarias para el clúster.

    >[AZURE.NOTE]HPC Pack utiliza el dominio de Active Directory para autenticar los usuarios del clúster.

7. Después de ejecutar las pruebas de validación y que esté preparado para la implementación, haga clic en **Crear**.


### Escenario de implementación 2. Uso del script de implementación de IaaS

El script de implementación de HPC Pack IaaS usa un archivo de configuración XML como entrada que describe la infraestructura del clúster HPC. En el siguiente archivo de configuración de ejemplo se implementa un pequeño clúster que consta de un nodo principal y dos nodos de proceso de CentOS 7 Linux de tamaño A7. Modifique el archivo según sea necesario para su entorno y la configuración de clúster deseada. Para obtener más información sobre los elementos en el archivo de configuración, vea el archivo Manual.rtf en la carpeta de script y [Creación de un clúster de HPC de Windows con el script de implementación de IaaS de HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

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
  <VMSize>A4</VMSize>
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

1. Abra la consola de PowerShell en el equipo cliente como administrador.

2. Cambie el directorio a la carpeta de script (E:\\IaaSClusterScript en este ejemplo).

    ```
    cd E:\IaaSClusterScript
    ```

3. Ejecute el comando siguiente para implementar el clúster de HPC Pack. En este ejemplo se supone que el archivo de configuración se encuentra en E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    El script genera un archivo de registro automáticamente porque el parámetro **-LogFile** no está especificado. Los registros no se escriben en tiempo real, pero se recopilan al final de la validación y la implementación. Si se detiene el proceso de PowerShell mientras se ejecuta el script, se perderán algunos registros.

    a. Como **AdminPassword** no se ha especificado en el comando anterior, se le pedirá que escriba la contraseña del usuario *MyAdminName*.

    b. A continuación, el script empieza a validar el archivo de configuración. Tarda desde decenas de segundos hasta varios minutos según la conexión de red.

    ![Validación][validate]

    c. Una vez pasadas las validaciones, el script enumera los recursos que se crearán para el clúster HPC. Escriba *Y* para continuar.

    ![Recursos][resources]

    d. El script empieza a implementar el clúster de HPC Pack y completa la configuración sin más pasos manuales. Esto puede tardar varios minutos.

    ![Implementación][deploy]

## Conexión al nodo principal

Después de que la implementación finalice correctamente, [conéctese mediante el Escritorio remoto al nodo principal](virtual-machines-windows-connect-logon.md) con las credenciales de dominio proporcionadas cuando implementó el clúster (por ejemplo, *hpc\\clusteradmin*).

En el nodo principal, inicie el Administrador de clústeres de HPC para comprobar el estado del clúster de HPC Pack. Puede administrar y supervisar los nodos de proceso de Linux del mismo modo que trabaja con nodos de proceso de Windows. Por ejemplo, verá los nodos de Linux en **Administración de recursos** (estos nodos se implementan con la plantilla **LinuxNode**).

![Administración de nodos][management]

También verá los nodos de Linux en la vista **Mapa térmico**.

![Mapa térmico][heatmap]

## Cómo mover los datos en un clúster con nodos de Linux

Hay varias opciones para mover datos entre los nodos de Linux y el nodo principal de Windows del clúster. A continuación se muestran tres métodos comunes.

* **Archivo de Azure**: expone un recurso compartido de archivos SMB administrado para almacenar archivos de datos en Almacenamiento de Azure. Los nodos de Windows y los nodos de Linux pueden montar un recurso compartido de archivo de Azure como una unidad o carpeta al mismo tiempo incluso si se implementan en diferentes redes virtuales.

* **Recurso compartido de SMB del nodo principal**: monta una carpeta compartida de Windows estándar del nodo principal en los nodos de Linux.

* **Servidor NFS del nodo principal**: proporciona una solución de uso compartido de archivos para un entorno mixto de Windows y Linux.

### Almacenamiento de archivos de Azure

El servicio [Archivo de Azure](https://azure.microsoft.com/services/storage/files/) expone recursos compartidos de archivos mediante el protocolo SMB 2.1 estándar. Las VM y los servicios en la nube de Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados, y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos.

Para obtener pasos detallados para crear un recurso compartido de archivos de Azure y montarlo en el nodo principal, vea [Introducción a Almacenamiento de archivos de Azure en Windows](../storage/storage-dotnet-how-to-use-files.md). Para montar el recurso compartido de archivos de Azure en los nodos de Linux, vea [Cómo utilizar el almacenamiento de archivos de Azure con Linux](../storage/storage-how-to-use-files-linux.md). Para establecer conexiones persistentes, consulte [Persisting connections to Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

En este ejemplo, creamos un recurso compartido de Archivo de Azure denominado rdma en nuestra cuenta de almacenamiento allvhdsje. Para montar el recurso compartido en el nodo principal, abra un símbolo del sistema y escriba los siguientes comandos:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

En este ejemplo, allvhdsje es el nombre de la cuenta de almacenamiento, storageaccountkey es la clave de la cuenta de almacenamiento y rdma es el nombre del recurso compartido de Archivo de Azure. El recurso compartido de Archivo de Azure se montará en Z: en el nodo principal.

Para montar el recurso compartido de Archivo de Azure en los nodos de Linux, ejecute un comando **clusrun** en el nodo principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** es una herramienta de HPC Pack útil para llevar a cabo tareas administrativas en varios nodos (vea también [CLusrun para los nodos de Linux](#CLusrun-for-Linux-nodes)en este artículo).

Abra una ventana de Windows PowerShell y escriba los comandos siguientes.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

El primer comando crea una carpeta denominada /rdma en todos los nodos en el grupo LinuxNodes. El segundo comando monta el recurso compartido de Archivo de Azure allvhdsjw.file.core.windows.net/rdma en la carpeta /rdma con dir y bits de modo de archivo establecidos en 777. En el segundo comando, allvhdsje es el nombre de la cuenta de almacenamiento y storageaccountkey es la clave de la cuenta de almacenamiento.

>[AZURE.NOTE]El símbolo "`" del segundo comando es un símbolo de escape de PowerShell. "`," significa que "," (coma) forma parte del comando.

### Recurso compartido del nodo principal

Si lo prefiere, puede montar una carpeta compartida del nodo principal en los nodos de Linux. Esta es la manera más sencilla de compartir archivos, pero el nodo principal y todos los nodos de Linux deben implementarse en la misma red virtual. A continuación se muestran los pasos que se deben seguir.

1. Cree una carpeta en el nodo principal y compártala con Todos con permisos de lectura y escritura. Por ejemplo, comparta D:\\OpenFOAM en el nodo principal como \\CentOS7RDMA-HN\\OpenFOAM. En el ejemplo, CentOS7RDMA-HN es el nombre de host del nodo principal.

    ![Permisos del recurso compartido de archivos][fileshareperms]

    ![Uso compartido de archivos][filesharing]

2. Abra una ventana de Windows PowerShell y ejecute los siguientes comandos para montar la carpeta compartida.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

El primer comando crea una carpeta denominada /openfoam en todos los nodos del grupo LinuxNodes. El segundo comando monta la carpeta compartida //CentOS7RDMA-HN/OpenFOAM en la carpeta con dir y bits de modo de archivo establecidos en 777. El nombre de usuario y la contraseña del comando deben ser el nombre de usuario y la contraseña de un usuario de clúster en el nodo principal. Consulte [Adición o eliminación de usuarios de clúster](https://technet.microsoft.com/library/ff919330.aspx).

>[AZURE.NOTE]El símbolo "`" del segundo comando es un símbolo de escape de PowerShell. "`," significa que "," (coma) forma parte del comando.


### Servidor NFS

El servicio NFS le permite compartir y migrar archivos entre equipos que ejecutan el sistema operativo Windows Server 2012 con el protocolo SMB y equipos basados en Linux con el protocolo NFS. El servidor NFS y todos los demás nodos tienen que implementarse en la misma red virtual. Proporciona mayor compatibilidad con los nodos de Linux en comparación con un recurso compartido de SMB; por ejemplo, es compatible con los vínculos de archivos.

1. Para instalar y configurar un servidor NFS, siga los pasos indicados en [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    Por ejemplo, cree un recurso compartido de NFS denominado nfs con las siguientes propiedades.

    ![Autorización de NFS][nfsauth]

    ![Permisos del recurso compartido de NFS][nfsshare]

    ![Permisos de NTFS de NFS][nfsperm]

    ![Propiedades de administración de NFS][nfsmanage]

2. Abra una ventana de Windows PowerShell y ejecute los siguientes comandos para montar el recurso compartido de NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  El primer comando crea una carpeta denominada /nfsshared en todos los nodos del grupo LinuxNodes. El segundo comando monta el recurso compartido de NFS CentOS7RDMA-HN:/nfs en la carpeta. Aquí CentOS7RDMA-HN:/nfs es la ruta de acceso remoto al recurso compartido de NFS.

## Envío de trabajos
Hay varias formas de enviar trabajos al clúster de HPC Pack:

* Administrador de clústeres HPC o GUI del Administrador de trabajos de HPC

* Portal web de HPC

* API de REST

El envío de trabajos al clúster en Azure a través de herramientas de GUI de HPC Pack y el portal web de HPC es igual que para los nodos de proceso de Windows. Consulte [Administrador de trabajos de HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) y [Envío de trabajos desde un equipo cliente local](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

Para enviar trabajos mediante la API de REST, consulte [Creating and Submitting Jobs by Using the REST API in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Consulte también el ejemplo de Python en el [SDK de HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) para enviar trabajos desde un cliente Linux.

## ClusRun para nodos de Linux

La herramienta **clusrun** de HPC Pack se puede usar para ejecutar comandos en los nodos de Linux mediante un símbolo del sistema o el Administrador de clústeres HPC. A continuación se muestran algunos ejemplos básicos.

* Mostrar los nombres de usuario actuales de todos los nodos del clúster.

    ```
    > clusrun whoami
    ```

* Instale la herramienta de depuración **gdb** con **yum** en todos los nodos del grupo linuxnodes y reinícielos después de 10 minutos.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Cree un script de shell que muestre cada número de 1 a 10 durante un segundo en cada nodo del clúster, ejecútelo y muestre la salida de los nodos inmediatamente.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] Tal vez tenga que usar ciertos caracteres de escape en los comandos de **clusrun**. Como se muestra en este ejemplo, utilice ^ en un símbolo del sistema para anular el símbolo ">".

## Pasos siguientes

* Pruebe a escalar verticalmente el clúster a un mayor número de nodos o ejecute una carga de trabajo de Linux en el clúster. Para ver un ejemplo, consulte [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* Pruebe un clúster con nodos de proceso de tamaño [A8 o A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) para ejecutar cargas de trabajo MPI. Para ver un ejemplo, consulte [Ejecución de OpenFoam con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[portal]: ./media/virtual-machines-linux-classic-hpcpack-cluster/portal.png
[validate]: ./media/virtual-machines-linux-classic-hpcpack-cluster/validate.png
[resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster/resources.png
[deploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster/deploy.png
[management]: ./media/virtual-machines-linux-classic-hpcpack-cluster/management.png
[heatmap]: ./media/virtual-machines-linux-classic-hpcpack-cluster/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsmanage.png

<!---HONumber=AcomDC_0330_2016-->