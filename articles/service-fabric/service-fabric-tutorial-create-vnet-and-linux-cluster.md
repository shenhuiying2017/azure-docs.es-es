---
title: "Creación de un clúster de Service Fabric con Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo implementar un clúster de Service Fabric con Linux en una instancia existente de Azure Virtual Network mediante la CLI de Azure."
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
ms.date: 09/26/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: de67512a9b03095b793fc82f3b0c348577511d5f
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Implementación de un clúster de Service Fabric con Linux en una instancia de Azure Virtual Network
Este tutorial es la primera parte de una serie. Obtendrá información sobre cómo implementar un clúster de Service Fabric con Linux en una instancia existente de Azure Virtual Network (VNET) y en una subred mediante la CLI de Azure. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones. Para crear un clúster con Windows mediante PowerShell, consulte la información sobre la [creación de un clúster con Windows seguro en Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual en Azure mediante la CLI de Azure
> * Creación de un clúster de Service Fabric seguro en Azure mediante la CLI de Azure
> * Protección del clúster con un certificado X.509
> * Conexión al clúster mediante la CLI de Service Fabric
> * Eliminación de un clúster

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Crear un clúster seguro en Azure
> * [Escalado o reducción horizontal](service-fabric-tutorial-scale-cluster.md)
> * [Actualización del sistema de tiempo de ejecución de un clúster](service-fabric-tutorial-upgrade-cluster.md)
> * [Implementación de API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale la [CLI de Service Fabric](service-fabric-cli.md).
- Instale la [CLI de Azure 2.0](/cli/azure/install-azure-cli).

Los siguientes procedimientos crean un clúster de Service Fabric de cinco nodos. Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Introducción
Este tutorial implementa un clúster de cinco nodos en un tipo de nodo único en una red virtual en Azure.

Un [clúster de Service Fabric](service-fabric-deploy-anywhere.md) es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Los clústeres pueden escalar a miles de equipos. Cada una de las máquinas físicas o virtuales que forman parte de un clúster se denominan nodo. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características como las propiedades de colocación.

Un tipo de nodo define el tamaño, el número y las propiedades de un conjunto de máquinas virtuales en el clúster. Cada tipo de nodo definido se configura como un [conjunto de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/), un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Los tipos de nodo se utilizan para definir los roles para un conjunto de nodos de clúster, por ejemplo, "front-end" o "back-end".  El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal debe tener al menos cinco máquinas virtuales para los clústeres de producción (o al menos tres máquinas virtuales en clústeres de prueba).  [Los servicios del sistema de Service Fabric](service-fabric-technical-overview.md#system-services) se colocan en los dos del tipo de nodo principal.

## <a name="cluster-capacity-planning"></a>Planeamiento de la capacidad de clúster
Este tutorial implementa un clúster de cinco nodos en un tipo de nodo único.  En cualquier implementación del clúster de producción, el planeamiento de la capacidad es un paso importante. Esto es algo que hay que tener en cuenta como parte de ese proceso.

- El número de tipos de nodos con que necesita el clúster 
- Propiedades de cada tipo de nodo (por ejemplo, tamaño, principal, accesible desde Internet, número de máquinas virtuales)
- Características de confiabilidad y durabilidad del clúster

Para más información, consulte [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción
En esta guía se usa la CLI de Azure. Al iniciar una nueva sesión, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.
 
Ejecute el siguiente script para iniciar sesión en su cuenta de Azure y seleccione su suscripción:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un nuevo grupo de recursos para su implementación y asígnele un nombre y una ubicación.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Implementación de la topología de red
A continuación, configure la topología de la red de implementación de API Management y el clúster de Service Fabric. La plantilla de Resource Manager [network.json][network-arm] está configurada para crear una red virtual (VNET) y también una subred y un grupo de seguridad de red (NSG) para Service Fabric, además de una subred y un grupo de seguridad de red para API Management. Obtenga más información sobre redes virtuales, subredes y grupos de seguridad de red [aquí](../virtual-network/virtual-networks-overview.md).

El archivo de parámetros [network.parameters.json][network-parameters-arm] contiene los nombres de las subredes y los grupos de seguridad de red donde se implementan Service Fabric y API Management.  API Management se implementa en el [siguiente tutorial](service-fabric-tutorial-deploy-api-management.md). En esta guía, los valores de los parámetros no deben cambiarse. Las plantillas de Resource Manager para Service Fabric usan estos valores.  Si los valores se modifican aquí, debe modificarlos en las otras plantillas de Resource Manager usadas en este tutorial y el [tutorial de implementación de API Management](service-fabric-tutorial-deploy-api-management.md). 

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use el siguiente script para implementar la plantilla de Resource Manager y los archivos de parámetros para la configuración de la red:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Implementación del clúster de Service Fabric
Una vez implementados los recursos de red, el siguiente paso es implementar un clúster de Service Fabric a la red virtual, en la subred y el grupo de seguridad de red designados para el clúster de Service Fabric. La implementación de un clúster en una red virtual y subred (implementada anteriormente en este artículo) existentes requiere una plantilla de Resource Manager.  Para más información, vea [Creación de un clúster con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Para esta serie de tutoriales, la plantilla está preconfigurada para usar los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en un paso anterior.  

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

Use esta plantilla para crear un clúster seguro.  Un certificado de clúster es un certificado X.509 que se usa para proteger la comunicación de nodo a nodo y autenticar los puntos de conexión de administración del clúster en un cliente de administración.  El certificado de clúster también proporciona SSL para la API de administración de HTTPS y para Service Fabric Explorer a través de HTTPS. Azure Key Vault se usa para administrar certificados para clústeres de Service Fabric en Azure.  Cuando un clúster se implementa en Azure, el proveedor de recursos de Azure responsable de crear clústeres de Service Fabric extrae los certificados de Key Vault y los instala en las máquinas virtuales del clúster. 

Puede usar un certificado de una entidad de certificación (CA) como el certificado de clúster o, para realizar pruebas, crear un certificado autofirmado. El certificado de clúster debe:

- contener una clave privada.
- crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
- tener un nombre de sujeto que coincida con el dominio que se usa para acceder al clúster de Service Fabric. Esta coincidencia es un requisito para proporcionar SSL a los puntos de conexión de administración HTTPS y de Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio .cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Rellene estos parámetros vacíos en el archivo *linuxcluster.parameters.json* para su implementación:

|Parámetro|Valor|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|

Deje los parámetros **certificateThumbprint**, **certificateUrlValue** y **sourceVaultValue** en blanco para crear un certificado autofirmado.  Si desea usar un certificado existente cargado previamente en un almacén de claves, rellene esos valores del parámetro.

El script siguiente usa el comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) y la plantilla para implementar un clúster nuevo en Azure. El cmdlet también crea un almacén de claves en Azure, agrega un certificado autofirmado nuevo en el almacén de claves y carga el certificado en el archivo de certificados de forma local. Puede especificar un certificado o un almacén de claves existente si usa otros parámetros del comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create).

```azurecli
Password="q6D7nN%6ck@6"
Subject="mysfcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Conexión al clúster seguro
Conéctese al clúster que usa el comando `sfctl cluster select` de la CLI de Service Fabric que utiliza su clave.  Nota: Use la opción **--no-verify** solo para obtener un certificado autofirmado.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Compruebe que está conectado y que el estado del clúster es correcto mediante el comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpieza de recursos
En el resto de artículos de esta serie de tutoriales se usa el clúster que se acaba de crear. Si no va a pasar inmediatamente al siguiente artículo, puede eliminar el clúster para evitar incurrir en cargos. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Inicie sesión en Azure y seleccione el identificador de suscripción con el que quiere quitar el clúster.  Para encontrar el identificador de suscripción, inicie sesión en [Azure Portal](http://portal.azure.com). Elimine el grupo de recursos y todos los recursos de clúster mediante el comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de una red virtual en Azure mediante la CLI de Azure
> * Creación de un clúster de Service Fabric seguro en Azure mediante la CLI de Azure
> * Protección del clúster con un certificado X.509
> * Conexión al clúster mediante la CLI de Service Fabric
> * Eliminación de un clúster

Luego, avance hasta el tutorial siguiente para obtener información sobre cómo escalar el clúster.
> [!div class="nextstepaction"]
> [Escalado de un clúster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
