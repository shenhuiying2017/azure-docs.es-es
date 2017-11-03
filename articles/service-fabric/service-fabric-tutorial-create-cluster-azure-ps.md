---
title: "Creación de un clúster de Service Fabric en Azure | Microsoft Docs"
description: "Aprenda a crear un clúster de Service Fabric de Windows o Linux en Azure mediante PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Creación de un clúster seguro en Azure mediante PowerShell
Este es el primer de una serie de tutoriales que le muestran cómo mover una aplicación .NET a la nube mediante clústeres de Service Fabric y contenedores. En los pasos siguientes aprenderá a crear un clúster de Service Fabric (Windows o Linux) que se ejecuta en Azure. Cuando haya terminado, tendrá un clúster seguro que se ejecuta en la nube en el que puede implementar aplicaciones.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale el [SDK de Service Fabric](service-fabric-get-started.md).
- Instale la [versión 4.1 o superior del módulo de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (si es necesario, [instale Azure PowerShell](/powershell/azure/overview) o [actualice a una versión más reciente](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)).


# <a name="create-a-service-fabric-cluster"></a>Creación de un clúster de Service Fabric

Este script crea un clúster de Service Fabric en versión preliminar de un único nodo. protegido mediante un certificado autofirmado que se crea con el clúster y que, luego, se coloca en un almacén de claves. Los clústeres de un único nodo no se pueden escalar más allá de una máquina virtual y los clústeres de versión preliminar no se pueden actualizar a versiones más recientes.

Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).
Para más información sobre la creación de clústeres de Service Fabric, consulte el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Abra una consola de PowerShell, inicie sesión en Azure y seleccione la suscripción en la que desea implementar el clúster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Parámetros del clúster

   Este script usa los siguientes parámetros y conceptos: Personalice los parámetros para ajustarlos a sus necesidades.

   | Parámetro       | Descripción | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | Ubicación | Región de Azure en la que se implementa el clúster. | *Por ejemplo, westeurope, eastasia, eastus* |
   | Nombre     | Nombre del clúster que desea crear. El nombre debe tener entre 4 y 23 caracteres y solo puede contener letras minúsculas, números y guiones. | *Por ejemplo, bobs-sfpreviewcluster* |
   | ResourceGroupName   | Nombre del grupo de recursos en el que se crea el clúster. | *Por ejemplo, myresourcegroup*. |
   | VmSku  | SKU de la máquina virtual que se usa para los nodos. | *Cualquier SKU de máquina virtual válida* |
   | SO  | SO de la máquina virtual que se usa para los nodos. | *Cualquier SO de máquina virtual válido* |
   | keyVaultName | Nombre del nuevo valor de KeyVault para asociar con el clúster. | *Por ejemplo, mykeyvault*. |
   | ClusterSize | El número de máquinas virtuales en el clúster (puede ser 1 o 3−99).| *Para un clúster en versión preliminar, especifique solo una máquina virtual*. |
   | CertificateSubjectName | El nombre del sujeto del certificado que se va a crear. | *Sigue el formato <name>.<location>.cloudapp.azure.com* |

## <a name="default-parameter-values"></a>Valores de parámetros predeterminados
La configuración de **máquina virtual** es opcional. Si no lo especifica, el nombre de usuario administrador es de forma predeterminada "vmadmin" y PowerShell le solicita una contraseña de máquina virtual antes de crear el clúster.

Los **puertos** serán de forma predeterminada 80 y 8081. Para especificar puertos adicionales, siga la guía para los [puertos de clústeres de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

Los **diagnósticos** están habilitados de forma predeterminada.

El **servicio DNS** no está habilitado de forma predeterminada.

El **proxy inverso** tampoco está habilitado de forma predeterminada.

## <a name="create-the-cluster-with-your-parameters"></a>Creación del clúster con los parámetros

Cuando haya decidido cuáles son los parámetros que se ajustan a sus requisitos, ejecute el siguiente comando para generar un clúster de Service Fabric seguro y su certificado.

Puede modificar este script para incluir parámetros adicionales. Para más información sobre los parámetros para la creación del clúster consulte el cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Antes de ejecutar este comando, debe crear una carpeta a la que transferir el certificado del clúster.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

El proceso de creación puede tardar varios minutos. Una vez finalizada la configuración, se envía la información sobre el clúster creado en Azure. También se copia el certificado de clúster en el directorio -CertificateOutputFolder de la ruta de acceso especificada mediante este parámetro.

Anote la dirección URL de **ManagementEndpoint** del clúster, que puede ser parecida a la siguiente: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="import-the-certificate"></a>Importación del certificado

Cuando el clúster se haya creado correctamente, ejecute el comando siguiente para asegurarse de que puede usar el certificado autofirmado.

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

Este comando instala el certificado en el usuario actual de la máquina.  Este certificado se necesita para acceder a Service Fabric Explorer y ver el estado del clúster.


## <a name="view-your-cluster-optional"></a>Visualización del clúster (opcional)

Cuando se hayan importado el clúster y el certificado, puede conectarse al clúster y ver su estado. Existen dos maneras de conectarse: Service Fabric Explorer o PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Para ver el estado del clúster, abra Service Fabric Explorer. Para ello, vaya a la dirección URL de **ManagementEndpoint** del clúster mediante un explorador web y seleccione el certificado que guardó en la máquina.

>[!NOTE]
>Al abrir Service Fabric Explorer, verá un error de certificado, ya que está usando un certificado autofirmado. En Edge, debe hacer clic en *Detalles* y, luego, en el vínculo *Acceder a la página web*. En Chrome, debe hacer clic en *Avanzadas* y, luego, en el vínculo *continuar*.

### <a name="powershell"></a>PowerShell

El módulo PowerShell de **Service Fabric** proporciona muchos cmdlets para administrar clústeres, aplicaciones y servicios de Service Fabric.  Use el cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para conectarse al clúster seguro. Los detalles de la huella digital de certificado y del punto de conexión se encuentran en la salida de un paso anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Otra manera de comprobar que está conectado y que el estado del clúster es correcto es usar el cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a crear un clúster de Service Fabric seguro en Azure mediante PowerShell.

A continuación, avance hasta el siguiente tutorial para aprender a implementar una aplicación existente.
> [!div class="nextstepaction"]
> [Implementación de una aplicación .NET con Docker Compose](service-fabric-host-app-in-a-container.md)

---
