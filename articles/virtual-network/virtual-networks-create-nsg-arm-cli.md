---
title: "Creación de grupos de seguridad de red: CLI de Azure 2.0 | Microsoft Docs"
description: Aprenda a crear e implementar grupos de seguridad de red mediante la CLI de Azure 2.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8efb3ab66d07875b51f723fed5594bcb477ed025
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-network-security-groups-using-the-azure-cli-20"></a>Creación de grupos de seguridad de red con la CLI de Azure 2.0

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea 

Puede completar la tarea mediante una de las siguientes versiones de la CLI: 

- [CLI de Azure 1.0](virtual-networks-create-nsg-cli-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager 
- [CLI de Azure 2.0](#Create-the-nsg-for-the-front-end-subnet): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo).

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de CLI de Azure 2.0 de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>Creación del grupo de seguridad de red para la subred `FrontEnd`

Para crear un grupo de seguridad de red denominado *NSG-FrontEnd* según el escenario anterior, siga los pasos siguientes.

1. Si todavía no lo ha hecho, instale y configure la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/#login). 

2. Cree un grupo de seguridad de red con el comando [azure network nsg create](/cli/azure/network/nsg#create). 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    Parámetros:
   
   * `--resource-group`: nombre del grupo de recursos donde se crea el grupo de seguridad de red. En este escenario, *TestRG*.
   * `--location`: región de Azure donde se crea el grupo de seguridad de red. En este escenario, *TestRG*.
   * `--name`: nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.

    La salida esperada es una gran cantidad de información, incluida una lista de todas las reglas predeterminadas. En el ejemplo siguiente se muestran las reglas predeterminadas mediante un filtro de consulta JMESPATH con el formato de salida `table`:

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   Salida:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. Cree una regla que permita el acceso al puerto 3389 (RDP) desde Internet con el comando [az network nsg rule create](/cli/azure/network/nsg/rule#create).

    > [!NOTE]
    > Según el shell que esté usando, tendrá que modificar el carácter `*` en los argumentos siguientes para que no se expanda el argumento antes de la ejecución.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    Resultado esperado:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    Parámetros:

    * `--resource-group testrg`: el grupo de recursos que se va a usar. Tenga en cuenta que distingue mayúsculas de minúsculas.
    * `--nsg-name NSG-FrontEnd`: nombre del grupo de seguridad de red en el que se crea la regla.
    * `--name rdp-rule`: nombre de la nueva regla.
    * `--access Allow`: nivel de acceso de la regla (Denegar o Permitir).
    * `--protocol Tcp`: protocolo (TCP, UDP o *).
    * `--direction Inbound`: dirección de conexión (Entrante o Saliente).
    * `--priority 100`: prioridad de la regla.
    * `--source-address-prefix Internet`: prefijo de dirección de origen en CIDR o con las etiquetas predeterminadas.
    * `--source-port-range "*"`: puerto de origen o intervalo de puertos. Puerto que ha abierto la conexión.
    * `--destination-address-prefix "*"`: prefijo de dirección de destino en CIDR o con las etiquetas predeterminadas.
    * `--destination-port-range 3389`: puerto de destino o intervalo de puertos. Puerto que recibe la solicitud de conexión.



4. Cree una regla que permita el acceso al puerto 80 (HTTP) desde Internet con el comando **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    Resultado esperado:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. Enlace el grupo de seguridad de red a la subred **FrontEnd** con el comando [az network vnet subnet update](/cli/azure/network/vnet/subnet#update).
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    Resultado esperado:
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>Creación del grupo de seguridad de red para la subred `BackEnd`
Para crear un grupo de seguridad de red denominado *NSG-BackEnd* según el escenario anterior, siga los pasos siguientes.

1. Cree el grupo de seguridad de red `NSG-BackEnd` con el comando **az network nsg create**.
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    Como en el paso 2 anterior, la salida esperada es bastante grande, incluidas las reglas predeterminadas.
   
2. Cree una regla que permita el acceso al puerto 1433 (SQL) desde la subred `FrontEnd` con el comando **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    Resultado esperado:

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. Cree una regla que deniegue el acceso a Internet mediante el comando **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    Resultado esperado:
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. Vincule el grupo de seguridad de red a la subred `BackEnd` mediante el comando **az network vnet subnet update**.
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    Resultado esperado:
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```
