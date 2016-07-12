<properties
   pageTitle="Script de PowerShell para implementar el clúster de HPC Pack de Linux | Microsoft Azure"
   description="Ejecución de un script de Windows PowerShell para implementar un clúster de HPC Pack de Linux completo en servicios de infraestructura de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Creación de un clúster de informática de alto rendimiento (HPC) en máquinas virtuales Linux con el script de implementación de HPC Pack IaaS

Ejecute el script de PowerShell de implementación de HPC Pack IaaS en un equipo cliente para implementar un clúster de HPC Pack completo en servicios de infraestructura (IaaS) de Azure. Si desea implementar un clúster de HPC Pack en cargas de trabajo de Azure para Windows, consulte [Creación de un clúster de informática de alto rendimiento (HPC) en máquinas virtuales de Windows con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## Archivos de configuración de ejemplo

### Ejemplo 1

El archivo de configuración siguiente crea un nuevo bosque de dominio e implementa un clúster de HPC Pack que tiene un nodo principal con bases de datos locales y 20 nodos de proceso de Linux. Todos los servicios en la nube se crean directamente en la ubicación de Este de Asia. Los nodos de proceso de Linux se crean en cuatro servicios en la nube y cuatro cuentas de almacenamiento (es decir, _MyLnxCN-0001_ a _MyLnxCN-0005_ en _MyLnxCNService01_ y _mylnxstorage01_, _MyLnxCN-0006_ a _MyLnxCN-0010_ en _MyLnxCNService02_ y _mylnxstorage02_, _MyLnxCN-0011_ a _MyLnxCN-0015_ en _MyLnxCNService03_ y _mylnxstorage03_, y _MyLnxCN-0016_ a _MyLnxCN-0020_ en _MyLnxCNService04_ y _mylnxstorage04_). Los nodos de proceso se crean a partir de una imagen de Linux de CentOS OpenLogic versión 7.0.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## Solución de problemas

* **Error "La red virtual no existe"**: si ejecuta el script de implementación de HPC Pack IaaS para implementar varios clústeres en Azure simultáneamente con una única suscripción, puede producirse un error de "La red virtual *Nombre\_red\_virtual* no existe" en una implementación o varias. Si se produce este error, vuelva a ejecutar el script para la implementación en la que ocurrió el error.

* **Problemas de acceso a Internet desde la red virtual de Azure**: si crea un clúster de HPC Pack con un nuevo controlador de dominio mediante el script de implementación o promueve manualmente una máquina virtual del nodo principal a un controlador de dominio, puede experimentar problemas al conectar las máquinas virtuales de la red virtual de Azure a Internet. Esto puede ocurrir si se configura automáticamente un servidor de reenviador DNS en el controlador de dominio y este servidor de reenviador DNS no se resuelve correctamente.

    Para evitar este problema, inicie sesión en el controlador de dominio y, o bien, quite la configuración de reenviador, o bien, configure un servidor de reenviador DNS válida. Para ello, en Administrador de servidores, haga clic **Herramientas** > **DNS** para abrir el Administrador de DNS y, a continuación, haga doble clic en **Reenviadores**.
    
## Pasos siguientes

* Para ver los tutoriales que usan el script para crear un clúster y ejecutar una carga de trabajo de HPC de Linux, consulte [Ejecución de NAMD con Microsoft HPC Pack en nodos de ejecución de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md) o [Ejecución de OpenFOAM con Microsoft HPC Pack en nodos de ejecución de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* También puede usar una plantilla del Administrador de recursos de Azure para implementar un clúster de HPC Pack. Si desea consultar un ejemplo, consulte [Create an HPC cluster with Linux compute nodes](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) (Creación de un clúster de HPC con nodos de proceso de Linux).

<!---HONumber=AcomDC_0629_2016-->