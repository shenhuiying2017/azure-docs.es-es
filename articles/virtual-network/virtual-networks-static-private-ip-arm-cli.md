---
title: "Configuración de direcciones IP privadas para máquinas virtuales en la CLI de Azure | Microsoft Docs"
description: "Aprenda a configurar direcciones IP privadas para máquinas virtuales mediante la interfaz de la línea de comandos (CLI) de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d9925b29a60fc46e9ecc775ca132bd2365f64b15
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configuración de direcciones IP privadas para una máquina virtual mediante la CLI de Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> En los siguientes comandos de ejemplo de la CLI de Azure se espera un entorno simple existente. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-arm-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Especificación de una dirección IP privada estática al crear una VM

Para crear una máquina virtual denominada *DNS01* en la subred *FrontEnd* de una red virtual denominada *TestVNet* con una dirección IP privada estática de *192.168.1.101*, complete estos pasos:

1. Si todavía no lo ha hecho, instale y configure la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login). 

2. Cree una dirección IP pública para la máquina virtual con el comando [az network public-ip create](/cli/azure/network/public-ip#create). En la lista que se muestra en la salida se explican los parámetros utilizados.

    > [!NOTE]
    > Puede que quiera o necesite usar valores diferentes para los argumentos de este paso y los siguientes, dependiendo de su entorno.
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Resultado esperado:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: nombre del grupo de recursos en el que se creará la dirección IP pública.
   * `--name`: nombre de la dirección IP pública.
   * `--location`: región de Azure donde se va a crear la dirección IP pública.

3. Ejecute el comando [azure network nic create](/cli/azure/network/nic#create) para crear una NIC con una dirección IP privada estática. En la lista que se muestra en la salida se explican los parámetros utilizados. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Resultado esperado:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parámetros:

    * `--private-ip-address`: dirección IP privada estática para la NIC.
    * `--vnet-name`: nombre de la red virtual en la que se va a crear la NIC.
    * `--subnet`: nombre de la subred en la que se va a crear la NIC.

4. Ejecute el comando [azure vm create](/cli/azure/vm/nic#create) para crear la máquina virtual mediante la dirección IP pública y la NIC creadas anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Resultado esperado:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parámetros que no sean las opciones básicas de [az vm create](/cli/azure/vm#create).

   * `--nics`: nombre de la NIC a la que está conectada la máquina virtual.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM

Ejecute el siguiente comando de la CLI de Azure para observar los valores de *Método de asignación de dirección IP privada* y *Dirección IP privada*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Resultado esperado:

```json
"192.168.1.101"
```

Para mostrar la información específica de la dirección IP de la NIC de esa máquina virtual, consulte la NIC específicamente:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

La salida es similar a esta:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM

No se puede eliminar una dirección IP privada estática de una NIC en la CLI de Azure para implementaciones de Azure Resource Manager. Debe:
- Crear una nueva NIC que use una dirección IP dinámica.
- Establecer que la NIC de la máquina virtual sea la NIC recién creada. 

Para cambiar el NIC de la máquina virtual que se utiliza en los comandos anteriores, realice los siguientes pasos:

1. Ejecute el comando **azure network nic create** para crear una nueva NIC mediante asignación de IP dinámica con una dirección IP nueva. Como no se especifica ninguna dirección IP, el método de asignación es **Dynamic**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Resultado esperado:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Ejecuta el comando **azure vm set** para cambiar la NIC que usó la VM.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Resultado esperado:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Si la máquina virtual es suficientemente grande para tener más de una NIC, ejecute el comando **azure network nic delete** para eliminar la NIC antigua.
   
## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de las [direcciones IP públicas reservadas](virtual-networks-reserved-public-ip.md) .
* Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

