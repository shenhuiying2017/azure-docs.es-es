---
title: "Creación de un clúster de Azure Service Fabric en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo implementar un clúster de Azure Service Fabric con Linux en una instancia existente de Azure Virtual Network mediante PowerShell."
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
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: b06d0196f1f911f2f6cf87242d70455ba22b1f88
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implementación de un clúster de Windows de Service Fabric en una red virtual de Azure
Este tutorial es la primera parte de una serie. Obtendrá información sobre cómo implementar un clúster de Azure Service Fabric con Linux en una instancia existente de Azure Virtual Network (VNET) y en una subred mediante PowerShell. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones.  Para crear un clúster de Linux con la CLI de Azure, consulte [Create a secure Linux cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Creación de un clúster de Linux seguro en Azure).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual en Azure mediante PowerShell
> * Creación de un almacén de claves y carga de un certificado
> * Crear un clúster de Service Fabric seguro en Azure PowerShell
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Crear un clúster seguro en Azure
> * [Reducir y escalar horizontalmente un clúster](/service-fabric-tutorial-scale-cluster.md)
> * [Implementación de API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale el [SDK de Service Fabric y el módulo de PowerShell](service-fabric-get-started.md).
- Instale la [versión 4.1 o superior del módulo de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Los siguientes procedimientos crean un clúster de Service Fabric de cinco nodos. Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción
En esta guía se usa Azure PowerShell. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.
 
Ejecute el siguiente script para iniciar sesión en su cuenta de Azure y seleccione su suscripción:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un nuevo grupo de recursos para su implementación y asígnele un nombre y una ubicación.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Implementación de la topología de red
A continuación, configure la topología de la red de implementación de API Management y el clúster de Service Fabric. La plantilla de Resource Manager [network.json][network-arm] está configurada para crear una red virtual (VNET) y también una subred y un grupo de seguridad de red (NSG) para Service Fabric, además de una subred y un grupo de seguridad de red para API Management. Obtenga más información sobre redes virtuales, subredes y grupos de seguridad de red [aquí](../virtual-network/virtual-networks-overview.md).

El archivo de parámetros [network.parameters.json][network-parameters-arm] contiene los nombres de las subredes y los grupos de seguridad de red donde se implementan Service Fabric y API Management.  API Management se implementa en el [siguiente tutorial](service-fabric-tutorial-deploy-api-management.md). En esta guía, los valores de los parámetros no deben cambiarse. Las plantillas de Resource Manager para Service Fabric usan estos valores.  Si los valores se modifican aquí, debe modificarlos en las otras plantillas de Resource Manager usadas en este tutorial y el [tutorial de implementación de API Management](service-fabric-tutorial-deploy-api-management.md). 

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para la configuración de la red:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implementación del clúster de Service Fabric
Una vez implementados los recursos de red, el siguiente paso es implementar un clúster de Service Fabric a la red virtual, en la subred y el grupo de seguridad de red designados para el clúster de Service Fabric. La implementación de un clúster en una red virtual y subred (implementada anteriormente en este artículo) existentes requiere una plantilla de Resource Manager.  Para esta serie de tutoriales, la plantilla está preconfigurada para usar los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en un paso anterior.  

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Use esta plantilla para crear un clúster seguro.  Un certificado de clúster es un certificado X.509 que se usa para proteger la comunicación de nodo a nodo y autenticar los puntos de conexión de administración del clúster en un cliente de administración.  El certificado de clúster también proporciona SSL para la API de administración de HTTPS y para Service Fabric Explorer a través de HTTPS. Azure Key Vault se usa para administrar certificados para clústeres de Service Fabric en Azure.  Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster. 

Puede usar un certificado de una entidad de certificación (CA) como el certificado de clúster o, para realizar pruebas, crear un certificado autofirmado. El certificado de clúster debe:

- contener una clave privada.
- crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
- tener un nombre de sujeto que coincida con el dominio que se usa para acceder al clúster de Service Fabric. Esta coincidencia es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio .cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Rellene estos parámetros vacíos en el archivo *cluster.parameters.json* para su implementación:

|Parámetro|Valor|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|
|location|southcentralus|

Deje los parámetros *certificateThumbprint*, *certificateUrlValue* y *sourceVaultValue* en blanco para crear un certificado autofirmado.  Si desea usar un certificado existente cargado previamente en un almacén de claves, rellene esos valores del parámetro.

El script siguiente usa el cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) y la plantilla para implementar un clúster nuevo en Azure. El cmdlet también crea un almacén de claves en Azure, agrega un certificado autofirmado nuevo en el almacén de claves y carga el certificado en el archivo de certificados de forma local. Puede especificar un certificado o un almacén de claves existente si usa otros parámetros del cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster).

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Conexión al clúster seguro
Conéctese al clúster mediante el módulo PowerShell de Service Fabric instalado con el SDK de Service Fabric.  En primer lugar, instale el certificado en el almacén personal (Mi) del usuario actual en el equipo.  Ejecute el siguiente comando de PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Ahora está listo para conectarse a su clúster seguro.

El módulo PowerShell de **Service Fabric** proporciona muchos cmdlets para administrar clústeres, aplicaciones y servicios de Service Fabric.  Use el cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para conectarse al clúster seguro. Los detalles de la huella digital de certificado y del punto de conexión se encuentran en el resultado del paso anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Compruebe que está conectado y que el estado del clúster es correcto mediante el cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpieza de recursos
En el resto de artículos de esta serie de tutoriales se usa el clúster que se acaba de crear. Si no va a pasar inmediatamente al siguiente artículo, puede ser conveniente eliminar el clúster y el almacén de claves para evitar incurrir en cargos. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Elimine el grupo de recursos y todos los recursos de clúster mediante el cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Además, elimine el grupo de recursos que contiene el almacén de claves.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una red virtual en Azure mediante PowerShell
> * Creación de un almacén de claves y carga de un certificado
> * Creación de un clúster de Service Fabric en Azure mediante PowerShell
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

Luego, avance hasta el tutorial siguiente para obtener información sobre cómo escalar el clúster.
> [!div class="nextstepaction"]
> [Escalado de un clúster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
