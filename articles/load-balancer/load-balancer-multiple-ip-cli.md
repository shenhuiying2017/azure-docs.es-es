---
title: Equilibrio de carga en varias configuraciones de IP mediante la CLI de Azure | Microsoft Docs
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con la CLI de Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 4c90cf910af142e8d0cd73a4e6f502a4fb78be9b
ms.lasthandoff: 03/11/2017


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Equilibrio de carga en varias configuraciones de IP

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

En este artículo, se explica cómo se utiliza Azure Load Balancer con varias direcciones IP en una interfaz de red secundaria (NIC). La compatibilidad con varias direcciones IP en una NIC es una característica que, en este momento, está incluida en versión preliminar. Para más información, consulte la sección [Limitaciones](#limitations) de este artículo. En el siguiente escenario se ilustra cómo funciona esta característica con Load Balancer.

En este escenario, tenemos dos máquinas virtuales que ejecutan Windows. Cada una de ellas cuenta con una NIC principal y otra secundaria. Cada una de las NIC secundarias tiene dos configuraciones de IP. Cada una de las máquinas virtuales hospeda dos sitios web: contoso.com y fabrikam.com. Cada uno de los sitios web está enlazado a una de las configuraciones de IP de la NIC secundaria. Usamos Azure Load Balancer para exponer dos direcciones IP front-end, una por cada sitio web, que van a distribuir el tráfico a la configuración de IP correspondiente del sitio web. En este escenario, se utiliza el mismo número de puerto en los dos front-end, así como en las dos direcciones IP del grupo de back-end.

![Imagen del escenario de equilibrio de carga](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Limitaciones

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Regístrese para obtener la versión preliminar ejecutando los siguientes comandos de PowerShell después de iniciar sesión y seleccionar la suscripción adecuada:

```
Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network

Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

No trate de completar los pasos restantes hasta que vea el siguiente resultado cuando ejecute el comando ```Get-AzureRmProviderFeature```:
        
```powershell
FeatureName                            ProviderName      RegistrationState
-----------                            ------------      -----------------      
AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
```
        
>[!NOTE] 
>Esta operación puede tardar unos minutos.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Pasos para equilibrar la carga en varias configuraciones de IP

Siga estos pasos para reproducir el escenario que se describe en este artículo:

1. [Instale y configure la CLI de Azure ](../xplat-cli-install.md) siguiendo los pasos que se describen en el artículo vinculado e inicie sesión en la cuenta de Azure.
2. [Cree un grupo de recursos](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) llamado *contosofabrikam*, tal y como se describe en el artículo vinculado.

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Cree un conjunto de disponibilidad](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) para las dos máquinas virtuales. En este caso, utilice el siguiente comando:

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Cree una red virtual](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) llamada *myVNet* y una subred denominada *mySubnet*:

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [Cree un equilibrador de carga](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools) llamado *mylb*:

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Cree dos direcciones IP públicas dinámicas para las configuraciones de IP de front-end del equilibrador de carga:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Cree las dos configuraciones de IP de front-tend: *contosofe* y *fabrikamfe*, respectivamente:

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Cree los grupos de direcciones del back-end: *contosopool* y *fabrikampool*; un [sondeo](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe):  - *HTTP*, y las reglas de equilibrado de carga: *HTTPc* y *HTTPf*:

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Ejecute los comandos siguientes y consulte la salida para [comprobar que el equilibrador de carga](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer) se creó correctamente:

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Cree una dirección IP pública](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address): *myPublicIp*, y una [cuenta de almacenamiento](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account): *mystorageaccont1*, para la primera máquina virtual (VM1), tal y como se muestra a continuación:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Cree las interfaces de red](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm) para VM1 y agregue una segunda configuración de IP, *VM1-ipconfig2*, y [cree la máquina virtual](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) tal como se describe a continuación:

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Repita los pasos 10 y 11 con la segunda máquina virtual:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Por último, debe configurar los registros de recursos DNS para que apunten a la dirección IP de front-end correspondiente del equilibrador de carga. Puede hospedar los dominios en Azure DNS. Para más información sobre el uso de Azure DNS con Load Balancer, consulte [Uso de Azure DNS con otros servicios de Azure](../dns/dns-for-azure-services.md).

