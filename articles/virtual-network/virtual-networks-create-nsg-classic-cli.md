---
title: Creación de un grupo de seguridad de red (clásico) mediante la CLI de Azure 1.0 | Microsoft Docs
description: Obtenga más información sobre cómo crear e implementar un grupo de seguridad de red (clásico) mediante la CLI de Azure.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: a9ab1a83366919a2d05da18819ed8167bdadb20a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Creación de un grupo de seguridad de red (clásico) mediante la CLI de Azure 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación clásico. También puede [crear grupos de seguridad de red con el modelo de implementación del Administrador de recursos](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario. Si desea ejecutar los comandos tal y como aparecen en este documento, compile primero el entorno de prueba mediante la [creación de una red virtual](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Creación de un grupo de seguridad de red para la subred front-end

1. Si nunca ha usado la CLI de Azure, consulte [Instalación de la CLI de Azure](../cli-install-nodejs.md).
2. Cambie al modo clásico:

    ```azurecli
    azure config mode asm
    ```   

3. Cree un grupo de seguridad de red:
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Cree una regla de seguridad que permita el acceso al puerto 3389 (RDP) desde Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Cree una regla que permita el acceso al puerto 80 (HTTP) desde Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Asocie el grupo de seguridad de red a la subred front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Creación del grupo de seguridad de red para la subred de back-end

1. Cree el NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Cree una regla que permita el acceso al puerto 1433 (SQL) desde la subred front-end:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Cree una regla que deniegue el acceso a Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Asocie el grupo de seguridad de red a la subred front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```