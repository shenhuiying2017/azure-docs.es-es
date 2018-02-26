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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 76fcdff6804cd1fa66c846597218d351eb6f4c77
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implementación de un clúster de Windows de Service Fabric en una red virtual de Azure
Este tutorial es la primera parte de una serie. Con él puede aprender a implementar un clúster de Service Fabric con Windows en una instancia de [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md) y en un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) mediante PowerShell y una plantilla. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones.  Para crear un clúster de Linux con la CLI de Azure, consulte [Create a secure Linux cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Creación de un clúster de Linux seguro en Azure).

Este tutorial describe un escenario de producción.  Si desea crear un clúster pequeño con fines de prueba rápidamente, consulte [Creación de un clúster de prueba de tres nodos](./scripts/service-fabric-powershell-create-test-cluster.md).

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
> * [Escalado o reducción horizontal](service-fabric-tutorial-scale-cluster.md)
> * [Actualización del entorno en tiempo de ejecución de un clúster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implementación de API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale el [SDK de Service Fabric y el módulo de PowerShell](service-fabric-get-started.md).
- Instale la [versión 4.1 o superior del módulo de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Los siguientes procedimientos crean un clúster de Service Fabric de cinco nodos. Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Conceptos clave
Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Los clústeres pueden escalar a miles de equipos. Cada una de las máquinas físicas o virtuales que forman parte de un clúster se denominan nodo. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características como las propiedades de colocación.

Un tipo de nodo define el tamaño, el número y las propiedades de un conjunto de máquinas virtuales en el clúster. Cada tipo de nodo definido se configura como un [conjunto de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/), un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Los tipos de nodo se utilizan para definir los roles para un conjunto de nodos de clúster, por ejemplo, "front-end" o "back-end".  El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal debe tener al menos cinco máquinas virtuales para los clústeres de producción (o al menos tres máquinas virtuales en clústeres de prueba).  [Los servicios del sistema de Service Fabric](service-fabric-technical-overview.md#system-services) se colocan en los dos del tipo de nodo principal.

El clúster está protegido mediante un certificado de clúster. Un certificado de clúster es un certificado X.509 que se usa para proteger la comunicación de nodo a nodo y autenticar los puntos de conexión de administración del clúster en un cliente de administración.  El certificado de clúster también proporciona SSL para la API de administración de HTTPS y para Service Fabric Explorer a través de HTTPS. Los certificados autofirmados son útiles para clústeres de prueba.  Para los clústeres de producción, use un certificado de una entidad de certificación (CA) como certificado del clúster.

El certificado de clúster debe:

- contener una clave privada.
- crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
- tener un nombre de sujeto que coincida con el dominio que se usa para acceder al clúster de Service Fabric. Esta coincidencia es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio .cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Azure Key Vault se usa para administrar certificados para clústeres de Service Fabric en Azure.  Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster.

Este tutorial implementa un clúster de cinco nodos en un tipo de nodo único. No obstante, en cualquier implementación del clúster de producción, el [planeamiento de la capacidad](service-fabric-cluster-capacity.md) es un paso importante. Esto es algo que hay que tener en cuenta como parte de ese proceso.

- El número de nodos y de tipos de nodo que necesita el clúster 
- Propiedades de cada tipo de nodo (por ejemplo, tamaño, principal, accesible desde Internet, número de máquinas virtuales)
- Características de confiabilidad y durabilidad del clúster

## <a name="download-and-explore-the-template"></a>Descarga y exploración de la plantilla
Descargue los siguientes archivos de plantilla de Resource Manager:
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

El archivo [vnet-cluster.json][template] permite implementar varios recursos, incluido el siguiente. 

### <a name="service-fabric-cluster"></a>Clúster de Service Fabric
Se implementa un clúster de Windows con las siguientes características:
- un tipo de nodo único 
- cinco nodos en el tipo de nodo principal (configurable en los parámetros de la plantilla)
- Sistema operativo: Windows Server 2016 Datacenter con Containers (configurable en los parámetros de la plantilla)
- protección con certificado (configurable en los parámetros de la plantilla)
- se habilita el [proxy inverso](service-fabric-reverseproxy.md)
- se habilita el [servicio DNS](service-fabric-dnsservice.md)
- [Nivel de durabilidad](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster): Bronze (configurable en los parámetros de plantilla)
- [Nivel de confiabilidad](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster): Silver (configurable en los parámetros de plantilla)
- punto de conexión de la conexión de cliente: 19000 (configurable en los parámetros de la plantilla)
- punto de conexión de la puerta de enlace HTTP: 19080 (configurable en los parámetros de la plantilla)

### <a name="azure-load-balancer"></a>Azure Load Balancer
Se implementa un equilibrador de carga, y se configuran sondeos y reglas para los siguientes puertos:
- punto de conexión de la conexión de cliente: 19000
- punto de conexión de la puerta de enlace HTTP: 19080 
- puerto de la aplicación: 80
- puerto de la aplicación: 443
- Proxy inverso de Service Fabric: 19081

Si se necesitan otros puertos de aplicación, debe ajustar el recurso Microsoft.Network/loadBalancers y el recurso Microsoft.Network/networkSecurityGroups para permitir el tráfico de entrada.

### <a name="virtual-network-subnet-and-network-security-group"></a>Red virtual, subred y grupo de seguridad de red
Los nombres de la red virtual, la subred y el grupo de seguridad de red se declaran en los parámetros de la plantilla.  Los espacios de direcciones de la red virtual y de la subred también se declaran en los parámetros de la plantilla:
- espacio de direcciones de red virtual: 172.16.0.0/20
- espacio de direcciones de subred de Service Fabric: 172.16.2.0/23

Se habilitan las siguientes reglas de tráfico de entrada en el grupo de seguridad de red. Puede cambiar los valores de puerto cambiando las variables de la plantilla.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB: 445
- Comunicación entre nodos: 1025, 1026, 1027
- Intervalo de puertos efímero: 49152 a 65534 (es necesario un mínimo de 256 puertos)
- Puertos para el uso de las aplicaciones: 80 y 443
- Intervalo de puertos de la aplicación: 49152 a 65534 (se usa para la comunicación de servicio a servicio y, a diferencia de otros, no se abre en Load Balancer)
- Bloqueo de todos los puertos restantes

Si se necesitan otros puertos de aplicación, debe ajustar el recurso Microsoft.Network/loadBalancers y el recurso Microsoft.Network/networkSecurityGroups para permitir el tráfico de entrada.

## <a name="set-template-parameters"></a>Configuración de los parámetros de plantilla
El archivo de parámetros [vnet-cluster.parameters.json][parameters] permite declarar muchos valores que se utilizan para implementar el clúster y los recursos asociados. Estos son algunos de los parámetros que debe modificar para su implementación:

|.|Valor de ejemplo|Notas|
|---|---||
|adminUserName|vmadmin| Nombre de usuario del administrador de las máquinas virtuales del clúster. |
|adminPassword|Password#1234| Contraseña del administrador de las máquinas virtuales del clúster.|
|clusterName|mysfcluster123| Nombre del clúster. |
|location|southcentralus| Ubicación del clúster. |
|certificateThumbprint|| <p>El valor debe estar vacío si se va a crear un certificado autofirmado o a proporcionar un archivo de certificados.</p><p>Para usar un certificado existente cargado previamente en un almacén de claves, rellene el valor de huella digital del certificado. Por ejemplo, "6190390162C988701DB5676EB81083EA608DCCF3"</p>. | 
|certificateUrlValue|| <p>El valor debe estar vacío si se va a crear un certificado autofirmado o a proporcionar un archivo de certificados. </p><p>Para usar un certificado existente cargado previamente en un almacén de claves, especifique la dirección URL del certificado. Por ejemplo, "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>El valor debe estar vacío si se va a crear un certificado autofirmado o a proporcionar un archivo de certificados.</p><p>Para usar un certificado existente cargado previamente en un almacén de claves, especifique el valor del almacén de claves de origen. Por ejemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implementación de la red virtual y el clúster
A continuación, configure la topología de red e implemente el clúster de Service Fabric. La plantilla de Resource Manager [vnet-cluster.json][template] permite crear una red virtual (VNET) y también una subred y un grupo de seguridad de red (NSG) para Service Fabric. La plantilla permite también implementar un clúster con seguridad mediante certificados habilitada.  Para los clústeres de producción, use un certificado de una entidad de certificación (CA) como certificado del clúster. Un certificado autofirmado se puede usar para proteger los clústeres de prueba.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Creación de un clúster mediante un certificado existente
El script siguiente usa el cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) y una plantilla para implementar un clúster nuevo en Azure. El cmdlet también crea un nuevo almacén de claves de Azure y carga el certificado. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Creación de un clúster mediante un nuevo certificado autofirmado
El script siguiente usa el cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) y una plantilla para implementar un clúster nuevo en Azure. El cmdlet también crea un almacén de claves en Azure, agrega un certificado autofirmado nuevo en el almacén de claves y carga el certificado en el archivo de certificados de forma local. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
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
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
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
En el resto de artículos de esta serie de tutoriales se usa el clúster que creó anteriormente. Si no va a pasar inmediatamente al siguiente artículo, puede ser conveniente eliminar el clúster y el almacén de claves para evitar incurrir en cargos. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Elimine el grupo de recursos y todos los recursos de clúster mediante el cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Además, elimine el grupo de recursos que contiene el almacén de claves.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente:

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


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
