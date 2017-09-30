---
title: "Creación de un clúster de Service Fabric en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear un clúster de Linux en Azure mediante una plantilla."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/16/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: aabf3a0767b2f6f96e357dc63f3e8ee737577a1f
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Implementación de un clúster de Linux de Service Fabric seguro en una red virtual de Azure
Este tutorial es la primera parte de una serie. Obtendrá información sobre cómo crear un clúster de Service Fabric (Linux) en ejecución en Azure y cómo implementarlo en una red virtual (VNET) y una subred existentes. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones. Para crear un clúster de Windows, consulte [Create a secure Windows cluster on Azure using a template](service-fabric-tutorial-create-vnet-and-windows-cluster.md) (Creación de un clúster de Windows seguro en Azure mediante una plantilla).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual en Azure mediante una plantilla
> * Creación de un clúster de Service Fabric seguro en Azure mediante una plantilla
> * Protección del clúster con un certificado X.509
> * Conexión al clúster mediante la CLI de Service Fabric
> * Eliminación de un clúster

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Creación de un clúster seguro en Azure mediante una plantilla
> * [Implementación de API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale la [CLI de Service Fabric](service-fabric-cli.md).
- Instale la [CLI de Azure 2.0](/cli/azure/install-azure-cli).

Los siguientes procedimientos crean un clúster de Service Fabric de cinco nodos. El clúster está protegido por un certificado autofirmado colocado en un almacén de claves. 

Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).
Para más información sobre la creación de clústeres de Service Fabric, consulte el artículo [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

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

El archivo de parámetros [network.parameters.json][network-parameters-arm] contiene los nombres de las subredes y los grupos de seguridad de red donde se implementan Service Fabric y API Management.  API Management se implementa en el [siguiente tutorial](service-fabric-tutorial-deploy-api-management.md). En esta guía, los valores de los parámetros no deben cambiarse. Las plantillas de Resource Manager de Service Fabric usan estos valores.  Si los valores se modifican aquí, debe modificarlos en las otras plantillas de Resource Manager usadas en este tutorial y el [tutorial de implementación de API Management](service-fabric-tutorial-deploy-api-management.md). 

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
Una vez implementados los recursos de red, el siguiente paso es implementar un clúster de Service Fabric a la red virtual, en la subred y el grupo de seguridad de red designados para el clúster de Service Fabric. Para esta serie de tutoriales, la plantilla de Resource Manager de Service Fabric está preconfigurada para los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en un paso anterior.

La implementación de un clúster en una red virtual y subred (implementada anteriormente en este artículo) existentes requiere una plantilla de Resource Manager.  Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [linuxcluster.json][cluster-arm]
- [linuxcluster.parameters.json][cluster-parameters-arm]

Rellene los parámetros **clusterName**, **adminUserName** y **adminPassword** vacíos en el archivo `linuxcluster.parameters.json` para su implementación.  Deje los parámetros **certificateThumbprint**, **certificateUrlValue** y **sourceVaultValue** en blanco si desea crear un certificado autofirmado.  Si tiene un certificado existente cargado en un almacén de claves, rellene esos valores del parámetro.

Use el siguiente script para implementar el clúster que usa la plantilla de Resource Manager y los archivos de parámetros.  Un certificado autofirmado se crea en el almacén de claves especificado y se usa para proteger el clúster.  El certificado también se descarga localmente.

```azurecli
Password="q6D7nN%6ck@6"
Subject="aztestcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file portalcluster.json --parameter-file portalcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Conexión al clúster seguro
Conéctese al clúster que usa el comando `sfctl cluster select` de la CLI de Service Fabric que utiliza su clave.  Nota: Use la opción **--no-verify** solo para obtener un certificado autofirmado.

```azurecli
sfctl cluster select --endpoint https://mysfcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Compruebe que está conectado y que el estado del clúster es correcto mediante el comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Un clúster está formado por muchos otros recursos de Azure, además del propio recurso del clúster. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Inicie sesión en Azure y seleccione el identificador de suscripción con el que quiere quitar el clúster.  Para encontrar el identificador de suscripción, inicie sesión en [Azure Portal](http://portal.azure.com). Elimine el grupo de recursos y todos los recursos de clúster mediante el comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de una red virtual en Azure mediante una plantilla
> * Creación de un clúster de Service Fabric seguro en Azure mediante una plantilla
> * Protección del clúster con un certificado X.509
> * Conexión al clúster mediante la CLI de Service Fabric
> * Eliminación de un clúster

A continuación, avance hasta el siguiente tutorial para aprender a implementar una aplicación existente.
> [!div class="nextstepaction"]
> [Implementación de API Management](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json

